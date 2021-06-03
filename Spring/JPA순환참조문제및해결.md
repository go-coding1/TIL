# SpringBoot JPA 순환 참조 문제 및 해결

토이 프로젝트를 진행하던 중 순환참조 문제에 마주하였다. 순환 참조란 무엇일 까?

> **순환 참조**(Circular reference)란, 참조하는 대상이 서로 물려 있어서 참조할 수 없게 되는 현상을 말한다.

JPA에서는 양방향으로 연결된 Entity끼리 조회하는 경우 서로의 정보를 순환하면서 조회하다가 `stackoverflow`가 발생하게 된다. 그럼 순환참조가 왜 일어나는 것일까?

### JPA에서 순환참조 이유

Spring Boot는 `@ResponseBody`를 선언 할시 `Object`를 `json`상태로 변환하기 위해 `Jackson`라이브러리를 이용합니다. Jackson은 직렬화를 이용해서 json 형태로 객체를 변환시키게 됩니다.

> **직렬화란?**
>
> 객체의 직렬화는 객체의 내용을 바이트 단위로 변환하여 파일 또는 네트워크를 통해서 스트림(송수신)이 가능하도록 하는 것을 의미한다.

Jackson의 작동방법은 기본적으로 멤버 변수의 접근 지정자를 우선적으로 봅니다.

```jaca
public class Member{
	private String stringValue;
	int intValue;
	protected float floatValue;
	public boolean booleanValue;
	//No Getter Setter
}
```

위 코드처럼 클래스의 4개 필드 중 `public`인 booleanValue 만 JSON으로 직렬화됩니다. 기본적으로 공용필드에 대해서만 직렬화가 진행되는데, 비 공용필드를 직렬화 하기위해선 비 공용 필드에 대한 `getter()`메서드가 존재해야 직렬화를 할 수 있습니다.

그럼 실제 Entity코드를 보면서 순환참조에 대해 알아 봅시다.

`Board.java`

```java
@Entity
@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
public class Board {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name="board_id")
    private Long id;

    @Column(name="board_name")
    private String name;

    @OneToMany(mappedBy = "board", cascade = CascadeType.ALL)
    private List<Post> postList = new ArrayList<>();
}

```

`Post.java`

```java
@Entity
@Data
public class Post extends TimeEntity{
    @Id
    @GeneratedValue
    @Column(name="post_id")
    private Long id;

    @Column(nullable = false, name = "post_title")
    private String title;   //제목

    @Column(nullable = false, length = 3000, name = "post_content")
    private String content; //내용

    @ManyToOne(targetEntity = Board.class, fetch = FetchType.EAGER, optional = false)
    @JoinColumn(name = "board_id", nullable = false)
    private Board board;

}

```

Board는 게시판이고 Post는 게시글 입니다.

Board: Post = 1 : N 관계 이고 양방향 매핑을 해 놓은 상태입니다.

이 상태에서 게시판의 정보를 알고 싶어서 조회를 해보겠습니다.

![board1](https://user-images.githubusercontent.com/54675591/120608704-7312a780-c48c-11eb-9b41-a7cdf215aea6.PNG)

`stackoverflow`가 발생하면서 순환참조가 일어나고 있습니다.

Board가 Post를 참조하고 Post가 Board를 참조하고... 순서를 정리하자면

1. Board 의 멤버 변수 중 하나인 ` List<Post> postList`의 직렬화를 진행합니다.
2. Post 의 변수들 중 하나인 Board 객체를 직렬화 합니다.
3. Board 의 멤버 변수 중 하나인 ` List<Post> postList`의 직렬화를 진행합니다.

위의 과정을 계속해서 반복해 순환 참조에 빠지게 됩니다.

---

### 순환 참조 해결

해결 방법에는 여러가지가 있지만 저는 `@JsonManagedReference`와 `@JsonBackReference`를 사용해 순환 참조를 막을 수 있습니다.

* `@JsonManagedReference` :  정상적으로 직렬화 됨
* `@JsonBackReference` : 직렬화 하지 않도록 막음

`Board.java`

```java
@Entity
@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
public class Board {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name="board_id")
    private Long id;

    @Column(name="board_name")
    private String name;

    @JsonManagedReference
    @OneToMany(mappedBy = "board", cascade = CascadeType.ALL)
    private List<Post> postList = new ArrayList<>();
}

```

`Post.java`

```java
@Entity
@Data
public class Post extends TimeEntity{
    @Id
    @GeneratedValue
    @Column(name="post_id")
    private Long id;

    @Column(nullable = false, name = "post_title")
    private String title;   //제목

    @Column(nullable = false, length = 3000, name = "post_content")
    private String content; //내용

    @JsonBackReference
    @ManyToOne(targetEntity = Board.class, fetch = FetchType.EAGER, optional = false)
    @JoinColumn(name = "board_id", nullable = false)
    private Board board;

}

```

다시 요청을 실행하면

![board2](https://user-images.githubusercontent.com/54675591/120610225-ecf76080-c48d-11eb-8275-20725e4513f3.PNG)

정상적으로 조회가 되게 됩니다.
