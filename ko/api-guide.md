## Security > Server Security Check > API 가이드

Server Security Check Public API를 설명 합니다.

## 공통 준비 사항

API 사용을 위해서는 API 엔드포인트와 토큰이 필요합니다.

### API 엔드포인트

| 리전 | 엔드포인트 |
| --- | ----- |
| 모든 리전 | [https://kr1-server-security-check.api.nhncloudservice.com](https://kr1-server-security-check.api.nhncloudservice.com/) |

### 인증 토큰 발급

Server Security Check는 API 인증/인가를 받기 위해 NHN Cloud 토큰을 이용합니다.
[NHN Cloud API 호출 및 인증](https://docs.nhncloud.com/ko/nhncloud/ko/public-api/api-authentication/)을 확인하셔서 인증 토큰 사용에 필요한 정보를 확인합니다.

## API 사용 공통 정보

### API 요청 공통 정보

API를 사용하려면 다음과 같은 정보가 필요합니다.

* 토큰 발급 이후에 API 헤더에 토큰 정보를 포함시킵니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| x-nhn-authorization | Header | String | O | 토큰 |

* 서비스 Appkey
    * Server Security Check 콘솔 상단 **URL \& Appkey** 메뉴에서 확인하거나 프로젝트 관리의 **이용 중인 서비스**에서 확인할 수 있습니다.
    * 서비스 URL Path에 Appkey 가 포함됩니다.

### API 응답 공통 정보

* API 요청에 대한 응답으로 아래와 같이 응답 코드를 반환 할 수 있습니다.
    * **200 OK**
    * **400 Bad Reuqest**
    * **404 Not Found**
    * <strong>413</strong> **Payload Too Large**
    * **405 Method Not Allowed**
    * **500 Internal Server Error**
* 모든 응답 코드는 공통의 response body를 포함합니다.
    * 공통 response body

| 이름 | 종류 | 형식 | 설명 |
| --- | --- | --- | --- |
| header | Body | Object |  |
| header.isSuccessful | Body | Boolean | true: 정상<br>false: 오류 |
| header.resultCode | Body | Integer | 0: 정상<br>그 외: 오류 |
| header.resultMessage | Body | String | "SUCCESS": 정상<br>그 외: 오류 원인 메시지 |

* <span style="color:rgb(49, 51, 56);">공통 response body 외 자세한 응답 결과는 응답 본문 헤더를 참고합니다.</span>

> [주의] API 응답에 가이드에 명시되지 않은 필드가 나타날 수 있습니다. 이런 필드는 NHN Cloud 내부 용도로 사용되며 사전 공지 없이 변경될 수 있으므로 사용하지 않습니다.

## Server Security Check

### 점검 결과 요약 조회

원하는 기간의 점검 결과를 요약 조회합니다.
(조회 기간 최대는 한달입니다.)

```
GET "/ssc/v1.0/appKey/{appKey}/inspection_result/summary"
x-nhn-authorization: {token-id}
```

<br>
##### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL Path | String | O | 서비스 Appkey |
| regionCode | Query | String | O | 리전 정보 (KR1, KR2, ...) |
| language | Query | String | O | KO, EN, JA (default : KO) |
| from | Query | <span style="color:rgb(49, 51, 56);">DateTime</span> | O | 검색 시작 시간<span style="color:rgb(49, 51, 56);">(</span><span style="color:oklch(0.3039 0.04 213.68);">YYYY-MM-DDTHH:mm:ss±hh:mm</span><span style="color:rgb(49, 51, 56);">)</span><br>ex: 2025-06-17T00:00:00%2B09:00 |
| to | Query | <span style="color:rgb(49, 51, 56);">DateTime</span> | O | 검색 종료 시간<span style="color:rgb(49, 51, 56);">(</span><span style="color:oklch(0.3039 0.04 213.68);">YYYY-MM-DDTHH:mm:ss±hh:mm</span><span style="color:rgb(49, 51, 56);">)</span><br>ex: 2025-06-17T23:59:59%2B09:00 |
| page | Query | Integer | O | 조회할 페이지 번호 (default: 1) |
| limit | Query | Integer | O | 조회할 페이지 크기 (default: 10, max: 1000) |
| kind | Query | ENUM | X | 점검 인스턴스 os<br>(Window, Linux) |
| bss | Query | ENUM | X | 점검 기준("M": "주요정보통신기반시설", "F": 전자금융기반시설) |

##### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| usageStasNo | Body | String | O | 점검 결과 sn |
| instanceName | Body | String | O | 점검 인스턴스 이름 |
| os | Body | ENUM | O | 점검 인스턴스 os<br>(Window, Linux) |
| systemVersion | Body | String | O | 점검 인스턴스 os 버전 |
| bss | Body | ENUM | O | 점검 기준("M": "주요정보통신기반시설", "F": 전자금융기반시설) |
| scriptVersion | Body | String | O | 점검 스크립트 버전 |
| executionTime | Body | DateTime | O | 점검 실행 시간 |
| checkCount | Body | Integer | O | 점검 개수 |
| weakCount | Body | Integer | O | 취약점 개수 |
| level3WeakCount | Body | Integer | O | 취약점 레벨 상 |
| level2WeakCount | Body | Integer | O | 취약점 레벨 중 |
| level1WeakCount | Body | Integer | O | 취약점 레벨 하 |

<details>
  <summary><span>예시</span></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "success": true
    },
    "results": [
        {
            "usageStasNo": 1,
            "instanceName": "test-ubuntu-1",
            "os": "Linux",
            "kind": "OS",
            "systemVersion": "ubuntu Server 22.04 LTS",
            "bss": "M",
            "scriptVersion": "G-1",
            "executionTime": "2025-07-17T11:42:20+09:00",
            "checkCount": 65,
            "weakCount": 15,
            "level3WeakCount": 5,
            "level2WeakCount": 5,
            "level1WeakCount": 5
        },
        {
            "usageStasNo": 2,
            "instanceName": "test-ubuntu-2",
            "os": "Linux",
            "kind": "OS",
            "systemVersion": "ubuntu Server 22.04 LTS",
            "bss": "M",
            "scriptVersion": "G-1",
            "executionTime": "2025-07-16T15:11:23+09:00",
            "checkCount": 65,
            "weakCount": 15,
            "level3WeakCount": 5,
            "level2WeakCount": 5,
            "level1WeakCount": 5
        }
    ],
    "page": {
        "itemPerPage": 20,
        "page": 1,
        "totalCount": 2
    }
}
```

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
</details>

### 점검 결과 상세 조회

점검 결과 요약 조회 후 점검 결과 번호로 특정 점검 결과를 상세 조회합니다.

```
GET "/ssc/v1.0/appKey/{appKey}/inspection_result/details/{usageStasNo}"
x-nhn-authorization: {token-id}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| appKey | URL | String | O | 서비스 Appkey |
| usageStasNo | URL | Integer | O | 점검 결과 번호 |
| language | Query | String | O | KO, EN, JA (default : KO) |

#### 응답

| 이름 | 종류 | 형식 | 필수 | 설명 |
| --- | --- | --- | --- | --- |
| categoryName | Body | String | O | 점검 분류 |
| resultId | Body | String | O | 분석 결과 id |
| weakLevel | Body | ENUM | O | 취약 레벨("H", "M", "L") |
| weakLevelName | Body | String | O | 취약점 enum 이름 |
| resultCode | Body | String | O | 점검 주기 설정 |
| itemName | Body | String | O | 항목명 |
| manageMethod | Body | String | O | 대응 방안 |

<details>
  <summary><span>예시</span></summary>

```json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS",
        "success": true
    },
    "results": [
        {
            "categoryName": "1. 계정관리",
            "resultId": "U-01",
            "weakLevel": "H",
            "resultCode": "X",
            "weakLevelName": "상",
            "itemName": "root 계정 원격 접속 제한",
            "manageMethod": "1. \"/etc/securetty\" 파일에서 pts/0 ~ pts/x 설정 제거 또는 주석 처리<br>2. \"/etc/pam.d/login\" 파일 수정 또는 신규 삽입<br>auth required /lib/security/pam_securitty.so"
        },
        {
            "categoryName": "1. 계정관리",
            "resultId": "U-02",
            "weakLevel": "H",
            "resultCode": "X",
            "weakLevelName": "상",
            "itemName": "비밀번호 복잡성 설정",
            "manageMethod": "[Linux - RHEL5]<br>1. 비밀번호 복잡성 설정 파일 확인<br># /etc/pam.d/system-auth, /etc/login.defs 내용을 내부 정책에 맞도록 편집<br>2. /etc/pam.d/system-auth 파일 설정<br>※ 다음 라인에 비밀번호 정책을 설정함<br>- 비밀번호 정책 설정 예시<br>password   requisite  /lib/security/$ISA/pam_cracklib.so retry=3 minlen=8 lcredit=-1 ucredit=-1 dcredit=-1 ocredit=-1<br>3. /etc/login.defs 파일 점검<br>pass_warn_age = 7(비밀번호 기간 만료 경고)<br>pass_max_days = 60(최대 비밀번호 사용 기간 설정)<br>pass_min_day = 1(최소 비밀번호 변경 기간 설정)<br><br>[Linux - RHEL7]<br>1. 비밀번호 복잡성 설정 파일 확인<br># /etc/pam.d/system-auth, /etc/login.defs 내용을 내부 정책에 맞도록 편집<br>2. /etc/pam.d/system-auth 파일 설정<br>※ 다음 라인에 비밀번호 정책을 설정함<br>- 비밀번호 정책 설정 예시<br>password   requisite  /lib/security/$ISA/pam_cracklib.so retry=3 minlen=8 lcredit=-1 ucredit=-1 dcredit=-1 ocredit=-1<br>또는<br>password   requisite  /lib/security/$ISA/pam_pwquality.so retry=3 minlen=8 lcredit=-1 ucredit=-1 dcredit=-1 ocredit=-1<br>3. /etc/login.defs 파일 점검<br>pass_warn_age = 7(비밀번호 기간 만료 경고)<br>pass_max_days = 60(최대 비밀번호 사용 기간 설정)<br>pass_min_day = 1(최소 비밀번호 변경 기간 설정)<br><br>[Linux - Ubuntu]<br>1. 비밀번호 복잡성 설정 파일 확인<br>/etc/pam.d/common-auth 내부 정책에 맞도록 편집<br>2. /etc/pam.d/common-auth 파일 설정<br>※ 다음 라인에 비밀번호 정책을 설정함<br>- 비밀번호 정책 설정 예시<br>password   requisite  /lib/security/$ISA/pam_cracklib.so retry=3 minlen=8 lcredit=-1 ucredit=-1 dcredit=-1 ocredit=-1"
        },
        {
            "categoryName": "1. 계정관리",
            "resultId": "U-03",
            "weakLevel": "H",
            "resultCode": "X",
            "weakLevelName": "상",
            "itemName": "계정 잠금 임곗값 설정",
            "manageMethod": "1. vi 편집기를 이용해 \"/etc/pam.d/system-auth\" 파일 열기<br>2. 아래와 같이 수정 또는 신규 삽입<br>/etc/pam.d/system-auth 파일에 다음을 추가한다.  <br>auth required /lib/security/pam_tally2.so deny=5 unlock_time=120 no_magic_root<br>account required /lib/security/pam_tally2.so no_magic_root reset"
        },
        {
            "categoryName": "1. 계정관리",
            "resultId": "U-04",
            "weakLevel": "H",
            "resultCode": "O",
            "weakLevelName": "상",
            "itemName": "비밀번호 파일 보호",
            "manageMethod": "1. /shadow 파일 존재 확인<br>(일반적으로 /etc 디렉터리 내 존재)<br># ls /etc<br>2. /etc/passwd 파일 내 두 번째 필드가 \"x\" 표시되는지 확인<br># cat /etc/passwd<br>root:x:0:0:root:/root:/bin/bash"
        },
        {
            "categoryName": "2. 파일 및 디렉터리 관리",
            "resultId": "U-05",
            "weakLevel": "H",
            "resultCode": "O",
            "weakLevelName": "상",
            "itemName": "root 홈, 패스 디렉터리 권한 및 패스 설정",
            "manageMethod": "1. vi 편집기를 이용하여 root 계정의 설정 파일(~/.profile 과 /etc/profile) 열기<br># vi /etc/profile<br>2. 아래와 같이 수정<br>(수정 전) PATH=.:$PATH:$HOME/bin<br>(수정 후) PATH=$PATH:$HOME/bin:."
        }
    ]
}
```

<br>
</details>

<br>
