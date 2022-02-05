# Fastly Global CDN Disruption

### 0. 해외 주요 서비스 접속 불가 문제 발생  
> Error 503 Service Unavailable  
> Details : cache-itm 18821-ITM ~  
> Vanish cache server  
> *error: CNN*

> Fastly error: unknown domain: www.nytimes.com.  
> Details: cache-itm18840-ITM  
> *error: NYT*

> connection failure  
> *error: Guardian*


21.06.08 화요일 7시 전후로 Github, Stackoverflow, Amazon, Twitch, Reddit, 주요 외신 페이지 ... 등의 서비스가 터지는 문제가 발생했다. github는 html 이라도 보였는데 다른 서비스는 아예 접근 자체가 불가능했다. 그러고보니 Stackoverflow나 Github가 터진것만 확인해서 몰랐는데 이제보니 뉴욕타임즈 터졌을 때 Fastly error라고 적혀있었다.  

아무튼 에러메시지만 종합해보면 
- 서버가 다운됐고
- 주된 문제는 캐시고
- 도메인 에러도 발생한 것 같다


### 1. 뭐가 문제인가  
사실 터진 서비스중에 Amazon이 보여서 AWS 문제인지 단순하게 의심했는데 그렇다기엔 AWS를 쓸 것 같지 않은 서비스도 터졌고 이모티콘 출력에만 문제가 생긴다거나 github도 action 기능은 된다고 하고 이것저것 의아한 점이 많았다. 배포서버에 문제가 생긴거면 아예 전부 먹통이 되어야 하는게 아닌가...  
그러다 Fastly Incident Report를 발견했는데 CDN은 들어나 봤지 뭔지 정확히 확인해본 적이 없어서 서버 복구되기를 기다리면서 관련 정보를 찾아봤다.  

- [Incident Report](https://status.fastly.com/incidents/vpk0ssybt3bj)

Fastly측 Report에 따르면 해당 문제는 태평양시 09:58에 보고되어 5~10분 간격으로 상황이 갱신되다가 한시간가량 지난 10:44에 문제를 발견했다. 10:57에 모니터링을 시작해서 12:41에 Resolve 처리 되었으니 최소 한 시간동안은 장애가 발생했다고 보면 되겠다. 모르긴 몰라도 배상금이 엄청날것 같다. 


### 2. Fastly? CDN?  
> Fastly describes their network as an edge cloud platform, which is designed to help developers extend their core cloud infrastructure to the edge of the network, closer to users.  
> The Fastly edge cloud platform includes their `content delivery network`, image optimization, video and streaming, cloud security, and load balancing services. [wiki](https://en.wikipedia.org/wiki/Fastly)

우선 Fastly는 미국의 클라우드 컴퓨팅 서비스 제공 업체로 개발자가 조금 더 사용자에게 가깝게 서비스할 수 있도록 돕는 것을 주 목표로 한다. `CDN`, image optimization, video & streaming, cloud security, load balancing service 등을 제공하며 나열된 기술들은 모두 조금 더 빠르고, 불편함 없이 사용자가 서비스를 이용할 수 있게 한다.  

**CDN**  
서버를 사용하는 사람이 많아지면 모든 요청이 메인 서버컴퓨터로 몰리기 때문에 병목현상 또는 접속장애가 발생한다. 게임 업데이트마다 서버가 터지는 이유도 아마 같은 문제인 것 같다.

(1)  
우선, 서버컴퓨터가 사용자의 요청에 반응하기 어려운 상황이 무엇일까?  
1. 요청이 지나치게 많다. 작업이 많아 속도가 느려진다.  
2. 거리가 멀다. 아무리 전송 속도가 빠르다고 해도, 대륙을 건너는덴 시간이 소요될 수 밖에 없다. 이래서 내가 글로벌서버에서 게임을 못한다.  
  
그렇다면 이 문제를 해결할 방법은 무엇일까.
1. 요청을 분산한다. 장사가 잘 되는 식당에서 직원(서버)를 많이 사용하는덴 이유가 있다.
2. 세계 곳곳에 서버 컴퓨터를 설치한다.

이게 CDN(Content Delivery Network) 이다. 서버의 부담을 줄이고 로딩하는데 오래 걸리는 콘텐츠들을 전송하기 위해 사용하는 **분산 네트워크**라고 정리할 수 있겠다. 

(2)  
웹 서핑을 하다보면 [사진 많음, 데이터 주의] 등의 머릿말을 단 게시글을 볼 수 있다. 사진이나 동영상이 데이터를 많이 잡아먹는다는건 누구나 아는 사실이다. 그러니 그 정보를 요청하는데도 시간이 오래 걸릴 수 밖에 없다.  
따라서 사용자가 이미지, 동영상 등을 요청하면 전 세계에 있는 CDN은 사용자와 가장 가까운 POP(Point of Presence)의 서버에 저장/캐시된 정보를 전송한다. 
1. 이 때 서비스가 CDN을 사용한다면 사용자는 특수한 도메인(DNS)에 파일을 요청하게 된다.
2. 이 DNS는 가장 가까운 POP에 정보를 요청한다.  
    - 이 때 가까운 POP의 서버(edge server)에 적당한 정보가 없다면 에지 서버는 메인 서버에 파일을 요청하고 파일을 캐싱한다. 
    - 최초 요청시에는 시간이 다소 소요된다.
3. 캐싱된 정보는 HTTP 헤더에 지정된 TTL(Time To Live)가 만료될 때 까지 정보를 저장한다. (기본 7일)

결과적으로, 사용자는 가장 가까운 POP의 서버로부터 정보를 받으므로 빠르고 편리하게 서비스를 이용할 수 있다.

여기까지 알게 되니 이 사태의 특수성이 이해가 된다. 도메인, 이모티콘과 사진 로드, 동영상 전송등에 문제가 생겼으니 이건 Fastly가 제공하는 서비스 중에서도 CDN 문제다.  

### 3. 생각
우리가 유용하게 사용하는 서비스들이 한 순간, 한 회사의 문제로 이렇게 먹통이 될 수 있다는걸 목격하고 나니 괜히 허망해졌다. 구조상 어쩔 수 없고 이런 방식 덕분에 우리가 더 편하게 서비스를 사용할 수 있단걸 알고 있음에도 그랬다.  
이전에도 서버가 터지는건 목격했지만 (게임할땐 특히 많이 봤지만,) 아예 맛이 가면 갔지 이렇게 애매하게 서비스가 다운되는 일은 본 적이 없어서 이것저것 찾아보다보니 TIL까지 쓰게 되었다.  
멋사에 지원할 때에 적었던 지원서에 좋게 느꼈던 서비스가 왜, 구체적으로 어떤 것 때문에 좋았고 불편하게 느꼈던 서비스는 왜 또 불편하다고 생각했는지 알고싶다고 적은 기억이 난다. 오늘도 마찬가지다. 뭔가 잘못되었다는건 알겠는데, 뭐가 문제인지 알지 못하니 답답하기만 했다. 하릴없이 서버만 외치며 속을 끓이기보단, 화를 내더라도 구체적으로 화를 낼 수 있으면 좋겠다는 생각을 했다.  
공부는 세상을 보는 해상도를 높이는 일이라고 하더라. Fastly 담당자는 지옥 비슷한걸 맛봤을테지만, 오늘 이 사건을 계기로 서버나 네트워크에 대해 조금 더 알 수 있게 되어 즐거웠다. 개발단계에서 뭘 건드리면 이 사태가 벌어지는지도 궁금하지만 그건... 회사에서 일하다보면 알게 되지 않을까?  
그리고 클하에서 들어보니 PyPI도 Fastly를 통해 배포되고 있어서 여러 곳에서 이슈 리포트가 되고 있다는것 같다. 한밤 중의 개발자 파이팅. 난 다시 시험공부를 하러 가야겠다.
