# Statement, PreparedStatement 차이

자바에서 데이터베이스로 쿼리문을 전송할 때 사용할 수 있는 인터페이스가 2가지 있다. Statement와 PreparedStatement이다. 둘다 쿼리 전송기능을 가지고 있지만 차이점이 있다. 어떤 점이 차이가 나고, 어느 인터페이스를 사용하는 것이 좋은지 자세하게 알아보자.

### 공통점

---

1. 두 인터페이스 모두 SQL 질의문을 전달하는 역할을 한다.
2. 사용시 반드시 try~catch문 또는 throws 처리를 해야한다.

### 

### Statement(인터페이스)

---

1. Statement 객체는 Statement 인터페이스를 구현한 객체를 Connection 클래스의 createStatement() 메소드를 호출함으로써 얻어진다.

2. Statement 객체가 생성되면 executeQuery() 메소드를 호출하여 SQL 문을 실행시킬 수 있다. 메소드의 인수로 SQL문을 담은 String 객체를 전달한다.

3. Statement는 정적인 쿼리문을 처리할 수 있다. 즉 쿼리문에 값이 미리 입력되어 있어야 한다.

   ![image](https://user-images.githubusercontent.com/54675591/123041533-64d2fe00-d430-11eb-8416-a2cff298e844.png)

   

### PreparedStatement(인터페이스)

---

1. PreparedStatement 객체는 Connection 객체의 preparedStatement() 메소드를 사용해서 생성한다. 이 메소드는 인수로 SQL문을 담은 String객체가 필요하다.

2. SQL 문장이 미리 컴파일되고, 실행 시간동안 인수값을 위한 공간을 확보할 수 있다는 점에서 Statement 객체와 다르다.

3. Statement 객체의 SQL은 실행될 때 매번 서버에서 분석해야 하는 반면, PreparedStatement 객체는 한번 분석되면 재사용이 용이하다.

4. 각각의 인수에 대해 위치홀더를 사용하여 SQL 문장을 정의할 수 있게 해준다. 위치홀더는 ? 로 표현된다.

5. 동일한 SQL문을 특정 값만 바꾸어서 여러 번 실행해야 할 때, 인수가 많아서 SQL문을 정리해야 될 필요가 있을 때 사용하면 유용하다.

   ![image](https://user-images.githubusercontent.com/54675591/123042191-64873280-d431-11eb-91b5-69f659e6f780.png)



### 어떤 것을 사용하는 것이 좋은가?

---

PreparedStatement를 사용하는 것이 좋다고 생각한다. PreparedStatement는 동적이 쿼리문을 처리할 수 있으므로 같은 SQL문에서 값만 변경하여 사용한다던가 인수가 많은 경우에 사용하기 좋다. 또한 미리 컴파일되기 때문에 수행 속도가 Statement 보다 빠른 장점이 있다. Statement 객체는 쿼리 실행시 값에 작은따음표( ' )가 포함되어 있으면 작은따음표를 두개 (' ') 표시해야 한다. 예를 들어 입력할 값이 I ' am 이라고 하자, 그러면 쿼리문 작성시에 값을 I ' ' am  이렇게 입력해야 한다. 그러나 PreparedStatement 객체는 작은따옴표 문제를 쿼리 실행시 자동으로 처리하므로 신경쓸 필요가 없다는 장점이 있다.
