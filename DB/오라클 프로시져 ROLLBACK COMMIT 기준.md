# 오라클 프로시져 ROLLBACK COMMIT 기준

프로시져를 작성하던 중 EXCEPTION 발생시 ROLLBACK을 해야하는 경우가 생겼다. 근데 프로시져의 ROLLBACK의 기준을 알고 싶었다. ROLLBACK을 만나면 프로시져가 수행한 모든 명령문들이 프로시져 수행 전으로 돌아가는 건지 아니면 가장 최근 COMMIT 내역으로 돌아가는지 궁금했다.

참고로 COMMIT ROLLBACK은 다 트랜잭션 단위를 기준으로 하는 데이터 상태를 나타낼때 사용된다.



### 결론

---

결론 부터 말하자면 정답은

**ROLLBACK을 만나면 가장 최근에 COMMIT된 상태로 돌아간다. **

예제 코드를 보면서 이해해 보자

### 설명

---

TEMP.TABLE

```
CREATE TABLE TEMP(
    TEMP_ID NUMBER,
    TEMP_NO VARCHAR2(10),
    STA_YMD DATE,
    MOD_DATE    DATE
)
```

![image](https://user-images.githubusercontent.com/54675591/123499518-655ad700-d672-11eb-8f97-f6a78947803d.png)

테이블의 구조와 테이블의 내용이 이 상태로 있다고 가정하고 프로시저를 짜보았다.

프로시저의 내용은 `MOD_DATE`의 값을 프로시저가 실행한 시간으로 업데이트 하는 것인데 일부러 ROLLBACK을 주기위해 `TEMP_NO`가 50인 값을 만나면 ROLLBACK이 발생하게 만들었다.

P_TEMP_FORLOOP_TEST

```SQL
CREATE OR REPLACE PROCEDURE P_TEMP_FORLOOP_TEST IS
    CURSOR cursor1 IS
    SELECT * FROM TEMP ORDER BY TEMP_ID;
BEGIN
  FOR LIST IN cursor1 LOOP
    IF LIST.TEMP_NO = '50' THEN
        DBMS_OUTPUT.PUT_LINE('예외 발생 ROLLBACK!!');
        ROLLBACK;
        RETURN;
    END IF;
    BEGIN
        UPDATE TEMP
           SET MOD_DATE = SYSDATE
         WHERE TEMP_NO = LIST.TEMP_NO;
    END;
    
    DBMS_OUTPUT.PUT_LINE('UPDATE DONE!' || LIST.TEMP_NO);
  END LOOP;
  COMMIT;
  DBMS_OUTPUT.PUT_LINE('COMMIT!!');
END P_TEMP_FORLOOP_TEST;
```

설명하자면 `IF LIST.TEMP_NO = '50' THEN`이면 ROLLBACK을 발생한다.

지금은 `FOR LOOP`문이 다 끝나고 `COMMIT`을 하는 상태이다. 이 상태로 프로시저를 실행해보자

실행1.

![image](https://user-images.githubusercontent.com/54675591/123499616-e74b0000-d672-11eb-9235-d3f3246f3059.png)

10->20->30->40 까지 업데이트 되다가 50을 만나고 ROLLBACK되어졌다. 그럼 조회를 해보자.

![image](https://user-images.githubusercontent.com/54675591/123499639-13668100-d673-11eb-878d-8be1fa7bd175.png)

아까의 결과와 변한것이 없는 걸을 확인할 수 있다.

4번의 UPDATE를 해줬지만 COMMIT을 하지 않았기 때문에 ROLLBACK을 했을때 이전에 했던 UPDATE 내역들은 하나의 트랜잭션으로 취급되어져 UPDATE 구문을 하기 전으로 돌아 간 것이다. 

그럼 이제 UPDATE를 할때마다 COMMIT을 하는 구문을 추가해보자.

P_TEMP_FORLOOP_TEST

```SQL
CREATE OR REPLACE PROCEDURE P_TEMP_FORLOOP_TEST IS
    CURSOR cursor1 IS
    SELECT * FROM TEMP ORDER BY TEMP_ID;
BEGIN
  FOR LIST IN cursor1 LOOP
    IF LIST.TEMP_NO = '50' THEN
        DBMS_OUTPUT.PUT_LINE('예외 발생 ROLLBACK!!');
        ROLLBACK;
        RETURN;
    END IF;
    BEGIN
        UPDATE TEMP
           SET MOD_DATE = SYSDATE
         WHERE TEMP_NO = LIST.TEMP_NO;
    END;
    
    DBMS_OUTPUT.PUT_LINE('UPDATE DONE!' || LIST.TEMP_NO);
    COMMIT;
  END LOOP;
  COMMIT;
  DBMS_OUTPUT.PUT_LINE('COMMIT!!');
END P_TEMP_FORLOOP_TEST;
```

아까 구문에서 COMMIT 만 한줄 추가해줬다. 그리고 다시 프로시져를 실행해보자.

![image](https://user-images.githubusercontent.com/54675591/123499691-78ba7200-d673-11eb-8f85-ab540a51b982.png)

실행 결과창에서는 아까와 똑같은 결과가 나왔다. 그럼 이제 실제 조회를 해보자.

![image](https://user-images.githubusercontent.com/54675591/123499814-3cd3dc80-d674-11eb-8044-0100bd1c581b.png)

아까와 달라졌다!! ROLLBACK되었지만 TEMP_NO가 10, 20, 30 인 행의 MOD_DATE 값은 UPDATE되었다.

이것으로 프로시져 안에서의 ROLLBACK의 기준은 하나의 프로시져를 ROLLBACK하는게 아니라 하나의 TRANSACTION을 ROLLBACK하는 것이라는 것을 알았다.



### 정리

---

* 프로시져안에서 ROLLBACK을 호출하면 이전 COMMIT 상태로 돌아간다.
* 프로시져 안에서 COMMIT 후 ROLLBACK을 호출하면 데이터의 상태는 프로시져 호출 이전이 아닌 프로시져 안에서 호출한 COMMIT 상태로 돌아간다.
* 결국 프로시져 안에서는 ROLLBACK은 프로시져 단위가 아니라 트랜잭션 단위이다.
