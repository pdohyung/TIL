# 기본 개념

--- 

### JDBC

JDBC(Java Database Connectivity)는 데이터베이스와 통신하기 위한 Java 표준 API다.  
JDBC는 데이터베이스 드라이버라는 추상화 계층을 제공하여, MySQL, Oracle, PostgreSQL 등 다양한 DBMS와 동일한 방식으로 통신할 수 있도록 한다.

### JPA

객체와 관계형 DB를 매핑해주는 ORM 기술로 Java가 제공하는 API다.  
JPA는 애플리케이션과 JDBC 사이에서 동작하며, 내부적으로 JDBC를 사용해 데이터베이스와 통신한다.  
JPA는 인터페이스로 정의된 표준 기술이며, Hibernate는 이를 구현한 대표적인 프레임워크다.

# 특징

---

### JDBC

- Connection 객체는 애플리케이션과 데이터베이스 간의 연결을 생성 및 관리하며, ResultSet 객체는 SQL 쿼리의 실행 결과를 처리하는 역할을 한다.
- JDBC는 SQL 문을 직접 작성하기 때문에 SQL 중심으로 동작하며, 세부적인 쿼리 제어가 가능하다.
- 데이터베이스 최적화가 중요한 경우, 직접 SQL을 작성해 제어할 수 있는 JDBC가 적합하다.

### JPA

- JPA는 SQL 대신 JPQL(Java Persistence Query Language)을 사용해 쿼리를 작성하므로, SQL 문을 직접 작성하는 경우가 줄어든다.
- 테이블 간 매핑을 통해 애플리케이션 코드를 객체 중심으로 유지할 수 있어, 데이터베이스 구조를 코드 내부에 반영하지 않아도 된다.
