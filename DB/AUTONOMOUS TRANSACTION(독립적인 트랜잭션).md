# AUTONOMOUS TRANSACTION, 독립적인 트랜잭션

프로시저에서 프로시저를 호출하고 또 프로시저에서 프로시저를 호출하면서 점점 TRANSACTION의 위치가 애매해지기 시작했다. 그래서 프로시저마다 독립적인 트랜잭션을 할당해 줄수는 없을까 싶어서 찾아보다가 **AUTONOMOUS TRANSACTION**을 발견하였다. 

---

### AUTONOMOUS TRANSACTION이란?

블록에서 자신의 작업을 수행하기 위해서 그 블록의 고유의 트랜잭션을 생성하는 경우에 해당하며 그 불록의 트랜잭션의 결과가 자신을 포함하거나 호출한 트랜잭션에의 상태에 의해 영향을 받지 않는 속정을 가지는 트랜잭션을 의미한다.

마스터 트랜잭션과는 lock, resource, commit에 관련된 의존성을 가지지 않는다.



테스트를 통해서 살펴보자

**TEST_PROCEDURE1**

```SQL
CREATE OR REPLACE PROCEDURE TEST_PROCEDURE1
IS
BEGIN
    INSERT INTO ADDRESS(
                         ADDRESS_ID
                       , ZIP_CD
                        ) VALUES(
                         CSV_SEQUENCE.NEXTVAL
                       , 'A1' 
                        );
    COMMIT;
END;
```

**TEST_PROCEDURE2**

```SQL
CREATE OR REPLACE PROCEDURE TEST_PROCEDURE2
IS
BEGIN
     INSERT INTO ADDRESS(
                         ADDRESS_ID
                       , ZIP_CD
                        ) VALUES(
                         CSV_SEQUENCE.NEXTVAL
                       , 'B1' 
                        );
     TEST_PROCEDURE1;
     ROLLBACK;
    -- COMMIT;
END;
```

구조는 `TEST_PROCEDURE2`프로시져내에서 `TEST_PROCEDURE1`이 호출된다. `TEST_PROCEDURE1'`은 `A1`값을 ADDRESS 테이블에 넣고 COMMIT한다. 이후 'TEST_PROCEDURE2`프로시저에서 ROLLBACK이 이루어진다. 

내가 원하는 의도는 `TEST_PROCEDURE1`의 `INSERT`문은 독립적인 트랜잭션으로 실행되고 싶다 이 말인 즉 `ROLLBACK` 되었을 때 B1은 없고 A1만 ADDRESS테이블에 값이 들어가 있기를 바란다.

`EXEC TEST_PROCEDURE2` 명령어를 실행한후 조회해보면

![image](https://user-images.githubusercontent.com/54675591/129293882-b08f2afc-7a32-4379-b3b2-242d7f4cfc54.png)

으잉?? `TEST_PROCEDURE2`의 입장에서 봤을 때 ROLLBACK 했으므로 B1은 없어야 정상이다. 근데 B1이 INSERT 되었다. 

그 이유는 두개의 프로시저가 같은 트랜잭션 블록에 포함되어있기 때문에 `TEST_PROCEDURE1`에서 COMMIT이 되어버려 B1도 COMMIT이 되어버린 것이다. 

방법은 `TEST_PROCEDURE1`의 트랜잭션을 독립적(다른 트랜잭션에 영향받지 않게)으로 만들어 주면 된다. `TEST_PROCEDURE1`을 수정해보자.

**TEST_PROCEDURE1**

```SQL
CREATE OR REPLACE PROCEDURE TEST_PROCEDURE1
IS
PRAGMA AUTONOMOUS_TRANSACTION;
BEGIN
    INSERT INTO ADDRESS(
                                        ADDRESS_ID
                                      , ZIP_CD
                        ) VALUES(
                                        CSV_SEQUENCE.NEXTVAL
                                      , 'A1' 
                        );
    COMMIT;
    --ROLLBACK;
END;
```

**TEST_PROCEDURE1**프로시저에 `PRAGMA AUTONOMOUS_TRANSACTION;`로 독립트랜잭션을 정의해주었다.

기존에 INSERT된 정보들을 지운 후 다시 `EXEC TEST_PROCEDURE2;`를 실행해주자

![image](https://user-images.githubusercontent.com/54675591/129294407-61d2ba0b-7b56-4bce-b0bc-009b42184e68.png)

원하는 결과가 나왔다.!! 이런식으로 AUTONOMOUS_TRANSACTION을 지정해주면 프로시져에게 독립적인 트랜잭션을 할당해 줄 수 있다. 

---

참고로 나는 오라클에서 진행했다. 다른 DB는 어떻게 되는지 모르겠다.