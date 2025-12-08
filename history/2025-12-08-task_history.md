# 2025-12-08-task_history.md

## protostar-nest

### Deployment Logic Improvements
- **Jenkinsfile Refactoring for Manual Control**
    - `parameters` 블록을 추가하여 배포 시 사용자가 직접 옵션을 선택할 수 있도록 개선했다.
        - `TARGET_COLOR`: 
            - `Auto`: 기존의 빌드 번호 홀짝 로직 유지.
            - `Blue` / `Green`: 특정 환경 강제 배포.
            - `Both`: **Blue/Green 컨테이너 모두 업데이트**. (Nginx 트래픽 전환은 스킵하고 Reload만 수행하여 안전성 확보)
        - `DEPLOY_ACTION`:
            - `Full Deploy`: 빌드, 배포, 트래픽 전환을 모두 수행.
            - `Build & Deploy Only`: 이미지만 업데이트하고 트래픽은 전환하지 않음.
            - `Switch Traffic Only`: 기 배포된 컨테이너로 Nginx 트래픽만 전환.
    - `Deploy to Server` 스테이지 로직을 분기 처리하여, 선택한 액션에 따라 필요한 단계만 실행하도록 최적화했다.

- **Maintenance Pipeline Added**
    - `jenkinsfile.cleanup` 신규 작성.
    - 운영 서버의 불필요한 Docker 이미지를 정리하는 유지보수용 파이프라인.
    - `PRUNE_ALL` 파이프라인 파라미터를 통해 `docker image prune -f` (기본값) 또는 `docker system prune -a --force` 실행 가능.
    - 작업 결과(성공/실패)를 Discord로 알림 발송.

### Build & Configuration Fixes
- **Prisma 7 Compatibility Fix**
    - `schema.prisma` 내 `datasource` 블록에서 `url` 속성 제거.
    - `prisma.config.ts`와 중복 설정으로 인한 빌드 에러(`P1012`) 해결 및 `prisma generate` 정상화.

## protostar-frontend

### Dependency & Configuration Update
- **React Security Patch (CVSS-10 Fix)**
    - `react`, `react-dom` 버전을 `19.1.0` -> `19.1.2`로 업데이트.
    - `eslint-config-next` 버전을 `15.5.5` -> `15.5.7`로 업데이트.
- **Project Renaming**
    - `package.json` 내 프로젝트 명을 `project-mini` -> `project-protostar`로 변경 (Version 0.9.0).
- **Jenkinsfile Correction**
    - 이미지 및 프로젝트 경로를 `mini` -> `protostar`로 수정하여 올바른 레지스트리/디렉토리를 바라보도록 변경.
    - 변수 리터럴 오타 수정 (`deployService` 변수 참조 등).

### Security Improvement
- **Chatbot XSS & Crash Fix**
    - `chatbot.js` 내 사용자 입력 메시지 및 첨부파일 제목에 대한 `escapeHtml` 처리 적용 (CodeRabbit 리뷰 반영).
    - `localStorage` 파싱 시 데이터 손상에 대비한 `safeJsonParse` 헬퍼 함수 추가 및 적용.

### Build Issue Resolution
- **Environment & Dependency Fixes**
    - `pnpm run build` 시 발생하던 `EACCES` 권한 에러 해결.
        - 원인: `docker-compose.yml`의 볼륨 마운트(`.:/app`)와 컨테이너 내부 `root` 실행으로 인해 호스트의 `.next` 디렉토리 소유권이 `root`로 변경됨.
        - 조치: `chown` 명령어로 소유권을 복구하고, 향후 Docker 사용 시 주의사항을 확인.
    - **React Version Mismatch Fix**
        - Next.js (Turbopack) 빌드 에러 해결 (`Incompatible React versions`).
        - `react` 버전을 `19.1.0` -> `19.1.2`로 업그레이드하여 `react-dom`(`19.1.2`)과 버전을 일치시킴.
    - `pnpm run build` 정상 완료 확인.

### Deployment Fix
- **Docker Image Name Mismatch Resolved**
    - `init.env` 내의 이미지 환경변수(`NEXTJS_BLUE_IMAGE`, `NEXTJS_GREEN_IMAGE`) 수정.
        - 변경 전: `ghcr.io/paul2021-r/project-mini-frontend`
        - 변경 후: `ghcr.io/paul2021-r/project-protostar-frontend`
    - Jenkins 파이프라인에서 빌드된 이미지 이름과 `docker-compose`가 참조하는 이미지 이름을 일치시켜 `manifest unknown` 에러 해결.
    - 정상 배포 확인 완료.

## protostar-server-configs

### Monitoring & Logging
- **Promtail Logging Improvement**
    - **Docker Service Discovery Implementation**
        - `promtail-config.yaml`의 `static_configs`를 `docker_sd_configs`로 대체하여 컨테이너 동적 감지 구현.
        - `relabel_configs`를 통해 Docker 컨테이너 이름을 추출하여 `container_name` 라벨로 매핑 (예: `/main-nestjs-blue` -> `main-nestjs-blue`).
        - `docker-compose.yml`에 Promtail 컨테이너용 `/var/run/docker.sock` 마운트 추가.
    - **Log Volume Mount Fix**
        - 호스트와 컨테이너 간의 로그 경로 불일치 문제 해결.
        - Promtail에 `/var/lib/docker/containers`를 마운트하고, 수집 경로를 `__path__: /var/lib/docker/containers/*/*-json.log`로 수정.

### Jenkins Pipeline Synchronization
- **Standardized Deployment Strategy**
    - `protostar-frontend` 및 `protostar-fastapi`의 Jenkinsfile을 `protostar-nest` 기준으로 동기화.
    - **Features**:
        - `TARGET_COLOR`(Auto/Blue/Green/Both) 및 `DEPLOY_ACTION` 파라미터 적용.
        - SSH 명령어 실행 신뢰성 확보 (Command Joining Pattern).
        - `Both` 옵션 지원 (Blue/Green 동시 업데이트, 트래픽 전환 스킵).
    - **Maintenance**: 각 프로젝트에 `jenkinsfile.cleanup` 파이프라인 추가.
- **protostar-fastapi Specifics**
    - `develop` 브랜치 생성 및 워크플로우 통합.
    - **Fix**: `jenkinsfile` 문법 오류 수정 (Line 31 `environment` 블록 닫는 괄호 `}` 누락) - 사용자 직접 수정 완료.
