---
title : REST API 이해하기
date : 2020-07-25
---

# REST API 제대로 이해하기


## 오해

'잘못 구현된 REST API' 라는 표현을 가끔 기술 블로그에서 접했지만 깊이 생각해보지는 않았다. 개인적으로는, HTTP를 이용하여 인터넷 상의 데이터에 CRUD 동작 모두 지원하고, URI 형태가 'hostname/blah/resources/{id}' 혹은 'hostdomain/blah/resource' 형태를 가지면 Restful API라고 생각해왔다. 따라서 잘못된 REST API 는 CRUD를 모두 지원하지 않거나, 약속에 어긋난 URI 를 사용하는 경우라고 생각했을 뿐이다.

그런데 아래 영상을 보게 됐다.

[그런 REST API로 괜찮은가](https://www.youtube.com/watch?time_continue=1697&v=RP_f5dMoHFc&feature=emb_logo02)



그 동안의 오해를 말끔히 정리하고, 앞으로 REST API 를 어떻게 대해야 할지 명쾌하게 설명해주는 강연이다.
강연자께서는 REST API 를 발명한 Roy Fielding 의 입장과 약간의 본인 의견으로 논란을 정리해주셨다.

- RESTful 하게 구현한 것들을 REST API 라고 부르고, RESTful 하지 않은 것들은 다른 이름(예를 들면, HTTP API)라고 부르자.
- HTTP 프로토콜 위에서 동작하는 모든 API 가 REST API가 아니고, 그럴 필요도 없다.



더 정확하게는 마지막 슬라이드에 강연자의 핵심이 잘 정리돼있다.

![REST API 제대로 이해하기](https://user-images.githubusercontent.com/7664099/88474435-5e5aae80-cf61-11ea-975a-685eda0d1e8c.png)



## REST Style

아래 6가지 요건이 REST API 의 요건인데, 대부분 쉽게 이해되는 것이다. layered system 은 분산처리등을 위해 서버 사이드가 계층적으로 구성될 수 있다는 것이고, code-on-demand 는 client side 에서 코드가 실행될 수 있는 환경. 즉 javascript 를 의미한다. REST API 라고 잘못 불리는 것들은 uniform interface 를 충족하지 못한다.

- client-server
- stateless
- cache
- uniform interface
- layered system
- code-on-demand (optional)



## uniform interface

내가 REST API 의 조건이라고 생각했던 2가지. 'CRUD 구현'과 '적절한 URI 주소'는 아래의 1,2 번에 해당하며 비교적 잘 지켜지는 것이다. 대신, 3,4 번에 해당하는 것들이 생략되는 경우가 많다.

1. identification of resources
2. manipulation of resources through representations
3. self-descriptive messages
4. hypermedia as the engine of application state (HATEOAS)



### self-descriptive messages

먼저, 3번은 리소스를 처음보는 클라이언트도 해석이 가능해야 한다는 것이다. 흔히 Content-Type 헤더에 사용하는 application/json 으로는 충분치 않고, 아래의 메시지를 받은 클라이언트가 op 과 path 키값이 의미하는 것을 해석할 수 있는 방법을 제공할 수 있어야 한다.

```json
HTTP/1.1 200 OK
[{"op" : "remove", "path":"/a/b/c"}]
```



응답 헤더에 response 에 대한 설명을 명시한다면 self-descriptive 한 메시지가 될 수 있다. 다음 둘 중 하나의 방법을 사용한다.

1. Content-Type 헤더
   mp3 처럼 잘 알려진 바이너리 리소스라면 audio/mpeg 처럼 적는 것으로 충분하다. 하지만 서버-클라이언트 간의 약속으로 만들어진 json 타입이라면 IANA 라는 기관에 Media Type을 등록할 수 있다. [[바로가기](https://www.iana.org/assignments/media-types/media-types.xhtml)]
   IANA는 IP주소와 최상위 도메인등을 관리하는 인터넷 기관이다. 전문가 리뷰를 거치는 프로세스가 있기 때문에 범용적으로 사용할 것이 아니면 불필요할 것 같다.

```json
HTTP/1.1 200 OK
Content-Type : application/vnd.todos+json
[
  {"id":0, "title":"회사 가기"},
  {"id":1, "title":"집에 가기"}
]
```



2. Link 헤더 + profile relation
   직접 명세에 대한 링크를 첨부하는 방법이다. 하지만 클라이언트에서 Link와 profile을 이해해야 한다. 또, Content-Type만 봐서는 응답 내용을 예측할 수 없기 때문에 Content-negotiation 이 불가능하다.

```json
HTTP/1.1 200 OK
Content-Type : application/json
Link: <https://example.org/docs/todos>; rel="profile"
[
  {"id":0, "title":"회사 가기"},
  {"id":1, "title":"집에 가기"}
]
```



### HATEOAS (Hypermedia as the engine of application state)

리소스가 다른 리소스로 이동할 수 있는 링크를 포함해야 한다. link의 정의 역시 앞에서 언급한 self-descriptive 요건을 위한 명세에 포함돼야 한다.

다음처럼 리소스가 링크를 직접 포함할 수도 있고,

```json
HTTP/1.1 200 OK
Content-Type : application/json
Link: <https://example.org/docs/todos>; rel="profile"
[
  {
    "link" : "https://example.org/todos/1",
    "title" : "회사 가기"
  },
  {
    "link" : "https://example.org/todos/2",
    "title" : "집에 가기"
  }
]
```

template 을 포함하는 방식도 가능하다.

```json
HTTP/1.1 200 OK
Content-Type : application/json
Link: <https://example.org/docs/todos>; rel="profile"
{
  "links" : {
    "todo" : "https://example.org/todos/{id}"
  },
  "data" : [{
    "id" : 1,
    "title" : "회사 가기"
  }, {
    "id" : 2,
    "title" : "집에 가기"
  }]
}
```

link에 대한 정의 역시 명세에 포함되어야 한다는 점에서 까다롭다. 하지만 이런 부분을 고려하여 IANA에 등록된 media type이 있는데 바로 [application/vndapi+json](https://jsonapi.org/) 이다. 페이지의 예시에서 articles 리소스의 요청에 대한 응답이 articles 의 페이지를 이동하거나 author 리소스에 대한 하이퍼링크를 포함하고 있는 것을 확인할 수 있다.



## 정리

결국, HTTP 프로토콜 개발에 참여하고 REST API 를 개발한 Roy Fielding 의 문제의식은 '어떻게 웹은 망가뜨리지 않고 진화시킬 수 있을까?' 였다.

REST API 가 self-descriptive 해야 한다는 조건은 web이라는 특수한 소프트웨어 환경에서도 지속적으로 각 시스템이 서로를 이해하고 발전할 수 있는 수단을 제공하기 위함이다. 프로토콜을 정의하고 시간이 지나면 기능이 추가되고 기존의 약속이 수정된다. 이 부분에서 필연적으로 버전 호환성의 문제가 생긴다. 웹이 확장하려면 지속적으로 프로토콜을 협의하기 어려운 두 시스템도 서로를 이해할 수 있는 방법을 제공할 필요가 있다. 

또, HATEOAS 를 통해서 웹의 연결성을 유지할 수 있다. 인터넷상의 더 많은 리소스들이 합의된 Media Type을 사용하고 HATEOAS를 보장한다면 도메인 간의 경계도 줄어들고 인터넷은 그 자체로 점점 더 실세상을 반영할 수도 있겠다는 생각이 든다.

마지막으로 모든 인터넷 API 가 RESTful 할 필요는 없다. 예를 들어서, 내가 앞으로 지속적이고 확장이 필요한 리소스를 저장하는 서비스를 개발한다면 (예를 들면 구글맵) RESTful 하게 개발하는 것이 유리할 것이다. Location 이나 부동산에 대한 표준적인 Media Type 을 정의하고 응답도 self-descriptive 하면 더 많은 사용자에게 API 사용을 확산하고 호환성을 제공할 수 있다. 하지만 이런 경우가 아니라면 개발에 overhead만 커질 것이다.




