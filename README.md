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

