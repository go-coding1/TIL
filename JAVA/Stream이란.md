이 글은 `모던 자바 인 액션`책 내용을 정리함

# 스트림이란 무엇인가?

거의 모든 자바 애플리케이션은 컬렉션을 만들고 처리하는 과정을 포함한다. 컬렉션으로 데이터를 그룹화하고 처리할 수 있다. 컬렉션은 대부분의 프로그래밍 작업에 사용된다. 하지만 연산(계산)의 측면으로 봤을 때는 컬렉션은 아직도 불편한점이 많이 있다.

**스트림**은 자바 8 API에 새로 추가된 기능이다. 스트림을 이용하면 선언형(람다)으로 컬렉션 데이터를 처리할 수 있다. 간단히 스트림이 데이터 컬렉션 반복을 멋지게 처리하는 기능이라고 생각하자. 또한 스트림을 이용하면 멀티스레드 코드를 구현하지 않아도 데이터를 **투명하게 병렬**로 처리할 수 있다. 묻지도 따지지도 말고 기존 컬렉션의 연산코드가 어떻게 스트림으로 표현되는지 보자.

```java
//menu는 음식의 정보를 담고있는 List<> 객체이다.
List<Dish> lowCaloricDishes = new ArrayList<>();
for(Dish dish : menu){
    if(dish.getCalories() < 400){
        lowCaloricDishes.add(dish)
    }
}
Collections.sort(lowCaloricDishes, new Comparator<Dish>(){	//익명 클래스로 요리 정렬
    public int compare(Dish dish1, Dish dish2){
        return Integer.compare(dish1.getCalories(), dish2.getCalories());
    }
});
List<String> lowCaloricDishesName = new ArrayList<>();
for(Dish dish: lowCaloricDishes){
    lowCaloricDishesName.add(dish.getName());	//정렬된 리스트를 처리하면서 요리 이름 선택
}
```

중간에 가비지 변수도 쓰이고 익명클래스도 사용해줘야하고 가독성도 좋지 않다. 다음은 스트림을 사용한 최신 코드이다.

```java
List<String> lowCaloricDishesName = 
    menu.stream()
    	.filter(d -> d.getCalories() < 400)
    	.sorted(comparing(Dish::getCalories))
    	.map(Dish::getName)
    	.collect(toList());
```

진짜 상당히 짧아졌다. 문법은 모르지만 대충 읽어보고 의미를 파악해보자

* `filter(d -> d.getCalories() < 400)` : 칼로리가 400 이하인 것 들만 선택하라는 뜻으로 보인다
* `sorted(comparing(Dish::getCalories))` : getCalories 이니까 칼로리로 sorted? 칼로리로 정렬 하라는 것으로 해석된다.
* `map(Dish::getName)` : map이 뭔지는 모르겠지만 getName을 보니 이름을 얻어오라는 내용인것 같다.

문법이 선언형 코드로 바뀌어졌다. if 조건문 등의 제어 블록을 사용하지 않고 '400칼로리 이하만 선택하라' 같은 동작의 수행을 지정할 수 있다.

자바 8의 스트림 API의 특징은 3가지로 요약할 수 있다.

* **선언형** : 더 간결하고 가독성이 좋아진다.
* **조립할 수 있음** : 유연성이 좋아진다.
* **병렬화** : 성능이 좋아진다.

그럼 본격적으로 스트림의 구조를 파악해보자.

# 스트림 구조

스트림의 정확한 정의는 **'데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소'**이다. 가장 중요한 말이다. 기존에 사용하던 Collection은 자료구조의 하나로 요소저장, 접근이 주제이고, 스트림에서는 계산이 주제이다. 그러니까 연속된 값들의 계산을 위한 것이라고 생각하면 된다. (하지만 위의 정의는 꼭 외워두길 바란다.)

위에서 사용했던 예제 코드를 다시 가져와서 분석해보자. (이번엔 의미를 분석하는 것이 아니라 진짜 작업을 분석한다.)

```java
List<String> lowCaloricDishesName = 
    menu.stream()
    	.filter(d -> d.getCalories() < 400)
    	.sorted(comparing(Dish::getCalories))
    	.map(Dish::getName)
    	.collect(toList());
```

> 참고로 menu는 List<> 타입이다.

* **.stream()** : menu 에서 stream을 얻어낸다. **스트림을 반환함**
* **.filter()** : 람다를 인수로 받아 스트림에서 특정 요소를 제외시킨다. 코드에서는  `d -> d.getCalories() < 400`이라는 람다를 전달해 칼로리가 400 미만인 요리를 선택한다. **스트림을 반환함**
* **.sorted()** : 스트림의 값들을 칼로리를 기준으로 오름차순으로 정렬한다. **스트림을 반환함**
* **.map()** : 람다를 이용해서 한 요소를 다른 요소로 변환하거나 정보를 추출한다. 예제에서는 `Dish::getName`을 전달해서 각 요리명을 추출한다. **스트림을 반환함**
* **.collect()** : 스트림을 다른 형식으로 변환한다. 요소에 `toList()`를 써줬으므로 `List<>`형태로 반환이 된다.

`filter() sorted() map() collect()`로 이어지는 **데이터 처리 연산**을 적용한다. `collect()`를 제외한 모든 연산은 서로 **파이프라인**을 형성할 수 있도록 *스트림을 반환*한다. 스트림은 딱 한번만 탐색할 수 있기 때문에 파이프라인을 구성하는 함수가 항상 스트림을 반환해준다. 그리고 스트림 라이브러리는 내부 반복을 사용한다. 

### 중간연산 최종연산

스트림 파이프 라인의 개념은 빌더 패턴과 비슷하다. 빌더 패턴에서는 호출을 연결해서 설정을 만든다. 그리고 준비된 설정에 `build()`메서드를 호출한다. 스트림을 이용할 때에는 **중간 연산**과 **최종 연산**으로 이뤄져 있다. 위에서 우리가 봤던 `collect()`메서드가 최종연산에 해당하고 나머지는 중간 연산에 해당한다. 스트림은 반드시 최종연산을 설정해줘야만 작동하게 되어있다.(계산만 해놓고 결과를 봐야하니까)

* 중간연산

  | 연산     | 형식      | 반환 형식  | 연산의 인수     | 함수 디스크립터 |
  | -------- | --------- | ---------- | --------------- | --------------- |
  | filter   | 중간 연산 | Stream\<T> | Predicate\<T>   | T -> boolean    |
  | map      | 중간 연산 | Stream\<T> | Function\<T, R> | T -> R          |
  | limit    | 중간 연산 | Stream\<T> |                 |                 |
  | sorted   | 중간 연산 | Stream\<T> | Comparator\<T>  | (T, T) -> int   |
  | distinct | 중간 연산 | Stream\<T> |                 |                 |

* 최종연산

  | 연산    | 형식      | 반환 형식     | 목적                                                         |
  | ------- | :-------- | ------------- | ------------------------------------------------------------ |
  | forEach | 최종 연산 | void          | 스트림의 각 요소를 소비하면서 람다를 적용한다.               |
  | count   | 최종 연산 | long(generic) | 스트림의 요소 개수를 반환한다.                               |
  | collect | 최종 연산 |               | 스트림을 리듀스해서 리스트, 맵, 정수 형식의 컬렉션을 만든다. |



# 활용

숫자 리스트에서 짝수이면서 중복을 제거해서 출력하는 스트림을 작성해보자

```java
List<Stream> numbers = Arrays.asList(1,2,1,3,3,2,4);
numbers.stream()
    	.filter(i -> i%2 == 0)
    	.distinct()
    	.forEach(System.out::println);
```

* 숫자 스트림

  [1, 2, 1, 3, 3, 3, 2, 4]

* `filter(i -> i%2 == 0)`

  [ 2, 2, 4]

* `distinct()`

  [2, 4]

* `forEach(System.out::println);`

  `System.out.println(2);`

  `System.out.println(4);`



스트림에 쓰기 시작하면 정말 끝도 없다. 글을 읽어도 헷갈리긴 마찬가지고 실제로 코드를 작성하면서 다른 개발자들의 코드를 보면서 익숙해져 가는 것이 가장 좋다. 특히 모르는 부분, 헷갈리는 부분이 나왔을 때 마다 공식문서를 들여다 보거나 구글링을 하면서 찾아보는게 가장 빠른 습득 방법이다.

