# 📚스프링 부트와 AWS로 구현하는 웹 서비스

![main](https://user-images.githubusercontent.com/44271206/125600462-3f4c73ed-9b5b-44f5-9b88-9064e4a94efe.png)





## ✅개발 환경

RESTful API 기반 Web Application

- IntelliJ
- Springboot - 2.1.7
- Gradle - 4.10.2
- JUnit4
- Github
- H2
- lombok
- JPA
- OAuth2.0
- Mustache
- Bootstrap
- AWS EC2 (Amazon Linux AMI 2)
- AWS RDS (MariaDB 10.2.21)
- Putty
- HeidiSQL






## ✅프로젝트 구성

#### 프로젝트 환경 설정

- 버전 관리를 쉽게하기 위해서 **Spring boot starter** 사용
- 아직 많은 기업에서 JUnit4를 사용한다는 점, 교재에 따라 JUnit5대신 **JUnit4** 사용
- JUnit의 버전에 따라 빌드 도구는 **Gradle의 4버전**
- 테스트용 Database로 배포할 때마다 초기화되는 **H2** 사용
- 쿼리문에 신경쓰지 않고, 객체 지향적인 코딩을 위해 **JPA** 사용
- 심플하고 View의 역할과 서버의 역할을 분리할 수 있는 **Mustache** 사용





#### 구현한 API의 동작과정

- Posts의 CRUD
- 로그인







## ✅발생했던 이슈 사항

1. OAuth2.0을 구현하고, 재배포시 로그인 상태가 초기화되는 상황을 해결하기 위해 Session에 로그인한 유저의 정보를 저장. 그러나 HttpSession의 getAttribute("user")하는 과정에서 **NullPointerException**발생
   - 원인: HttpSession이 null값으로  내장 메소드가 실행되지 않은 것.
   - HttpSession을 final 키워드로 선언하여 해결, 해당 Class에는 **@RequiredArgsConstructor**를 적용했다. 해당 Annotation은 final로 선언된 필드들을 생성자도 자동 생성하기 때문에 final 키워드로 해결할 수 있다.
2. 로그인한 유저의 이름이 화면에 나오지 않고, 로컬 컴퓨터의 이름이 나오는 문제가 발생
   - 원인: index.js에서 {{userName}}으로 표시했는데, Window OS의 환경변수인 %USERNAME%으로 인해 발생한 문제
   - userName 대신 loginUser로 변경하여 해결
3. IntelliJ에서 DB Brower를 이용하여 AWS RDS 연동 과정에서 Connection이 안되는 문제 발생
   - IntelliJ 대신 HeidiSQL 툴과 AWS RDS를 연동
   - IntelliJ의 설정 문제같은데, 해결 방법을 찾지 못함
4. Putty환경에서 프로젝트와 EC2 서버 연동까지 완료, RDS까지 연동하는 과정에서 실패
   - 외부 Security 연동과 RDS를 연동하는 과정에서 AWS의 database를 인식하지 못함
   - EC2 서버에 application-real-db.properties 오타 발견
   - [database명]과 [DB 인스턴스 명] 구분하기





## ✅프로젝트 진행 예정

- Travis CI 배포 자동화
- NginX 무중단 배포