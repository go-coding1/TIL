### JPA 관련 어노테이션 정리

```java
@Entity
@NoArgsConstructor
@AllArgsContructor
@RequiredArgsContructor
@Data
@Where(clause = "delete = false")
public class Person{
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @NonNull
    @NotEmpty
    @Column(nullable = false)
    private String name;
    private String hobby;
    private STring address;
    
    @Valid
    @Embeded
    private Birthday birthday;
    
    private String job;
    
    private String phoneNumber;
    
    @ColumnDefault("0")
    private boolean deleted;
    
    public void set(PersonDto personDto){
        if(!StringUtils.isEmpty(personDto.getHobby())){
            this.setHobby(personDto.getHobby());
        }
        
        if(!StringUtils.isEmpty(personDto,getAddress())){
            this.setAddress(personDto.getAddress());
        }
        
        if(!StringUtils.isEmpty(personDto.getPhoneNumber)){
            this.setPhoneNumber(personDto.getPhoneNumber());
        }
        if(personDto.getBirthday() != null){
            this.setBirthday(Birthday.of(personDto.getBirthday()));
        }
    }
}
```



###### @Entity

DB에 저장하기 위해 유저가 정의한 클래스에 붙여줌, Domain으로 정의

RDBMS에서 Table을 객체화 시킨 것임

테이블과 연결될 클래스임을 나타낸다.

###### @Id

primary key 를 가지는 변수 선언

해당 변수를 테이블에서 주키로 설정한다.

###### @GeneratedValue

PK의 생성 규칙을 나타낸다.

기본값은 AUTO로 MySql의 auto_increment와 같이 자동증가하는 정수형 값

SpringBoot 2.0 이전에는 자동으로 auto_increment가 됐지만 SpringBoot 2.0 이후부터는 `@GeneratedValue(strategy = GenerationType.Identity)` 옵션을 줘야 auto_increment가 된다.

###### @Column

`@Column`에서 지정한 변수명과 DB컬럼명을 다르게 주고 싶은 경우에 사용한다.

```java
@Column(name = " aaa")
private String bbb;
```

로 쓰면 DB테이블 컬럼명이 aaa이름으로 생성된다. 

해당 어노테이션을 사용하지 않으면 기본적으로 멤버 변수명과 일치하는 DB컬럼을 매핑한다. 

기본 값 외 추가로 변경이 필요한 옵션은 변경이 가능하다. 

String은 기본값이 `varchar2(255)`이다. 이 값을 바꾸고 싶다면, `@Column(length = 500)`으로 지정해주면 `varchar2(500)`이 된다.

###### @NotNull

`CharSequence, Collection, Map, Array`의 객체가 null일수는 없다는 뜻. 그치만 `empty`는 가능하다.

###### @NotEmpty

`CharSequence, Collection, Map, Array`의 객체가 null과 empty값이 될 수 없다는 뜻. size > 0 이여야 한다.

###### @NotBlank

`String`이 `null`일 수 없으면 `length`가 0보다 커야한다.

###### @NoArgsConstructor

기본 생성자 자동 추가한다는 뜻이다.

옵션으로 `@NoArgsConstructor(access = AccessLevel.PROTECTED)`를 주면 기본생성자의 접근 권한을 protected로 제한할 수 있다.

Entity클래스를 프로젝트 코드상에서 기본생성자로 생성하는 것은 막되, JPA에서 Entity클래스를 생성하는 것은 허용하기 위해 사용
