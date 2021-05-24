## The Session Facade Pattern

 비즈니스 처리를 위해서는 항상 서비스측의 여러 객체들을 이용하게 된다. 동일한 비즈니스 처리가 여러 클라이언트 프로그램에 존재한다고 전제하며, 해당 처리르 ㄹ위한 서버 객체들의 인터페이스를 변경하거나 처리 방법이 변경될 경우, 코드관리가 어렵게 된다. 물론, 비즈니스 처리마다 여러 번의 네트워크 호출로 인해 성능상의 부담도 생기게 된다. 문제는 데이터 접속과 워크플로우/비즈니스 로직이 클라이언트들 사이에 흩어져 있기 때문에, 다수의 미분화된 세션/엔티티 빈들을 사용하는 경우에는 여러 번의 네트워크 호출로 인한 부담이 생길 뿐만 아니라 코드를 관리하기도 힘들어진다.

 비즈니스 티어의 EJB들을 여러 번 호출하면서 그 호출 사이에 컨텍스트를 수집하고, 워크플로우나 세션 정보를 저장하는 클라이언트가 필요한 큰 단위의 인터페이스를 제공하는 것이 Session Facade 패턴의 목적이다. 

![구조](https://lh4.googleusercontent.com/cTVbvIU0Wo08K7luEnCyANeTQNP7CoIyr51HYNrWiGnBjix5bfiGu4xay_CbfmQgAQ08yQP7EqSGQ_79SMT9zykSCMGDsF9_WKwI2BNd8Xz-f0UCiihqskfOPUGhpTXK)

![구조](https://lh6.googleusercontent.com/mcef9faPFY-St3SzXeqK4_5Xwo0ZBcvK2l8ui26ZrzfLd3LKKySC47Qssu0vhp7gpcpLXfKUZOI20Y8InT4y6VjELzBNLplzCehP27H5RbIaXaSwFdPBEl9jaXTiHvP5)

* Session Facade

  특정 워크플로와 워크플로들의 집합이 제공하는 기능을 지원한다. 실제 작업은 비즈니스 로직을 담당하는 EJB에게 위임된다.

* BusinessEJB

  기본적으로 필요한 비즈니스 로직을 포함하고 있다. Session Facade에 대한 레퍼런스를 갖고 있으면 안된다.

> 의도는 EJB클라이언트가 하나의 트랜잭션과 하나로 묶여진 네트워크 호출로 전체 비즈니스 로직을 수행하도록 한다.

 Session Facade 패턴은 한 번의 네트워크 호출만으로도 필요한 기능을 실행할 수 있도록 수행력을 강화하고, 유스케이스를 충족 시키는데 사용되는 비즈니스 로직과 워크플로 로직들을 캡슐화 할 수 있는 레이어를 제공한다는 이점이 있다. 상태 유지 세션빈에 적용되는 경우도 있지만, 일반적으로 무상태 세션빈들을 레이어로서 활용한다. Session Facade 패턴은 오늘날 사용되고 있는 것들 중 가장 기본적인 EJB패턴이다. 퍼포먼스 이점을 제공할 뿐만 아니라 클라이언트와 서버 사이를 세션 빈의 레이어로 구분한다. 또한 애플리케이션 내에서 모든 메소드들을 모든 유스케이스에 매핑하는 것과 같은 방법으로 J2EE애플리케이션을 구분하는 EJB시스템을 위한 표준적인 아키텍처를 제시해준다.
