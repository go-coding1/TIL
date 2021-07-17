# Spring 웹 계층

API를 만들기 위해 총 3개의 클래스가 필요합니다.

* Request 데이터를 받을 DTO
* API 요청을 받을 Controller
* 트랜잭션, 도메인 기능 간의 순서를 보장하는 Service

여기서 많은 분들이 오해하고 계신 것이, **Service에서 비즈니스 로직을 처리**해야 한다는 것입니다. 하지만, 전혀 그렇지 않습니다. Service는 **트랜잭션, 도메인 산 순서 보장**의 역할만 합니다.

"그럼 비즈니스 로직은 누가 처리하냐?"라고 반문할 수 있습니다, 잠깐 다음 그림을 보겠습니다.

![image](https://user-images.githubusercontent.com/54675591/126027043-378c8e26-9fbc-4232-8a07-4f0f4ff22041.png)

* Web Layer
  * 흔히 사용하는 컨트롤러(@Controller)와 JSP/Freemarker 등의 뷰 템플릿 영역입니다.
  * 이외에도 필터(@Filter), 인터셉터, 컨트롤러 어드바이스(@ControllerAdvice) 등 **외부 요청과 응답**에 대한 전반적인 영역을 이야기합니다.
* Service Layer
  * @Service에 사용되는 서비스 영역입니다.
  * 일반적으로 Controller와 Dao의 중간 영역에서 사용됩니다.
  * @Transactional이 사용되어야 하는 영역이기도 합니다.
* Repository Layer
  * **Database** 와 같이 데이터 저장소에 접근하는 영역입니다.
  * 기존에 개발하셨던 분들이라면 Dao(Data Access Object) 영역으로 이해하시면 쉬울 것입니다.
* DTOs
  * Dto(Data Transfer Object)는 **계층 간에 데이터 교환을 위한 객체**를 이야기하며 DTOs는 이들의 영역을 이야기 합니다.
  * 예를 들어 뷰 템플릿 엔진에서 사용될 객체나 Repository Layer에서 결과로 넘겨준 객체 등이  이들을 이야기합니다.
* Domain Model
  * 도메인이라 불리는 개발 대상을 모든 사람이 동일한 관점에서 이해할 수 있고 공유할 수 있도록 단순화시킨 것을 도메인 모델이라고 합니다.
  * 이를테면 택시 앱이라고 하면 배차, 탑승, 요금 등이 모두 도메인이 될 수 있습니다.
  * @Entity를 사용해보신 분들은 @Entity가 사용된 영역 역시 도메인 모델이라고 이해해주시면 됩니다.
  * 다만, 무조건 데이터베이스의 테이블과 관계가 있어야만 하는 것은 아닙니다.
  * VO처럼 값 객체들도 이 영역에 해당하기 때문입니다.

Web^Controller^ , Service, Repository, Dao, Domain, 이 5가지 레이어에서 비지니스 처리를 담당해야 할 곳은 어디일까요?

바로 **Domain** 입니다.

기존에 서비스로 처리하던 방식을 **트랜잭션 스크립트**  라고 합니다.  주문 취소 로직을 작성한다면 다음과 같습니다.

```java
@Transactional
public Order cancleOrder(int orderId){
  //1)데이터베이스로부터 주문정보(Orders), 결제정보(Billing), 배송정보(Delivery) 조회
  OrdersDto order = ordersDao.selectOrders(orderId);
  BillingDto billing = billingDao.selectBilling(orderId);
  DeliveryDto delivery = deliveryDao.selectDelivery(orderId);
  
  //2)배송 취소를 해야하는지 확인
  String deliveryStatus = delivery.getStatus();
  
  //3)만약 배송 중이라면 배송 취소로 변경
  if("IN_PROGRESS".equals(deliveryStatus)){
    delivery.setStatus("CANCLE");
    deliveryDao.update(delivery);
  }
  
  //4)각 테이블에 취소 상태 Update
  order.setStatus("CANCEL");
  ordersDao.update(order);
  
  billing.setStatus("CANCEL");
  deliveryDao.update(billing);
  
  return order;
}
```

모든 로직이 **서비스 클래스 내부에서 처리됩니다.** 그러다 보니 **서비스 계층이 무의미하며, 객체란 단순히 데이터 덩어리** 역할만 하게 됩니다.(서비스는 트랜잭션과 도메인간의 순서만 보장해야함, 근데 다른 기능도 많이 사용 중) 반면 도메인 모델에서 처리할 경우 다음과 같은 코드가 될 수 있습니다.

```java
@Transactional
public Order cancelOrder(int orderId){
  //1)
  Orders order = orderRepository.findById(orderId);
  Billing billing = billingRepository.findByOrderId(orderId);
  Delivery delivery = deliveryRepository.findByOrderId(orderId);
  
  //2~3)
  delivery.cancel();
  
  //4)
  order.cancel();
  billing.cancel();
  
  return order;
}
```

Order, billing, delivery가 각자 본인의 취소 이벤트 처리를 하며(이 말은 도메인 개체가 서비스 처리(취소행위)를 담당), 서비스 메소드는 **트랜잭션과 도메인 간의 순서만 보장** 해 줍니다.

---
글 출처 : 스프링부트와 AWS로 혼자 구현하는 웹 

