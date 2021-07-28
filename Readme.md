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
- AWS S3
- Putty
- HeidiSQL
- Travis CI
- Nginx






## ✅프로젝트 구성

#### 프로젝트 환경 설정

- 버전 관리를 쉽게하기 위해서 **Spring boot starter** 사용
- 아직 많은 기업에서 JUnit4를 사용한다는 점, 교재에 따라 JUnit5대신 **JUnit4** 사용
- JUnit의 버전에 따라 빌드 도구는 **Gradle의 4버전**
- 테스트용 Database로 배포할 때마다 초기화되는 **H2** 사용
- 쿼리문에 신경쓰지 않고, 객체 지향적인 코딩을 위해 **JPA** 사용
- 심플하고 View의 역할과 서버의 역할을 분리할 수 있는 **Mustache** 사용





## ✅무중단 배포 인프라 구조

![infra구조](C:\Users\82103\Desktop\IntelliJ_Project\127263914-bc2fb44a-840b-491a-912e-c3260c3dcfd9.png)



## ✅ API 제작 과정

MVC 패턴으로 Dto, Service, Controller 클래스와 JpaRepository를 상속받은 Repository를 활용



1. Domain 패키지
   - Domain 패키지는 Database와 직접적으로 관련된 클래스들을 다룬다. 예를 들어 Entity와 Repository가 해당된다.
   - 굳이 패키지를 분리하는 이유는 Web Layer별로 역할을 철저히 나누며 테스트를 쉽게 하기 위해서입니다. 
2. 생성자로 Bean 주입
   - 생성자로 Bean을 주입하면 순환 참조와 같은 문제가 있을 때, 사전에 오류를 발견할 수 있다.
   - 해당 클래스의 의존성 관계가 변경될 때마다 생성자 코드를 수정할 필요가 없다.
   - 다른 방식과 다르게 필드를 final로 선언할 수 있다. 이는 런타임 환경에서 객체가 변경되는 상황을 사전에 방지할 수 있으며, @RequiredArgsConstructor로 생성자를 만들 수 있다.
3. Dto를 역할에 따라 분리
   - Posts Dto로 API를 처리할 수 있지만, request/response용 Dto를 따로 구현했다.
   - Posts Dto는 Entity 클래스로 Database로 맞닿아 있다. 즉, Entity 클래스는 Database의 Table과 맵핑되는 역할을 하므로, Entity 클래스의 잦은 변경에는 고려해야할 점이 많다.
4. 테스트 코드 작성
   - 기능이 많은 어플리케이션의 경우, 테스트 코드는 프로젝트의 완벽함을 입증한다.
   - 가령, 특정 코드를 변경한 후,  기존 코드들이 정상적으로 작동하는지 확인하려면 어플리케이션을 직접 구동해서 하나하나 실행해봐야 한다.
   - 하지만, 테스트 코드는 이러한 번거로움을 해결할 수 있으며, CI/CD를 활용하는 경우에는 프로젝트의 빌드를 성공적으로 다룬다.





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
   - 대체재로 MariaDB에 호환되는 HeidiSQL을 활용, 정상적으로 진행 가능

4. Putty에서 EC2서버의 hostname을 변경했지만, 적용 안됌

   - 프로젝트명과 hostname을 똑같이 해봤지만 실패

   - 설정 과정에서 문제가 있다고 생각하던 중, 버전을 고려함

   - 지금까지 서비스가 종료된 Amazon Linux AMI의 방법을 사용했던 것, 해당 프로젝트는 Amazon Linux AMI 2 버전이므로 AWS의 가이드를 참고하며 아래 명령어를 통해 해결

     ```shell
     sudo hostnamectl set-hostname webserver.mydomain.com(hostname)
     ```

     

5. Putty환경에서 프로젝트와 EC2 서버 연동까지 완료, RDS까지 연동하는 과정에서 실패
   - 외부 Security 연동과 RDS를 연동하는 과정에서 AWS의 database를 인식하지 못함
   - EC2 서버에 application-real-db.properties 오타 발견
   - [database명]과 [DB 인스턴스 명] 구분하기

6. 프로젝트 변경사항을 커밋/푸쉬했을 때, EC2 서버에 반영되지 않는 상황(배포 자동화x)
   - deployment-logs : 기존에 열려있는 PID를 죽이지 못하고, 새로 배포가 안되는 문제
   - nohup.out : the port may already be in use or the connector may be misconfigured.
   - 설정된 8080 port가 죽지않는 상황임을 인지하였고, 이를 강제로 종료할 수 있는 방법을 찾음
   - 명령 프롬프트 창에서 netstat -ano 명령어로 현재 pid를 확인, 해당 pid를 강제 종료시킴

