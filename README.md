## 1단계 root경로에 있는 build.gradle 파일 수정

plugins 하위에 아래 내용 추가.
```
    id("org.sonarqube") version "5.1.0.4882"
    id 'jacoco'
```
subproject 하위에 아래 내용 추가.
```
    sonarqube {
        properties {
            // 소스 코드 경로 설정
            property "sonar.sources", "src/main"

            // 테스트 디렉토리가 존재하는 경우만 테스트 코드 경로 설정
            if (file("src/test").exists()) {
                property "sonar.tests", "src/test"
            } else {
                property "sonar.tests", "" // 테스트 파일 없음을 명시
            }

            // 컴파일된 클래스 파일 경로
            property "sonar.java.binaries", "$buildDir/classes/java/main"
            // 테스트 커버리지 플러그인 모듈 설정
            property "sonar.java.coveragePlugin", "jacoco"
            // 테스트 커버리지 레포트 경로 설정
            property "sonar.coverage.jacoco.xmlReportPaths", "$buildDir/reports/jacoco/test/jacocoTestReport.xml"
        }
    }
```

테스트 결과도출을 위해 아래 내용도 추가.
```
test {
    useJUnitPlatform()
    finalizedBy jacocoTestReport // 테스트가 끝난 후 리포트를 생성하도록 설정
}

jacocoTestReport {
    dependsOn test // 테스트 실행 후 리포트 생성
    reports {
        xml.required = true // SonarQube는 XML 리포트를 필요로 함
        xml.destination = file("build/jacocoTestReport.xml")
        html.required = true
    }
}
```

## 2단계 bootJar를 실행. 각 모듈 하위에 build 디렉토리에 빌드된 파일이 생성.
./gradlew bootJar

## 3단계 (Option) 테스트 파일 생성.
./gradlew test -x compileTestJava

