## 할 일 목록

~~1. 프론트엔드 페이지 구성요소 확정 -> 기능 확정~~ : protostar_pages_components.csv 로 마무리
2. API 구체화 -> 1번을 기반으로 구성되어 추가할 게 있을지 검토
3. 백엔드 서버 개발을 위한 필요사항 정리
    1. 환경 변수 관리(Environment Variables)
        - Local
        - Jenkins 배포 : deploy 시점에 jenkins 가 생성한 임시 .env 기반으로 주입할 것, 각 서버는 validation 로직을 통해 검증이 될 수 있도록 해야 한다.  NestJS - Joi or zod / FastAPI - pydantic-settings
    2. DB 마이그레이션
        - 연결
        - ORM : NestJS - Prisma / FastAPI - SQLAlchemy, Alembic 조합
        - Migration Pipline DB 스키마 먼저 변경되어야 한다(?)
    3. 보안 및 트래픽 제어 설정
        - App Level : NestJS - @nestjs/throttler / Helmet 미들웨어 적용으로 HTTP 헤더 보안 적요
        - Nginix Level : User-Agent 필터링
        - CORS 설정
    4. 모듈 및 폴더 구조 만들기
        - NestJS:
            - auth
            - user
            - chat
            - ai-client
            - common
        - FastAPI:
            - core
            - workers
            - services
            - schemas
        - 서버 간 통신 방법 정의
    5. 로깅 전략
        - Loki 연동 :
            - NestJS - wisnton + nest-winston
            - FastAPI - structlog
    6. Health Check API : /health API 기반으로 서버 상태를 계속 확인할 수 있도록 설정
    7. API 문서 자동화(Swagger)
    8. 배포 개선
        - 신버전 배포 이후, 일정 시간이 지나면 동일하게 버전을 맞춰주는 Jenkins 잡 필요
        - 스위칭 전략 (G/B) Standby Mode 제어 전략
4. 챗봇 구현
    ~~- 데모 버전 구현을 통해 구조나 레이아웃 테스트~~ : chatbot.js
    - API 연동
    - 자동 생성 기능 구현(dashboard)

5. DevOps - Loki 완벽한 연결 확인 및 진행 

---

## 기술적 구현 결정 사항

1. 비회원일 때 세션 유지(약 7일) 방법 및 익명 데이터와 유저 유지 시키는 현실적인 방법 고안,전형적인 uuid 발급 및 max-age 로 컨트롤
2. 챗봇 HTML 코드블럭을 만들어주고, 그것이 해당 유저의 합당한 코드 블럭인지를 구현하는 방법은? CORS 기반의 도메인 등록 화이트리스트 방식으로 해줘야함.
3. 유저는 세션을 하나라도 열게 되었을 때 uuid 발급과 함께 유저로 인식되지만, 확장되어간다.
