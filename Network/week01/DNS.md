# 2. Application layer (1)

> DNS, **HTTP** : Application 프로토콜
>
> 본질은 HTTP request response ! 
>
> HTTP request response를 하기 위해 ip주소가 필요한데 ip주소를 기억하지 못하므로 name을 ip 주소로 변환하기 위한 DNS라는 준비동작을 하는 것임
>
> - DNS - UDP
> - HTTP - TCP
>
> DNS는 host이름과 ip주소만 왔다갔다하므로 메세지 크기가 굉장히 작음. 유실되도 데미지가 작아 속도가 월등히 빠른 UDP를 사용.
>
> HTTP는 response 크기가 굉장히 크기 때문에 유실되면 큰 문제이므로 TCP를 사용함.

<br>

## Cahing Example

![image-20210502214528087](https://user-images.githubusercontent.com/77573938/116816151-c34fce80-ab9b-11eb-811f-e6f7c949f044.png)

- 라우터 A에서 서버까지 갔다가 돌아오는 시간 :  100 Mbps
- requset하는 시간 : 초당 15Mbps
- 인터넷 딜레이 시간 :  2초

->

- request에 대한 응답이 초당 15Mbps로 들어오고, 내부 네트워크의 폭이 100 Mbps이므로 내부 망은 굉장히 원활하다. (LAN 사용률 : 15%) (100차선에 15차선이 사용된 꼴)

- 그러나 초당 15Mbps로 들어오는데 비해 access link 가 15.4Mbps이므로 access link utilization이 99%이다. (15.4차선에 15차선이 사용된 꼴)

- 링크 사용률이 80% 이상이 되면 지연 발생함



### 해결방법

1. **직관적인 해결책 :  케이블 확장공사** (not good)
   - 단점 : 비용 수천만원

![image-20210502211710605](https://user-images.githubusercontent.com/77573938/116816164-cba80980-ab9b-11eb-8186-37a273fcf1e8.png)

<br>

2. **해결책 : 웹 프록시를 설치함** (good)
   - hit rate=0.4이므로 request의 40%를 local web cache에서 처리한다.
   - 99% -> 60% 로 줄어들기 때문에 평균딜레이는 1.2초로 케이블 확장공사 방법에 비해 성능이 훨씬 좋다.

![image-20210502211655641](https://user-images.githubusercontent.com/77573938/116816169-d2cf1780-ab9b-11eb-89a2-ea16d92ecac0.png)

<br>

## Conditional GET

> 일관성 문제 해결방안

캐시가 있으면 무조건 메모리 속도가 빨라진다. 그러나, 캐시라는게 원본에 대한 copy를 갖고 있는것이므로 일관성 문제에 대해 해결책을 가지고 있어야 한다.

![image-20210502213916723](https://user-images.githubusercontent.com/77573938/116816177-dd89ac80-ab9b-11eb-91ee-20feceef8170.png)

- **Conditional GET** : 기존에 우리가 알던 HTTP request message를 보낼 때 다양한 필드를 사용하는데, `if-modified-since` 필드를 사용하는 것. 조건에 따라 GET하겠다.
- `if-modified-since: <date>` : `<date>` 시간 이후로 수정되었는지를 확인 하는 것

- 수정이 안되었으면 `304 Not Modified`를 내보내고 실제 파일은 담지 않고 보낸다.
- 수정이 되었으면 파일을 담아 보낸다.

<br>

![image-20210502213931148](https://user-images.githubusercontent.com/77573938/116816187-ea0e0500-ab9b-11eb-9dfc-2d71abab28d3.png)

- 파일이 같은건지 다른지 확인할 때마다, 동일한 파일을 요청해서 가져온 후 비교한다.
- 그런데 만약 파일 사이즈가 4GB이면 요청이 들어올 때마다 4GB가 왔다갔다하므로 비효율적이다.
- -> 파일이 같은 버전인지만 확인하고 싶을때 `Conditional GET` 을 사용하면 된다.

<br>

cf. 트래픽이든 뭐든 문제가 생겼을 때 기본적인 해결방안 : 캐시를 두거나 계층화를 시킨다.



# DNS (Domain Name System)

> application 계층의 또 다른 중요한 프로토콜

### DNS란?

DNS는 도메인네임서버이며 거대한 분산 데이터베이스 시스템이다. 인터넷은 서버들을 유일하게 구분할 수 있는 32-bit IP주소를 기본체계로 이용하는데 숫자로 이루어진 조합이라 인간이 기억하기에는 무리다. 따라서 DNS를 이용해 IP주소를 인간이 기억하기 편한 언어체계로 변환하는 역할을 DNS가 한다.



### 도메인 이름 체계

DNS가 저장 관리하는 계층 구조에서 최상위엔 루트(root)가 존재하고 그 아래로 모든 호스트가 트리 구조로 이루어져 있다. 그리고 이 루트 도메인 바로 아래 단계에 있는 것을 1단계 도메인이라고 하며 이를 최상위 도메인이라고 한다. 이를 약어로 TLD(Top Level Domain)이라고 한다. 최상위 도메인은 국가명을 나타내는 국가최상위도메인과 일반적으로 사용 되는 일반최상위도메인으로 구분된다.

<br>

**프로세스와 프로세스 사이의 커뮤니케이션**

- 프로세스가 다른 기계에 존재하는게 특징

- 다른 기계에 있는 프로세스에 메세지를 보내려고 하면 그 프로세스의 주소를 명확히 알아야 도착함
- process와 process가 통신을 하기 위해선, port도 알아야하지만 결국 process가 running 하고 있는 machine의 주소를 알아야 한다.

- 우리가 사용하는 주소는 ip번호와 포트번호로 구성되어 있음

- -> DNS의 개념도구 : 호스트 네임에 해당되는 ip주소만 기록을 해두자 (비유하자면 전화번호부)

### Domain Name System

- IPv4와 domain name을 맵핑해 놓은 mapping table(호스트 이름-IP 주소)

- 그러나 이렇게 할 경우, 여러 문제가 있다.
  - 검색시간이 엄청나게 걸림
  - 사람이 몰리면 서버가 느려짐
  - 서버가 다운되면 웹 브라우징을 못함

- -> 현재 DNS는 **분산화 & 계층화** 시켜놨다.

<br>

## DNS: a distributed, hierarchical database

계층별로 나뉘는 DNS 서버 & host name도 나라별 or 목적별 계층을 가짐

![image-20210502220121641](https://user-images.githubusercontent.com/77573938/116816198-f09c7c80-ab9b-11eb-80cf-4fd21a0ccea4.png)

- **Root DNS Servers**

  : TLD server들의 host name과 그에 맵핑 되는 IP address들

  - Top-Level Domain(TLD): .com, .net, .org, ...

- **Top-Level Domain (TLD) servers**

  : Authoritative domain server들의 host name과 그에 맵핑 되는 IP address들

  - 각 DNS 서버는 .com/.net/.org로 끝나는 host name의 DNS 서버의 host name과 IP address들을 보유

- **Authoritative DNS servers**

  : Canonical name들과 맵핑되는 IP주소들을 가짐
  
  - **네트워크를 운영하는 모든 기관은 각자 authoritative DNS 서버를 운영해야 함**
  - 각 기관이 보유하고 있는 기관의 호스트 이름의 mapping에 대한 책임을 관리

<br>

Example)  Client가 www.amazon.com 의 IP주소를 요청할 때

- root server에게 com TLD server의 호스트 name과 IP주소를 요청
- .com DNS server(TLD server)에게 amazon.com DNS 서버의 IP주소 요청
- amazon.com DNS server에게 IP 주소 요청

<br>

### Local DNS name server (LDNS)

- 각 네트워크 기관들이 DNS 캐시처럼 내부에서 요청되는 쿼리들(호스트+ip주소 mapping)에 대한 결과들을 캐싱하고 있는 것
- 사용자는 local name server에 물어보고, 여기에 mapping이 없으면 외부에 물어봄
- 호스트가 DNS 쿼리를 수행하면 쿼리가 로컬 DNS 서버로 전송됨
  - 최근 이름-주소 변환 쌍의 로컬 캐시를 가짐 (but, 오래되었을 수 있음)
  - 프록시 역할을 하며 쿼리를 계층 구조로 전달

<br>

## host name으로 ip주소를 알아오는 과정

![image-20210502221831031](https://user-images.githubusercontent.com/77573938/116816205-f72af400-ab9b-11eb-85a5-ce916bee0211.png)

- 목표 : `dns.poly.edu` 가 `dns.cs.umass.edu`에 메세지를 보내고 싶은 상황 -> ip주소를 알아야함
  - Client는 자신이 속한 ISP의 LDNS에게 DNS 질의
  - LDNS는 root server에게 .edu를 관장하는 TLD 서버의 IP주소 받아옴
  - LDNS는 IP주소를 받아 umass.edu의 authoritative server의 IP 요청,
  - LDNS는 authoritative server에게 다시 질의, 최종 목적지의 IP 요청

③ .edu TLD server의 IP주소 알려줌

⑤ authoritative server의 IP주소 알려줌

⑦ 최종 목적지의 IP주소 알려줌

⇒ 8번 이후 결과적으로 TCP connection을 묻고 연결 시작

<br>

## DNS: caching, upgrading records

> 항상 캐시가 등장하면 일관성 문제가 발생함. 이에 대한 대응책

- **TTL (Time To Live)** : 레코드가 유효한 기간

<br>

## DNS records

- 각 레코드는 `name, value, type, ttl` 4개의 필드로 구성되어 있다.

- **`type` 필드에 따라 DNS 맵핑을 달리 해야한다.**

![image-20210502222918198](https://user-images.githubusercontent.com/77573938/116816215-fdb96b80-ab9b-11eb-8f1f-e3f6ad533ff8.png)

- Root DNS Servers

`.edu` , `dns.edu` 두 개의 레코드가 항상 같이 다님

| name    | value   | type             | ttl  |
| ------- | ------- | ---------------- | ---- |
| .edu    | dns.edu | NS (name server) |      |
| dns.edu | 2.2.2.2 | A (address)      |      |

- Authoritative DNS servers

가장 하위 계층이므로 모든 레코드의 type은 `A`

<br>

Ex.  성균관대 학생이 한양대의 정보를 알고 싶어서 성균관대 안에서 www.hanyang.edu 를 칠 경우 

ip 주소를 알아오는 과정:

1. 성균관대 내에 있는 로컬 네임 서버로(NS) 간다.
2. 99%는 mapping이 있어서 되지만, 안된다고 가정하면 Root server로 간다.
3. Root server가 `.edu` , `dns.edu` 두 레코드를 넘겨줌 -> NS한테 물어봄
4. TLD server인 `.EDU` 가 두 레코드를 넘겨줌 -> NS한테 물어봄
5. authoritative server가 A 레코드를 응답함
6. ip주소 알 수 있음

