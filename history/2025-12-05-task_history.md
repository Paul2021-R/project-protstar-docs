# 2025-12-05 Task History

## Overview

Protostar 프론트엔드(`project-mini-frontend`)의 주요 페이지 스캐폴딩, 챗봇 위젯 포팅 및 임베드 기능 구현, 그리고 About 페이지 구조 개편 작업을 수행했습니다. 또한 Docker 환경 설정을 정리하여 개발 환경의 안정성을 확보했습니다.

## Completed Tasks

### 1. Frontend Page Scaffolding

`protostar_pages_components.csv` 기획에 맞춰 다음 페이지들을 구현했습니다.

- **Public Pages**:
  - Terms of Service (`/terms-of-service`)
  - Privacy Policy (`/privacy-policy`)
- **Dashboard Pages (Protected)**:
  - Chatbot Configuration (`/chatbot`)
  - Chat History (`/chat-history`)
  - Materials (`/materials`)
  - Statistics (`/statistics`)
  - Settings (`/setting`)
- **Demo**:
  - Service Chatbot Page (`/service-chatbot`)

### 2. Chatbot Widget Implementation

기존 바닐라 JS로 작성된 `chatbot.js`를 React 컴포넌트(`ChatbotWidget.tsx`)로 완벽하게 포팅했습니다.

- **주요 기능**:
  - 채팅창 토글 및 애니메이션
  - 세션 관리 (Local Storage 활용)
  - 일일 세션 제한 (Mock) 및 커스텀 오버레이 알림
  - 페이지 컨텍스트 추가 (+) 버튼 기능

### 3. Chatbot Embed Configuration

외부 사이트(예: GitHub Blog)에 챗봇을 심을 수 있는 기능을 `Chatbot Configuration` 페이지에 구현했습니다.

- **기능 상세**:
  - 도메인 등록 입력 필드
  - **Generate Embed Code** 버튼: 입력된 도메인과 `baseUrl`을 포함한 임베드 스크립트 생성
  - **Copy Code** 버튼: 클립보드 복사 기능
- **Bug Fixes**:
  - `chatbot.js` 404 에러 해결 (파일을 `public/` 디렉토리로 이동)
  - 외부 사이트에서 아이콘이 깨지는 문제 해결 (이미지 경로를 절대 경로(`baseUrl`)로 변경)
  - Client Component 에러 해결 (`use client` 지시어 추가)

### 4. About Page Refactoring & Features Restoration

사용자 피드백을 반영하여 메인 페이지와 About 페이지 구조를 개선했습니다.

- **Main Page**: `Features` 섹션 내용을 복구하여 서비스의 주요 기능을 소개하도록 변경.
- **About Page**: 기존 단일 `/about` 페이지를 삭제하고 타겟별 페이지로 분리.
  - **For Job Seekers** (`/about/seeker`)
  - **For Recruiters** (`/about/recruiter`)
  - 공통 컴포넌트(`InviteForm`, `AboutOverview`)를 제작하여 유지보수성 향상.
- **Navigation**: Header 메뉴를 `About`에서 `For Job Seekers`, `For Recruiters`로 분리하여 직관성 개선.

### 5. Docker Environment Fixes

로컬 개발 환경에서의 충돌 방지를 위해 Docker 설정을 업데이트했습니다.

- **Project Naming**:
  - `project-protostar-nest` -> `project-protostar-backend`
  - `project-protostar-fastapi` -> `project-protostar-backend`
- **Container Naming**:
  - Frontend 컨테이너 이름을 `protostar-next-dev`, `protostar-next-prod`로 구체화.

## Artifacts

- Created Pages: `src/app/(public)/**/*`, `src/app/(protected)/**/*`
- Created Components: `ChatbotWidget.tsx`, `InviteForm.tsx`, `AboutOverview.tsx`
- Public Assets: `public/chatbot.js`, `public/assets/images/project-protostar/protostar_icon.png`

## Next Steps

- Dashboard 세부 기능 구현 (파일 업로드, 채팅 기록 연동 등)
- 백엔드 API 연동
