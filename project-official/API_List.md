---
tags:
  - ProtoStar
  - project
  - API
parent notes:
  - "[[MasterNote - ProtoStar]]"
  - "[[../PRD - ProtoStar|PRD - ProtoStar]]"
---
|**카테고리**|**Method**|**API URL**|**접근 권한**|**기능 설명 및 제안 사항 반영**|
|---|---|---|---|---|
|**인증/계정**|`POST`|`/api/v1/auth/register`|Public|이메일 회원가입 (초대코드 검증 로직 포함)|
||`POST`|`/api/v1/auth/login`|Public|이메일 로그인 (Access/Refresh Token 발급)|
||`POST`|`/api/v1/auth/social/{provider}`|Public|소셜 로그인 (Google, Github)|
||`POST`|`/api/v1/auth/refresh`|Public|Access Token 갱신|
||`GET`|`/api/v1/auth/invitations/validate`|Public|**[초대코드]** 유효성 검증 API|
||`POST`|`/api/v1/auth/guest`|Public|**[별지기]** 3회 제한 초과 시, 게스트 등록 또는 임시 세션 발급용|
|**유저/설정**|`GET`|`/api/v1/users/me`|User|내 프로필 정보 조회|
||`PATCH`|`/api/v1/users/me`|User|내 정보 수정 (비밀번호, 알림 설정 등)|
||`DELETE`|`/api/v1/users/me`|User|회원 탈퇴|
|**자료 관리**<br><br>  <br><br>**(RAG)**|`GET`|`/api/v1/materials`|User|등록된 학습 자료 목록 조회 (학습 상태 필드 포함: `Processing`, `Done`, `Fail`)|
||`POST`|`/api/v1/materials/file`|User|**[비동기]** 파일(MD, PDF) 업로드 및 벡터 임베딩 요청 (즉시 `Processing` 상태 반환)|
||`POST`|`/api/v1/materials/url`|User|**[비동기]** URL 크롤링 및 학습 요청|
||`GET`|`/api/v1/materials/{id}`|User|특정 자료 상세 및 학습 상태 확인|
||`PATCH`|`/api/v1/materials/{id}`|User|자료 내용 수정 및 재학습 트리거|
||`DELETE`|`/api/v1/materials/{id}`|User|자료 삭제 및 벡터 DB 데이터 제거|
|**챗봇 관리**|`GET`|`/api/v1/bots/me`|User|내 챗봇 설정(페르소나, 프롬프트) 조회|
||`PATCH`|`/api/v1/bots/me`|User|챗봇 설정 수정|
||`POST`|`/api/v1/bots/me/test`|User|**[데모]** 설정 기반 챗봇 테스트 (횟수 제한 없음)|
||`GET`|`/api/v1/bots/me/embed-script`|User|배포용 JS 스크립트 및 코드 스니펫 조회|
|**대시보드**<br><br>  <br><br>**(대화 관리)**|`GET`|`/api/v1/chats`|User|내 챗봇에 들어온 대화 세션 목록 (기간/키워드 필터링)|
||`GET`|`/api/v1/chats/{chatId}`|User|특정 대화 세션의 상세 내용 조회|
||`GET`|`/api/v1/chats/{chatId}/messages`|User|세션 내 메시지 리스트 조회|
||`PATCH`|`/api/v1/messages/{msgId}/correction`|User|**[정정]** AI 답변 수정 및 데이터셋 반영|
||`GET`|`/api/v1/analytics/dashboard`|User|대시보드 메인 통계 (총 질문 수, 인기 키워드 등)|
|**공개 위젯**<br><br>  <br><br>**(별지기용)**|`GET`|`/api/v1/public/bots/{botId}/info`|Public|챗봇 기본 정보(프로필 이미지, 인사말) 로드|
||`POST`|`/api/v1/public/chats`|Public|**[제한]** 새 대화 세션 생성 (IP/Fingerprint 기반 3회 제한 카운트 시작)|
||`POST`|`/api/v1/public/chats/{chatId}/msg`|Public|**[스트리밍]** 질문 전송 및 답변 생성 (SSE 등 스트리밍 응답 권장)|
||`POST`|`/api/v1/public/messages/{msgId}/feedback`|Public|답변에 대한 피드백(좋아요/싫어요) 전송|
|**알림**|`POST`|`/api/v1/notifications/webhooks`|User|Discord/Slack 등 알림 수신용 웹훅 URL 등록|
||`POST`|`/api/v1/notifications/test`|User|알림 발송 테스트|

1. **RAG 비동기 처리 (`/materials`)**:

    - 파일/URL 등록 시 서버는 즉시 응답(`202 Accepted`)을 보내고, 백그라운드에서 임베딩 작업을 수행합니다. 클라이언트는 목록 조회 API를 통해 상태(`Processing` → `Done`)를 폴링(Polling)하거나 확인할 수 있도록 설계했습니다.

2. **질문 횟수 제한 로직 (`/public/chats`)**:

    - 세션 생성(`POST /public/chats`) 시 백엔드에서 클라이언트 IP 또는 브라우저 지문(Fingerprint)을 확인하여 비회원(Guest)의 일일 생성 한도를 제어합니다.

    - 제한 초과 시 `403 Forbidden`과 함께 `Guest Auth`가 필요하다는 에러 코드를 반환하여 `/auth/guest` 로 유도합니다.

3. **챗봇 응답 스트리밍**:

    - `/public/chats/{chatId}/msg`는 긴 답변을 기다리는 지루함을 줄이기 위해, SSE(Server-Sent Events)나 WebSocket을 고려하여 설계하는 것이 좋습니다.

---

네, 현재 리스트는 PRD의 핵심 기능을 잘 커버하고 있지만, **실제 개발(구현) 단계**나 **운영 환경**, 그리고 **사용자 경험(UX)의 디테일**을 고려했을 때 누락되었거나 추가하면 좋을 API들이 보입니다.

다음은 **개발자 관점(백엔드/인프라)**과 **서비스 사용성 관점**에서 놓치기 쉬운 포인트들입니다.

---

### 1. 인프라 및 파일 처리 (시스템 안정성)

현재 `POST /materials/file`로 파일을 직접 업로드하는 구조로 되어 있습니다. 하지만 파일 용량이 커지거나 동시 접속이 늘어나면 서버 부하가 심해집니다.

|**Method**|**API URL**|**필요 이유**|
|---|---|---|
|`GET`|`/api/v1/common/health`|**(헬스 체크)** 로드 밸런서(AWS ALB, Nginx 등)가 서버 상태를 확인하거나, 무중단 배포 시 컨테이너 생존 여부를 확인하기 위해 필수입니다.|
|`GET`|`/api/v1/materials/presigned-url`|**(파일 업로드 최적화)** 서버를 거치지 않고 **클라이언트(FE)에서 S3(Object Storage)로 직접 업로드**하기 위한 인증 URL 발급용입니다. (서버 리소스 절약)|

### 2. 공개 위젯의 디테일 (UX 강화)

챗봇 위젯이 단순히 "안녕?" 하고 끝나는 것이 아니라, **사용자가 클릭할 수 있는 '추천 질문'**이나 **테마 설정**을 불러와야 자연스럽습니다.

|**Method**|**API URL**|**필요 이유**|
|---|---|---|
|`GET`|`/api/v1/public/bots/{botId}/starter-questions`|**(추천 질문 리스트)** 채팅방 진입 시 "이력서는 어디 있나요?", "기술 스택은?" 같은 **클릭 가능한 프리셋 질문**을 불러옵니다. (User가 설정한 값)|
|`GET`|`/api/v1/public/bots/{botId}/theme`|**(위젯 스타일링)** User가 설정한 챗봇의 메인 컬러, 아바타 아이콘, 다크모드 여부 등 **디자인 설정값**을 가져옵니다. (`/info`에 포함해도 되지만 분리하면 가볍습니다)|

### 3. RAG 신뢰성 및 디버깅 (AI 답변 근거)

AI가 답변을 했을 때, **"어떤 문서의 어느 부분을 보고 대답했는지"** 알아야 '정정' 기능을 제대로 쓸 수 있고, 별지기(채용 담당자)에게 신뢰를 줄 수 있습니다.

|**Method**|**API URL**|**필요 이유**|
|---|---|---|
|`GET`|`/api/v1/chats/{chatId}/messages/{msgId}/sources`|**(답변 출처 확인)** 특정 답변이 참고한 **문서 청크(Chunk)와 유사도 점수**를 조회합니다. (대시보드에서 "이 답변이 왜 나왔지?" 분석용)|

### 4. 검색 및 탐색 (User-04, User-06 관련)

PRD의 `User-04`(별지기가 유저 탐색)와 `User-06`(북마크) 기능이 P3 우선순위라 하더라도, API 설계 단계에서는 고려해두는 것이 좋습니다.

|**Method**|**API URL**|**필요 이유**|
|---|---|---|
|`GET`|`/api/v1/public/users`|**(유저 검색)** 기술 스택, 직무 등으로 샛별 유저 리스트를 검색합니다. (쿼리 파라미터 활용)|
|`POST`|`/api/v1/bookmarks/users/{userId}`|**(북마크 추가)** 별지기가 관심 있는 개발자를 저장합니다.|
|`DELETE`|`/api/v1/bookmarks/users/{userId}`|**(북마크 해제)** 저장 취소|
|`GET`|`/api/v1/bookmarks`|**(내 북마크 목록)** 내가 찜한 개발자 리스트 확인|

### 5. 에러 리포팅 및 피드백 상세

단순 좋아요/싫어요(`Bot-03`) 외에, 구체적인 신고나 시스템 에러 로그가 필요할 수 있습니다.

|**Method**|**API URL**|**필요 이유**|
|---|---|---|
|`POST`|`/api/v1/public/reports`|**(신고하기)** 챗봇이 부적절한 발언(욕설, 환각 등)을 했을 때 별지기가 신고하는 기능.|

---

### **종합 제안 (요약)**

가장 시급한 **Must Have**는 **1번(헬스체크, Presigned URL)**과 **3번(답변 출처 확인)**입니다.

- **헬스체크**는 배포하자마자 필요합니다.

- **답변 출처 확인**은 개발자가 본인의 챗봇을 튜닝할 때 "내가 올린 이력서 pdf의 3페이지를 보고 답했구나"를 알아야 `PATCH /messages/.../correction` (정정)을 의미 있게 할 수 있기 때문입니다.

이 내용들을 포함하여 최종 API 명세를 확정하시겠습니까? 아니면 우선순위 P0~P1(핵심 기능)에 집중하기 위해 일부는 백로그로 넘기시겠습니까?
