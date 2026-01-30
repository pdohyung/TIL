### Docker Redis 설정

```bash
# redis image pull
docker pull redis

# 이미지 조회
docker images

# redis 실행
docker run --name redis -d -p 6379:6379 redis

# 컨테이너 확인
docker ps -a

# redis client 접속
docker exec -it redis /bin/bash

# redis db 접속
docker exec -it redis redis-cli
```

### Docker Compose 설정

- `docker compose up`: 실행

```yaml
version: '3.8' # 버전

services:
  # spring boot
  backend:
    container_name: spring-app
    image: your-repo/spring-app:latest
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=dev
      - SPRING_REDIS_HOST=redis-service
      - SPRING_REDIS_PORT=6379
      - SPRING_REDIS_PASSWORD=${REDIS_PASSWORD}
    networks:
      - backend-network
    depends_on: # redis-service에 의존
      - redis-service

  # redis
  redis-service:
    image: redis:7-alpine # redis 이미지
    container_name: redis
    ports:
      - "6379:6379"
    command: [ "redis-server", "--requirepass", "${REDIS_PASSWORD}" ]
    networks:
      - backend-network

# 네트워크 분리
networks:
  backend-network:
    driver: bridge
```
