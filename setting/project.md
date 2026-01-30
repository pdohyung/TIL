### 서브모듈 추가

1. 서브모듈 Repo 생성 및 파일 추가
2. 메인 프로젝트에 아래 적용

    ```bash
    # 서브모듈 추가
    git submodule add {Repo URL}
    
    # 업데이트
    git submodule update --remote
    ```
3. build.gradle에 추가
    ```yaml
    # yml 서브모듈 파일 복사
    task copyYML(type: Copy) {
        from './server-yml'
        include "*.yml"
        into './src/main/resources'
    }
    # gradle 리소스 생성
    processResources.dependsOn copyYML 
    ```
