# [6] 브라우저에 URL을 입력하면 일어나는 일을 5분 이상 말하기

## 브라우저에 URL을 입력하면 일어나는 일
![image](https://github.com/user-attachments/assets/f529d6d3-06e9-4794-9cd0-3aac106e065a)

### (1) 브라우저 실행 (URL 입력)

> www.abc.com에 접속
> 

```
💡URL

https:// [www.abc.com/](http://www.abc.com/) ⇒ abc.com 도메인에 속한 이름이 www인 컴퓨터
www : host name
[abc.com](http://abc.com) : domain name
```

- 인터넷 네트워크는 IP주소를 기반으로 접속하며, HTTP통신에 앞서 TCP/IP 통신이 가능해야만 브라우저에서 화면이 넘어가짐
- 하지만 아직 IP주소를 모르는 상황임

### (2) 로컬 머신(클라이언트) 입장에서 IP주소를 알아냄

1. hosts 파일
    - **hosts 파일** : URL의 IP주소가 무엇인지 단순 기술된 텍스트 파일로, hosts파일에 IP주소를 기입하면 이 주소로 변환됨
        - EX) www.abc.com의 IP주소는 5.5.5.5
            
            www.a.com의 IP주소를 4.4.4.4라고 작성하면 클라이언트 PC는 5.5.5.5가 아닌 4.4.4.4에 접속을 시도하게 되어 접속이 제대로 되지 않음
            
            ⇒ hosts파일에 보안을 많이 적용하는 이유
            
        - 보안 프로그램 설치 시 hosts 파일을 임의 변조하지 못하도록 보호해줌
2. DNS Cache
    - **DNS Cache** : 도메인 이름을 IP주소로 변환하는 과정을 효율적으로 수행하기 위해 조회 결과를 로컬에 저장하는 메커니즘
    - hosts파일에 IP주소가 없다면 DNS Cache에게 물어봄
3. DNS Query
    - **DNS Query** : 사용자가 도메인 이름을입력하고 IP 주소를 얻기 위해 DNS 서버에 보내는 요청
    - DNS Cache에 정보가 없다면(한번이라도 접속한적이 없어 IP주소를 모른다면) DNS에게 물어봄

⇒ [www.abc.com](http://www.abc.com) 입력 시, 이에 대한 정보를 hosts파일 - DNS Cache 순으로 확인하고 없다면 DNS에게 물어봄(DNS Query)

### (3) ISP의 DNS서버에 도메인의 IP주소를 요청

- **ISP (Internet Service Provider)** : 개인이나 기업체에게 인터넷 접속 서비스, 웹사이트 구축 및 웹호스팅 서비스 등을 제공하는 회사를 말한다. 대표적으로 한국에서는 KT, SK브로드밴드, LG U+ 등이 있다.
- 컴퓨터에 연결되어 있는 ISP에 DNS서버가 있고, 이 DNS서버에게 도메인의 IP주소를 요청하고 응답을 받음.
    
    EX) 이 컴퓨터가 연결되어있는 회사가 KT라면, KT의 DNS서버에 www.abc.com의 IP주소를 요청하고 DNS가 5.5.5.5라고 응답을 줌
    
- DNS에게 IP주소를 한번 물어보고 응답을 받으면 DNS Cache에 저장을 하게됨. 같은 웹사이트에 2번 이상 접속을 하는 경우, DNS Query를 진행하지 않음.


### (4) DNS로부터 받은 IP주소로 TCP/IP 연결

- HTTP 통신 프로토콜은 TCP/IP 베이스로 작동

### (5) HTTP Request (클라이언트→ 웹서버)

### (6) HTTP Response (웹서버 → 클라이언트)

### (7) 화면에 www.abc.com의 화면이 보임

- 리소스를 가져올 때 html, css, image, js 순서대로 가져와서 렌더링
1. 처음 접속 시, 웹 리소스를 다운로드하고 로컬 디스크에 저장.
2. 재접속 시
    - DNS 질의 생략.
    - 서버와 변경된 리소스만 확인.
    - 변경 없으면 캐시된 리소스를 사용하여 화면 출력.

## DNS 작동원리

1. www.abc.com의 주소를 Root DNS에게 알려달라고 요청
2. Root DNS는 com을 전문적으로 다루는 서버들(TLD DNS 서버)의 주소를 알려줌
3. 캐시 DNS가 com 서버에게 com에 속하는 abc라는 도메인을 요청하고, com서버가 IP주소들(DNS서버)을 알려줌
4. com 밑의 abc 컨트롤 DNS(Authoritative DNS Server**)**에게 www 이름을 가진 것의 IP주소를 요청하고 전달받음
   
- Root DNS : 인터넷의 도메인 네임 시스템의 루트존
- TLD DNS : 도메인 등록 기관이 관리하는 서버로, 도메인 이름의 가장 마지막 부분을 말함 (com, [co.kr](http://co.kr) 등)
- Authoritative DNS Server : 실제 개인 도메인과 IP 주소의 관계가 기록/저장/변경되는 버
## hosts 파일 보안

- 7.7 DDoS 대란 : PC가 악성코드에 감염되면 hosts파일을 변조시킴
    1. 특정 도메인(ex. www.naver.com)을 특정 IP(ex. 9.9.9.9)로 변조
    2. 9.9.9.9가 존재하지 않거나 naver를 흉내낸 fake 홈페이지를 만들어 심각한 보안 문제가 발생
 

---

### 참고 자료

[https://inpa.tistory.com/entry/WEB-🌐-DNS-개념-동작-완벽-이해-★-알기-쉽게-정리](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-DNS-%EA%B0%9C%EB%85%90-%EB%8F%99%EC%9E%91-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4-%E2%98%85-%EC%95%8C%EA%B8%B0-%EC%89%BD%EA%B2%8C-%EC%A0%95%EB%A6%AC)
