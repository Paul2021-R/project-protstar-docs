# 2025-12-18-task_history.md

## protostar-nest
### CORS 및 보안 가드 적용
- **Origin Guard 구현**: `OriginGuard`를 추가하여 요청의 `Origin` 헤더가 화이트리스트(`https://paul2021-r.github.io`)에 포함되는지 검증하는 로직을 구현했습니다.
- **Global Guard 설정**: `main.ts`에서 `app.useGlobalGuards(new OriginGuard())`를 통해 애플리케이션 전역에 가드를 적용했습니다.
- **CORS 설정 고도화**: `enableCors` 설정에서 정적 화이트리스트를 기반으로 동적으로 Origin을 허용하도록 콜백 함수를 구현하고, 허용 메서드(`GET, POST 등`)와 `credentials: true` 설정을 적용했습니다.

### 프로덕션 컨테이너 리소스 설정
- **Node.js 앱 메모리 제한**: `app/docker-compose.yml`의 `dev` 및 `prod` 서비스에 `deploy.resources` 설정을 추가했습니다.
    - Limits: `memory: 512M`
    - Reservations: `memory: 256M`

### Redis 모듈 연동
- **RedisModule 구현**: `ioredis`를 사용하여 Redis 연결을 관리하는 글로벌 모듈(`common/redis/redis.module.ts`)을 구현했습니다.
- **ConfigService 연동**: `REDIS_HOST`, `REDIS_PORT`, `REDIS_PASSWORD` 등의 환경 변수를 통해 연결 정보를 동적으로 주입받도록 설정했습니다.
- **AppModule 통합**: `AppModule`에서 `RedisModule`을 import하여 애플리케이션 전반에서 Redis 클라이언트(`REDIS_CLIENT`)를 주입받아 사용할 수 있도록 구성했습니다.

## protostar-server-configs
### Redis 리소스 안정성 확보
- **Redis 메모리 예약 설정**: `main-server/docker-compose.yml`의 `redis` 서비스에 메모리 제한 및 예약 설정을 추가하여 서버 안정성을 강화했습니다.
    - Limits: `memory: 512M`
    - Reservations: `memory: 256M`
