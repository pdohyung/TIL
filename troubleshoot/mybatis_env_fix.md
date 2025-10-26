### 클래스 경로 인식 문제

상황: `class path resource [mybatis/mybatis-config.xml] cannot be opened...` 오류로 인해 테스트 컨텍스트 로딩을 실패했다.

원인: IntelliJ IDEA의 테스트 설정을 `IntelliJ IDEA` 내장 러너로 설정하면, Spring Boot 테스트 컨텍스트 로더가 MyBatis 설정 파일을 `ClassPathResource`가 아닌 `ServletContextResource`로 인식하여 웹 컨텍스트 루트에서 파일을 찾는다.

해결: 테스트 실행 권한을 IntelliJ IDEA에서 Gradle로 변경하여 Gradle의 클래스 경로 환경을 사용하도록 한다. (`Setting → Build Tools → Gradle → Run tests using: Gradle` 선택)
