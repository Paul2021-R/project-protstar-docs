# 2025-12-20-task_history.md

## protostar-fastapi
### AI 모듈 및 데이터베이스 기반 구축
- **AI Core 구현**: `app/core/ai.py`를 신규 생성하여 AI 기능을 위한 핵심 로직을 작성했습니다. 더불어 `app/prompts/` 디렉토리를 신설하고 시스템 프롬프트(`system.md`)와 사용자 데이터(이력서, 포트폴리오)를 체계적으로 관리하도록 구조를 잡았습니다.
- **Redis 통합 (Redis Integration)**: `app/core/redis.py`를 통해 Redis 연결 설정을 구현하고, `config.py`와 `main.py`에 반영하여 앱 구동 시 함께 초기화되도록 구성했습니다. Docker 환경(`docker-compose.yml`, `dockerfile`)에 Redis 의존성을 반영하는 작업도 완료했습니다.

## protostar-frontend
### 챗봇 위젯 고도화 (Chatbot Enhancement)
- **UI 및 스크립트 개선**: `ChatbotWidget.tsx`와 `chatbot/page.tsx`를 중심으로 UI 로직을 개선했습니다. 챗봇 실행을 위한 `chatbot.js` 스크립트와 아이콘 에셋(`protostar_icon.png`) 연결 작업도 진행하여 사용자 경험을 다듬었습니다.
- **API 연동 준비**: API 연동을 앞두고 전반적인 코드 포맷팅을 정리하고(`5c63752`), 구조적인 준비(`769232d`)를 마쳤습니다.
