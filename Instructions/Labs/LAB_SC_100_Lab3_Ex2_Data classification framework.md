---
lab: 3
title: 연습 2 - 데이터 분류 프레임워크
---


# 랩 3 - 연습 2 - 데이터 분류 프레임워크

ISO-27001:2022 감사에 대비하여 Contoso Ltd.의 데이터 분류 구조화 작업이 할당되었습니다. 목표는 유출, 삭제 및 손실에 대한 효과적인 데이터 보호를 보장하는 데 중요한 강력한 프레임워크를 설정하는 것입니다. 사용자의 역할에는 회사 내 건설 프로젝트에 대한 새 프로젝트 ID 시스템을 통합하는 작업이 포함됩니다. 정부 규정을 준수하려면 특정 프로젝트 ID를 포함하는 모든 문서를 5년 동안 보관해야 합니다.

프로젝트 ID를 분류하는 다음 예제가 제공되었습니다.

|프로젝트 ID|
|----|
|PAR-1023-DA|
|BER-0822-AB|
|Rom-0419-bm|
|sTr*1223-Se|
|BaR#0418-ag|
|dui0522-in|

## 1부: 솔루션 설계(필수)

이 작업에서는 Contoso Ltd.가 직면한 문제를 해결하기 위한 개념을 디자인합니다.

### 디자인 접근 방법

이렇게 새 프로젝트 ID를 도입하려면 정규식을 통합하여 사용자 지정 패턴을 개발해야 하는 SIT(중요한 정보 유형)를 생성해야 합니다. 그런 다음, 이 SIT를 사용하여 보존 레이블 및 관련 자동 레이블 지정 정책을 고안할 수 있습니다.

### 제안된 솔루션

|요건|솔루션|작업 플랜:|
|----|----|----|
|프로젝트 ID가 포함된 문서 식별|Microsoft Purview 정보 보호|사용자 지정 중요한 정보 유형 만들기|
|5년 동안 데이터를 보존해야 하는 정부 규정 준수| Microsoft Purview 데이터 수명 주기 관리|보존 정책 배포|

## 파트 2: 솔루션 구현(선택 사항)

### 작업 1 - 사용자 지정 중요한 정보 유형 만들기

프로젝트 ID가 포함된 문서를 검색하는 사용자 지정 중요한 정보 유형을 만듭니다.

1. Microsoft Purview 규정 준수 포털 **https://compliance.microsoft.com/** 에 Allan Deyoung의 관리자 계정 **MOD Administrator**를 사용하여 로그인합니다.
1. Microsoft Purview 규정 준수 포털의 왼쪽 탐색 창에서 **데이터 분류**를 확장한 다음 **분류자**를 선택합니다.
3. **분류자** 페이지에서 **중요한 정보 유형** > **중요한 정보 유형 만들기**를 선택합니다.
4. **중요한 정보 유형 이름 지정** 페이지에서 다음 정보를 입력합니다.
    - **이름**: 프로젝트 ID 번호
    - **설명**: 프로젝트 ID 번호 식별
1. **다음**을 선택합니다.
1. **이 중요한 정보 유형에 대한 패턴 정의** 페이지에서 **+ 패턴 만들기**를 선택합니다.
1. **새 패턴** 페이지에서 **기본 요소 추가**를 선택한 다음 **정규식**을 선택합니다.
1. **정규식 추가 페이지**의 **ID** 텍스트 상자에서 **ProjectID**를 입력합니다.
1. **정규식** 텍스트 상자에는 다음 식을 입력합니다.

```
[a-zA-Z]{3}(\W)?[\d]{4}(\W)?[a-zA-Z]{2}
```

> [!NOTE] 제공된 정규식은 세 개의 문자, 그 다음에는 잠재적으로 선택적인 비단어 문자, 4자리 숫자, 선택적 비단어 문자, 그리고 마지막으로 두 문자로 특징지어지는 시퀀스를 식별하기 위해 작성되었습니다. 단어가 아닌 문자의 존재는 임의이며, 가장 중요한 패턴은 데이터 내의 특정 형식 또는 구조와 관련이 있습니다.

1. **정규식**에 대한 **문자열 일치**를 선택하고 **완료**를 선택합니다.
1. 상단에 있는 **신뢰 수준**에서 **높은 신뢰도**를 선택하고 **만들기**를 선택합니다.
1. 구성을 완료하고 중요한 정보 유형을 만듭니다.

프로젝트 ID를 식별하는 새 중요한 정보 유형을 성공적으로 만들었습니다.

### 작업 2 - 보존 레이블 만들기

5년 동안 건설 프로젝트와 관련된 모든 문서를 보관하는 보존 레이블을 만듭니다.

1. Microsoft Purview 규정 준수 포털 **https://compliance.microsoft.com/** 에 계속 로그인한 상태여야 합니다.
1. Microsoft Purview 규정 준수 포털의 왼쪽 탐색 창에서 **데이터 수명 주기 관리**를 확장한 다음 **Microsoft 365**를 선택합니다.
1. **데이터 수명 주기 관리** 페이지에서 **레이블** 블레이드를 선택합니다.
1. **레이블** 블레이드에서 **레이블 만들기**를 선택합니다.
1. **보존 정책 이름 지정** 페이지에서 다음 정보를 입력합니다.

    - **이름**: 건설 프로젝트 설명서 보존
    - **사용자에 대한 설명**: 건설 프로젝트 설명서 보존 정책은 프로젝트 완료 후 5년 동안 모든 프로젝트 관련 문서의 보존을 규정합니다.
    - **관리자에 대한 설명**: 이 레이블은 5년 동안 건설 프로젝트 문서를 보존하기 위해 적용되며 자동 레이블 지정과 함께 사용됩니다.

1. **다음**을 선택합니다.
1. **레이블 설정 정의** 페이지에서 **영구 또는 특정 기간 동안 항목 보관**을 선택한 다음, **다음**을 선택합니다.
1. **보존 기간 정의** 페이지에서 다음 정보를 입력합니다.

    - **항목 보존 기간**: 5년
    - **보존 기간 시작 기준**: 항목 작성 시

1. **다음**을 선택합니다.
1. **보존 기간 이후에 수행되는 작업 선택** 페이지에서 **보존 설정 비활성화**를 선택합니다.
1. 구성을 완료한 다음 게시하지 않고 보존 레이블을 만듭니다.

보존 기간이 5년인 보존 레이블을 만들었습니다.

### 작업 3 - 자동 적용 보존 레이블

이 연습에서 만든 중요한 정보 유형을 사용하여 보존 레이블을 자동으로 적용합니다.

1. Microsoft Purview 규정 준수 포털 **https://compliance.microsoft.com/** 에 계속 로그인한 상태여야 합니다.
1. Microsoft Purview규정 준수 포털에서 **데이터 수명 주기 관리**를 확장하고 **Microsoft 365**를 선택합니다.
1. **데이터 수명 주기 관리** 창에서 **레이블 정책** 블레이드를 선택합니다.
1. **레이블 정책** 블레이드에서 **레이블 자동 적용**을 선택합니다.
1. **시작** 페이지에서 다음 정보를 입력합니다.

    - **이름**: 건설 프로젝트와 관련된 문서에 레이블 지정
    - **설명**: 이 정책은 건설 프로젝트와 관련된 모든 문서에 "건설 프로젝트 문서 보존" 정책을 자동으로 적용합니다.

1. **다음**을 선택합니다.
1. **이 레이블을 적용할 콘텐츠 유형 선택** 페이지에서 **중요한 정보가 포함된 콘텐츠에 레이블 적용**을 선택한 후 **다음**을 선택합니다.
1. **중요한 정보가 포함된 콘텐츠** 페이지에서 **사용자 지정**을 선택한 다음 **사용자 지정 정책**을 선택하고 **다음**을 선택합니다.
**중요한 정보가 포함된 콘텐츠 정의**에서 **프로젝트 ID 번호**에 만든 사용자 지정 중요한 정보 유형을 추가합니다. 다음 설정을 지정합니다.

    - **그룹 이름**: 프로젝트 ID 조회
    - **신뢰 수준**: 높은 신뢰도
    - **인스턴스 수**: 1~모든

1. **다음**을 선택합니다.
1. **정책 범위** 페이지에서 **다음**을 선택합니다.
1. **만들 보존 정책 유형 선택** 페이지에서 **정적**을 선택한 후 **다음**을 선택합니다.
1. 사용 가능한 모든 에 레이블을 적용합니다.
1. **자동으로 적용할 레이블 선택**에서 **레이블 추가**를 선택한 다음 작업 2에서 만든 **건설 프로젝트 문서 보존** 레이블을 선택하고 **추가**를 선택합니다.
1. 구성을 활성화하고 완료하여 자동 레이블 지정 정책을 만듭니다.

프로젝트 ID가 포함된 모든 문서에 보존 레이블을 성공적으로 게시하고 자동으로 적용했습니다.