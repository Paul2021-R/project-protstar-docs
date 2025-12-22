# 2025-12-22-task_history.md

## protostar-frontend
### 챗봇 위젯 및 배포 환경 고도화 (Chatbot & Build Config)
- **환경 변수 주입 설정 (Env Injection)**: Jenkins 빌드 시점의 환경 변수가 Next.js 클라이언트 컴포넌트에 올바르게 주입되도록 `Dockerfile`의 `ARG` 설정과 `Jenkinsfile`의 `--build-arg` 옵션을 추가했습니다.
- **챗봇 연결 디버깅**: `ChatbotWidget.tsx` 및 `chatbot.js`를 수정하여 외부에서의 스크립트 로드 및 API 호출 오류를 해결했습니다.
- **UI 개선**: `feature/add-more-pages-and-modify-css` 통합을 통해 전반적인 스타일과 페이지 구성을 확장했습니다.

## protostar-fastapi
### AI 스트리밍 및 로직 구현 (AI Streaming)
- **스트리밍 기능 구현**: `feature/chat-stream` 브랜치를 병합하여 Server-Sent Events(SSE) 기반의 채팅 응답 스트리밍 기능을 구현했습니다.
- **프롬프트 엔지니어링**: `ai.py` 및 관련 모듈에서 AI 응답 품질을 높이기 위한 프롬프트 수정 및 테스트를 진행했습니다.

## protostar-nest
### CORS 및 가드 설정 (Security & Config)
- **Origin Guard 개선**: `OriginGuard`와 `main.ts`를 수정하여 CORS 처리 및 출처 검증 로직을 보완했습니다.
- **배포 설정 정비**: `docker-compose` 서비스 명칭 변경 및 `package.json` 수정을 통해 배포 안정성을 확보했습니다.
