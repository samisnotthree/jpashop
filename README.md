# jpashop
![image](https://user-images.githubusercontent.com/85722378/152516419-4c2c8c68-9c91-4ab5-9b53-0c6ff9c0deb2.png)


- 최근 IntelliJ 버전은 Gradle로 실행을 하는 것이 기본 설정이다. 이렇게 하면 실행속도가 느리다.
다음과 같이 변경하면 자바로 바로 실행해서 실행속도가 더 빠르다.
Preferences Build, Execution, Deployment Build Tools Gradle
Build and run using: Gradle IntelliJ IDEA
Run tests using: Gradle IntelliJ IDEA


- 외래 키가 있는 곳을 연관관계의 주인으로 정해라.
연관관계의 주인은 단순히 외래 키를 누가 관리하냐의 문제이지 비즈니스상 우위에 있다고 주인으로 정하면
안된다. 예를 들어서 자동차와 바퀴가 있으면, 일대다 관계에서 항상 다쪽에 외래 키가 있으므로 외래 키가
있는 바퀴를 연관관계의 주인으로 정하면 된다. 물론 자동차를 연관관계의 주인으로 정하는 것이 불가능 한
것은 아니지만, 자동차를 연관관계의 주인으로 정하면 자동차가 관리하지 않는 바퀴 테이블의 외래 키 값이
업데이트 되므로 관리와 유지보수가 어렵고, 추가적으로 별도의 업데이트 쿼리가 발생하는 성능 문제도 있다.


- 엔티티 설계시 주의점
엔티티에는 가급적 Setter를 사용하지 말자
Setter가 모두 열려있다. 변경 포인트가 너무 많아서, 유지보수가 어렵다. 나중에 리펙토링으로 Setter 제거

- 모든 연관관계는 지연로딩으로 설정!
즉시로딩( EAGER )은 예측이 어렵고, 어떤 SQL이 실행될지 추적하기 어렵다. 특히 JPQL을 실행할 때 N+1
문제가 자주 발생한다.
실무에서 모든 연관관계는 지연로딩( LAZY )으로 설정해야 한다.
연관된 엔티티를 함께 DB에서 조회해야 하면, fetch join 또는 엔티티 그래프 기능을 사용한다.
@XToOne(OneToOne, ManyToOne) 관계는 기본이 즉시로딩이므로 직접 지연로딩으로 설정해야 한다.

- 컬렉션은 필드에서 초기화 하자.
컬렉션은 필드에서 바로 초기화 하는 것이 안전하다.
null 문제에서 안전하다.
하이버네이트는 엔티티를 영속화 할 때, 컬랙션을 감싸서 하이버네이트가 제공하는 내장 컬렉션으로 변경한
다. 만약 getOrders() 처럼 임의의 메서드에서 컬력션을 잘못 생성하면 하이버네이트 내부 메커니즘에 문
제가 발생할 수 있다. 따라서 필드레벨에서 생성하는 것이 가장 안전하고, 코드도 간결하다.


- 생성메서드란
서비스에서  
new ~~();  
set.  
set.  
하는 행동을 서비스가 아닌 엔티티에서 하게 하는 메서드  
-> @NoArgsConstructor(access = AccessLevel.PROTECTED)  
혹은  
-> protected 생성자  
를 해당 클레스에 만들어서 서비스에서 new ~~(); 하지 못하게한다.  


- 주문 서비스의 주문과 주문 취소 메서드를 보면 비즈니스 로직 대부분이 엔티티에 있다. 서비스 계층
은 단순히 엔티티에 필요한 요청을 위임하는 역할을 한다. 이처럼 엔티티가 비즈니스 로직을 가지고 객체 지
향의 특성을 적극 활용하는 것을 도메인 모델 패턴( http://martinfowler.com/eaaCatalog/domainModel.html )이라 한다.  
반대로 엔티티에는 비즈니스 로직이 거의 없고 서비스 계층에서 대부분
의 비즈니스 로직을 처리하는 것을 트랜잭션 스크립트 패턴( http://martinfowler.com/eaaCatalog/transactionScript.html )이라 한다.


- 폼 객체 vs 엔티티 직접 사용
요구사항이 정말 단순할 때는 폼 객체( MemberForm ) 없이 엔티티( Member )를 직접 등록과 수정 화면
에서 사용해도 된다. 하지만 화면 요구사항이 복잡해지기 시작하면, 엔티티에 화면을 처리하기 위한 기능이
점점 증가한다. 결과적으로 엔티티는 점점 화면에 종속적으로 변하고, 이렇게 화면 기능 때문에 지저분해진
엔티티는 결국 유지보수하기 어려워진다.
실무에서 엔티티는 핵심 비즈니스 로직만 가지고 있고, 화면을 위한 로직은 없어야 한다. 화면이나 API에 맞
는 폼 객체나 DTO를 사용하자. 그래서 화면이나 API 요구사항을 이것들로 처리하고, 엔티티는 최대한 순수
하게 유지하자.


- 변경 감지와 병합(merge)  
준영속 엔티티?  
영속성 컨텍스트가 더는 관리하지 않는 엔티티를 말한다.
(여기서는 itemService.saveItem(book) 에서 수정을 시도하는 Book 객체다. Book 객체는 이미 DB
에 한번 저장되어서 식별자가 존재한다. 이렇게 임의로 만들어낸 엔티티도 기존 식별자를 가지고 있으면 준
영속 엔티티로 볼 수 있다.)


- 준영속 엔티티를 수정하는 2가지 방법  
변경 감지 기능 사용  
병합( merge ) 사용  


- 변경 감지 기능 사용  
영속성 컨텍스트에서 엔티티를 다시 조회한 후에 데이터를 수정하는 방법
트랜잭션 안에서 엔티티를 다시 조회, 변경할 값 선택 트랜잭션 커밋 시점에 변경 감지(Dirty Checking)
이 동작해서 데이터베이스에 UPDATE SQL 실행


- 병합 사용  
병합은 준영속 상태의 엔티티를 영속 상태로 변경할 때 사용하는 기능이다.  
병합 동작 방식  
1. merge() 를 실행한다.  
2. 파라미터로 넘어온 준영속 엔티티의 식별자 값으로 1차 캐시에서 엔티티를 조회한다.  
2-1. 만약 1차 캐시에 엔티티가 없으면 데이터베이스에서 엔티티를 조회하고, 1차 캐시에 저장한다.  
3. 조회한 영속 엔티티( mergeMember )에 member 엔티티의 값을 채워 넣는다. (member 엔티티의 모든 값
을 mergeMember에 밀어 넣는다. 이때 mergeMember의 “회원1”이라는 이름이 “회원명변경”으로 바
뀐다.)  
4. 영속 상태인 mergeMember를 반환한다.  


- 병합시 동작 방식을 간단히 정리
1. 준영속 엔티티의 식별자 값으로 영속 엔티티를 조회한다.  
2. 영속 엔티티의 값을 준영속 엔티티의 값으로 모두 교체한다.(병합한다.)  
3. 트랜잭션 커밋 시점에 변경 감지 기능이 동작해서 데이터베이스에 UPDATE SQL이 실행  
주의: 변경 감지 기능을 사용하면 원하는 속성만 선택해서 변경할 수 있지만, 병합을 사용하면 모든 속성이
변경된다. 병합시 값이 없으면 null 로 업데이트 할 위험도 있다. (병합은 모든 필드를 교체한다.)


- 엔티티를 변경할 때는 항상 변경 감지를 사용하세요  
컨트롤러에서 어설프게 엔티티를 생성하지 마세요.  
트랜잭션이 있는 서비스 계층에 식별자( id )와 변경할 데이터를 명확하게 전달하세요.(파라미터 or dto)  
트랜잭션이 있는 서비스 계층에서 영속 상태의 엔티티를 조회하고, 엔티티의 데이터를 직접 변경하세요.  
트랜잭션 커밋 시점에 변경 감지가 실행됩니다.  


---------------------------------------------------------------------------------

- V1 엔티티를 Request Body에 직접 매핑
 문제점  
 엔티티에 프레젠테이션 계층을 위한 로직이 추가된다.  
 엔티티에 API 검증을 위한 로직이 들어간다. (@NotEmpty 등등)  
 실무에서는 회원 엔티티를 위한 API가 다양하게 만들어지는데, 한 엔티티에 각각의 API를 위한 모든 요청 요구사항을 담기는 어렵다.  
 엔티티가 변경되면 API 스펙이 변한다.  
 결론  
 API 요청 스펙에 맞추어 별도의 DTO를 파라미터로 받는다.  
 

- 조회 V1: 응답 값으로 엔티티를 직접 외부에 노출한다.  
 문제점  
 엔티티에 프레젠테이션 계층을 위한 로직이 추가된다.  
 기본적으로 엔티티의 모든 값이 노출된다.  
 응답 스펙을 맞추기 위해 로직이 추가된다. (@JsonIgnore, 별도의 뷰 로직 등등)  
 실무에서는 같은 엔티티에 대해 API가 용도에 따라 다양하게 만들어지는데, 한 엔티티에 각각의 API를 위한 프레젠테이션 응답 로직을 담기는 어렵다.   
 엔티티가 변경되면 API 스펙이 변한다.  
 추가로 컬렉션을 직접 반환하면 항후 API 스펙을 변경하기 어렵다.(별도의 Result 클래스 생성으로 해결)  
 결론  
 API 응답 스펙에 맞추어 별도의 DTO를 반환한다.  
 
 
