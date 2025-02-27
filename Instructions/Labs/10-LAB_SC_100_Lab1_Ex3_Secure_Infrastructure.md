# 보안 인프라

Contoso Ltd.는 최근 Tailwind Traders를 인수했으며, 여전히 스토리지에 로컬 파일 서버를 사용합니다. Contoso Ltd.의 사이버 보안 설계자는 기존 클라우드 환경을 이용하여 이러한 파일 서버를 보호하는 솔루션을 평가하려고 합니다. Tailwind Traders는 POC 구현에 사용할 수 있는 테스트 서버(랩 VM 2)를 제공했습니다. 이 연습에서는 Azure Arc를 사용하여 서버를 설정하고 클라우드 인프라 및 보안 환경에 통합하고 서버 로그를 클라우드용 Defender로 보냅니다.

## 1부: 솔루션 설계(필수)

이 작업에서는 클라우드 인프라 내에서 온-프레미스 환경을 보호하는 개념을 설계합니다.

### 디자인 접근 방법

초기 단계에서는 설명된 시나리오에 따라 요구 사항을 분석하고, 목표를 이해하고, 요구 사항을 정의합니다.

제공된 사용 사례에 따라 다음 요구 사항을 설명할 수 있습니다.

- 구독에서 클라우드용 Defender를 사용합니다.
- 온-프레미스 서버를 보호해야 합니다.
- Contoso의 SIEM 솔루션에서 처리할 수 있는 로그를 저장해야 합니다.
- 배포 리소스의 준수 상태 평가

두 번째 단계에서는 Contoso Ltd.의 기존 환경을 검사합니다. 클라우드용 Defender는 구성 및 배포를 개선하는 단계를 식별하여 클라우드 및 온-프레미스 리소스를 보호하기 위한 권장 사항을 제공합니다. 워크로드를 적극적으로 모니터링하여 전반적인 보안 태세를 향상시키고 위협에 대한 노출을 줄입니다.

### 제안된 솔루션

|요건|솔루션|작업 플랜:|
|----|----|----|
|구독에서 클라우드용 Defender를 사용합니다.| Defender for Cloud | 클라우드용 Defender에서 Defender 계획을 활성화합니다. |
|온-프레미스 서버를 보호해야 합니다. | Azure Arc | 온-프레미스 서버를 클라우드 환경으로 온보딩합니다. |
|Contoso의 SIEM 솔루션에서 처리할 수 있는 로그를 저장해야 합니다. |클라우드용 Defender, DataCollectionRules, AzureMonitoring Agent, Log Analytics 작업 영역 | Contoso의 온-프레미스 서버에서 로그를 수집하는 데이터 수집 규칙을 만듭니다. |
|배포 리소스의 준수 상태 평가 | 클라우드용 Defender의 보안 정책| NIST SP 800-53 Rev.5 규정 준수를 사용하도록 설정하고 준수 상태를 평가합니다.|

## 파트 2: 솔루션 구현(선택 사항)

### 작업 1: Log Analytics 작업 영역 만들기

이 작업에서는 다른 리소스에서 전송되는 데이터를 보관하는 데 필요한 Log Analytics 작업 영역을 만듭니다.

1. **lon-sc1\admin** 계정으로 클라이언트 1 VM(LON-SC1)에 로그인합니다. 암호는 랩 호스팅 공급자가 제공합니다.
1. **Microsoft Edge**를 열고 주소 표시줄을 선택하고 **`https://portal.azure.com`**(으)로 이동하고 Azure Porta에 **User1-*******@LODSUATMCA.onmicrosoft.com**(으)로 로그인합니다(여기서 ******은(는) 랩 호스팅 공급자가 제공하는 고유한 테넌트 ID임).  암호는 랩 호스팅 공급자가 제공합니다.
1. 로그인 상태를 유지하시겠습니까? 대화 상자에서 이 메시지를 다시 표시 안 함 체크박스를 선택하고 **아니요**를 선택합니다.
1. 브라우저에 기본 전역 관리자 자격 증명을 저장하지 않으려면 안 함을 선택하여 하단에서 암호 저장 대화 상자를 닫습니다.
1. Microsoft Azure 시작 화면에서 취소를 선택합니다.
1. **리소스 만들기**를 선택하고 **Log Analytics 작업 영역**을 검색합니다.
1. **Log Analytics 작업 영역 타일**을 찾아 **만들기**를 선택합니다.
1. Log Analytics 작업 영역 만들기 사이트에서 새 **리소스 그룹**을 만들고 **`ContosoRG`**(으)로 이름을 지정합니다.
1. 인스턴스 세부 정보에서 이름을 **`ContosoLA`**(으)로 입력하고 지역에서 **미국 동부**를 선택합니다.
1. **검토 및 만들기**를 선택합니다.
1. **만들기**를 선택하여 배포를 시작합니다.

Log Analytics 작업 영역을 성공적으로 만들었습니다.

### 작업 2: 클라우드용 Defender 사용

클라우드용 Defender가 자산을 보호하려면 보호하려는 리소스 유형에 대해 Defender 계획을 사용하도록 설정해야 합니다.

1. 아직 Azure Portal **https://portal.azure.com**에 로그인한 상태여야 합니다.
1. **클라우드용 Microsoft Defender**를 검색하여 선택합니다.
1. 왼쪽 탐색 창의 **관리**에서 **환경 설정**을 선택합니다.
1. **모두 확장**을 선택하고 구독을 선택합니다
1. 구독이 **등록되지 않은 것**으로 표시되면 페이지를 다시 로드합니다.
1. 구독 옆에 있는 줄임표(...)를 선택하고 **설정 편집**을 선택합니다.
1. **클라우드 워크로드 보호**에서 **서버**는 오른쪽의 슬라이더를 **켜기**로 계획합니다.
1. 페이지 맨 위에서 **저장**을 선택합니다.

서버 계획을 사용하도록 설정하면 클라우드용 Defender가 더 많은 리소스 유형을 지원하는 것을 볼 수 있습니다.

### 작업 3: Azure Arc에서 온-프레미스 서버 사용

Azure Arc는 클라우드용 Defender가 사용하는 Log Analytics 작업 영역으로 데이터를 보내는 데 사용합니다.

1. VM **LON-SC2**로 전환하고 Azure Portal **`https://portal.azure.com`** 에 로그인합니다.
1. **`Azure Arc`**(을)를 검색하여 엽니다.
1. 왼쪽의 탐색 창에서 **Azure Arc 리소스**를 확장하고 **머신**을 선택합니다.
1. **추가/만들기** > **머신 추가**를 선택합니다.
1. 단일 서버 추가에서 **스크립트 생성**을 선택합니다.
1. 리소스 그룹 필드에서 드롭다운 메뉴를 사용하여 **ContosoRG**를 선택합니다.
1. 지역 필드에서 드롭다운 메뉴를 사용하여 **미국 동부**를 선택합니다.
1. **스크립트 다운로드 및 실행**을 선택합니다.
1. **다운로드**를 선택하고 두 번째 랩 클라이언트 **LON-SC2**에서 스크립트를 실행하여 온프레미스 서버를 Azure에 온보딩합니다.
1. 관리자 권한으로 Windows PowerShell 열기 명령 이렇게 하려면 창의 오른쪽 아래 모서리에 있는 Windows 아이콘을 마우스 오른쪽 단추로 선택하고 **Windows PowerShell(관리자)** 을 선택합니다.
1. 실행 정책을 무제한으로 설정합니다.

    ```Powershell
    Set-ExecutionPolicy -ExecutionPolicy unrestricted
    ```

1. PowerShell 창에서 Y를 선택합니다.
1. 온보딩 스크립트를 합니다. 이를 수행하려면 파일 탐색기를 선택합니다. 서버 VM의 로컬 C 드라이브에 있는 다운로드 폴더로 이동해야 합니다. 마우스 오른쪽 단추로 **OnboardingScript** 파일을 선택하고 ****PowerShell**로 실행을 선택합니다.
1. 인증 팝업이 나타나면 Azure Portal에 사용 중인 동일한 계정으로 로그인합니다.
1. 스크립트가 성공적으로 완료될 때까지 기다립니다.
1. LON-SC1로 돌아가서 Azure Arc를 엽니다.
1. **머신**을 선택하고 페이지 맨 위에서 **새로 고침**을 선택한 다음 서버가 Azure Arc에 성공적으로 배포되었는지 확인합니다.

테스트 서버에서 Azure Arc를 사용하도록 설정했으므로 데이터가 Log Analytics 작업 영역으로 흐르기 시작해야 합니다. 이 프로세스는 대시보드에서 결과를 볼 수 있을 때까지 다소 시간이 걸릴 수 있습니다.

### 작업 4: 클라우드용 Defender에 서버 추가 및 로그 수집

데이터 수집 규칙을 배포하여 온 프레미스 서버에서 이벤트 로그를 가져옵니다. 규칙은 서버에 모니터링 에이전트를 자동으로 배포하고 로그를 이전에 만든 Log Analytics 작업 영역으로 전달합니다.

1. 아직 Azure Portal **https://portal.azure.com**에 로그인한 상태여야 합니다.
1. 클라우드용 Defender를 엽니다.
1. 왼쪽 탐색 창의 **관리**에서 **환경 설정**을 선택합니다.
1. **모두 펼치기**를 선택합니다.
1. 이전에 만든 Log Analytics 작업 영역인 **ContosoLA**가 표시됩니다.  
1. **ContosoLA**의 줄임표(...)를 선택한 다음, **설정 편집**을 선택합니다.
1. **서버** 계획은 오른쪽의 슬라이더를 **켜기**로 설정합니다.
1. 페이지 맨 위에서 **저장**을 선택합니다.
1. 맨 위에 있는 검색 창을 사용하여 **데이터 수집 규칙**을 검색한 다음 검색 결과에서 선택합니다.
1. **만들기**를 실행합니다.
1. - 규칙 이름: **`ContosoDCR`**
   - 리소스 그룹: **ContosoRG**
1. **다음: 리소스**를 선택합니다.
1. **리소스 추가**를 선택합니다. 리소스 그룹의 범위를 확장합니다. 이전에 온보딩된 Azure Arc 머신을 확인하고 **적용**을 선택합니다.
1. **다음: 수집 및 제공**을 선택합니다.
1. **데이터 원본 추가**를 선택합니다.
1. 데이터 원본 유형 **Windows 이벤트 로그**를 선택합니다.
1. **수집할 이벤트 로그 및 수준 구성**에서 모든 옵션을 선택합니다.
1. **다음: 대상**을 선택합니다.
1. **대상 추가**를 선택합니다.
   - 대상 유형: **Azure Monitor Logs**
   - 계정 또는 네임스페이스: **ContosoLA**
1. **데이터 원본 추가**를 선택합니다.
1. **검토 및 만들기**를 선택합니다.
1. **만들기**를 실행합니다.

리소스가 클라우드용 Defender에 완전히 온보딩될 때까지 수 시간이 걸릴 수 있습니다. 다음 단계는 클라우드용 Defender가 이 리소스에 대해 생성하는 권장 사항을 살펴보는 것입니다.

### 작업 5: 규정 준수 표준 추가

권장 사항에 따라 리소스 보호 및 보안 정책(예: NIST SP 800-53 Rev.5) 할당을 시작하여 Tailwind Traders의 리소스가 조직의 규정을 준수하도록 할 수 있습니다.

1. 아직 Azure Portal **https://portal.azure.com**에 로그인한 상태여야 합니다.
1. 클라우드용 Defender 열고 **관리**를 확장하고 **환경 설정**을 선택합니다.
1. **모두 펼치기**를 선택합니다.
1. 구독 옆에 있는 줄임표(...)를 선택하고 **설정 편집**을 선택합니다.
1. 왼쪽 탐색 메뉴에서 **보안 정책**을 선택합니다. 목록을 로드하는 데 시간이 조금 걸릴 수 있습니다.
1. **`NIST SP 800-53 Rev. 5`** 를 검색합니다. 상태 슬라이더를 **켜기**로 변경합니다.
1. 클라우드용 Defender로 돌아가서 **클라우드 보안**을 확장한 다음 **규정 준수**를 선택합니다.

랩 환경의 제한으로 인해 리소스와 규정 준수 권장 사항은 표시되지 않습니다. 배포된 리소스가 클라우드용 Defender에 표시될 때까지는 시간이 걸립니다.

규정 준수 대시보드에서 이제 대시보드에 표시되는 실패한 평가를 검토하여 권장 사항의 세부 정보를 이해할 수 있습니다.

클라우드용 Defender는 해당 규정에 대한 리소스를 지속적으로 평가하여 특정 규정 준수 인증을 달성하는 데 방해가 될 수 있는 문제를 식별합니다. 규정 준수 유지는 조직의 데이터를 보호하고 안전한 클라우드 환경을 보장하는 데 매우 중요합니다.
