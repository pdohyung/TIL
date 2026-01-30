### h2 tcp 설정

1. h2 DB 실행 및 [`localhost:8080/h2-console`](http://localhost:8080/h2-console) 접속
2. `jdbc:h2:~/{DB명}` 최초 1회 접속
3. `jdbc:h2:tcp://localhost/~/{DB명}` 최초 생성 이후, 접속

- 커스텀 경로: jdbc:h2:tcp://localhost/~/h2_db/backend
