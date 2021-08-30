# html \<meta>태그 정리

meta태그의 속성을 변경해야 할 일이 있는 와중 \<meta>의 종료가 다양하다는 것을 알았다. 이곳 저곳에서 구글링 하던 도중 잘 정리해놓은 블로그가 있어서 가져온다.

[https://front8062.tistory.com/2](https://front8062.tistory.com/2)

---

* \<meta charset="utf-8">

문자 인코딩에 대한 meta 태그 입니다. charset는 보통 utf-8을 많이 사용합니다. 그 이유는 한글 때문입니다.

* \<meta name="keyword" content="블로그">

검색에 영향을 끼치는 태그입니다. 요즘에는 검색방식이 바뀌어서 그렇게 까지영향을 끼치지 않습니다.

* \<meta name="description" content="설명">

페이지 설명을 입력하여 정보파악을 할수있게합니다. 최대 155자를 입력가능하다고 한다.

* \<meta http://http:xxxx.xom/xxx">

일정 시간이 지나면 페이지 새로고침이 됩니다.

* \<meta name="format-detection" content="telephone=no">

아이폰때문에 사용하는 meta태그입니다.

전화번호와 같은 형식이면 문자열을 감지하고 전화를 걸 수 있는데, 위와 같은 태그가 있으면 문자열을 감지할 수 없게 됩니다.

* \<meta name="apple-mobile-web-app-capable" content="yes">

아이폰 웹페이지에서 풀 스크린으로 동작하도록 해주는 meta 태그입니다.

* \<meta http-equiv="X-UA-Compatible" content="옵션">

IE에서 문서모드를 설정해주는 meta 태크입니다. 인코딩 meta와 같이 필수로 추가를해줍니다.

* \<meta http-equiv="X-UA-Compatible" content="IE=9">

해당 IE문서 모드를 IE9로 설정하여 열리게 합니다.

* \<meta http-equiv="X-UA-Compatible" content="IE=edge">

IE문서모드를 최신 버전으로 열리게 합니다.

* \<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">

크롬이 설치되어있다면 IE에서도 크롬으로 열리게하는 옵션입니다.

* \<meta http-equiv="Expire" ConTENT="-1">

캐쉬의 완료 즉 파기 시간을 정의하는 옵션입니다.

* \<meta http-equiv="Cache-Control'ConTENT="no-cache'> \<meta http-equiv="Pragma' ConTENT="no-cache'>

캐쉬가 되지 않게 하는 옵션입니다.

* \<meta http-equiv="Imagetoolbar" content="no">

그림위에서 마우스 오버시 이미지 관련 툴바가 생기지 않게 하는 옵션입니다.
