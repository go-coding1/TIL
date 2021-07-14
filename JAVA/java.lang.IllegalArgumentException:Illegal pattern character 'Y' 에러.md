# java.lang.IllegalArgumentException:Illegal pattern character 'Y' 에러

`java.text.SimpleDateFormat`을 사용할 때 다음과 같은 에러가 나는 경우가 있다.

`java.lang.IllegalArgumentException:Illegal pattern character 'Y'`

이 경우는 

`SimpleDateFormat simpleDateFormat = new SimpleDateFormat("YYYY-MM-dd");`에서 발생하는데 

이유는 

**JAVA 8 이상에서는 SimpleDateFormat에서 Y(대문자)를 쓸수 있지만**

**JAVA 7 이하에서는 Y(대문자)를 인지를 못해서 y(소문자)를 써야만 한다!!**

소문자로 작성하면 된다.

