---

title: 카카오페이 연계
last_updated: Jan 21, 2021
keywords: study
sidebar: mydoc_sidebar
comments: true
permalink: kakaopay.html

---

## 카카오 페이 연계
- [공식 문서](https://developers.kakao.com/docs/latest/ko/kakaopay/common)

### github repo
- [FE: vue3 (node-js branch)](https://github.com/pick8chu/vue3-test/tree/node-js)
- [BE: nodejs (master branch)](https://github.com/pick8chu/server_nodejs)

### Obstacles
> BE를 Spring Boot를 사용, WebClient를 사용했을 때 겪었던 문제점

1. Content-type 문제
- VO 를 form body로 변환하는 것. (content-type : application/x-www-form-urlencoded;charset=utf-8)
- 해결: 
```java
    //    reference: https://jojoldu.tistory.com/478
    private BodyInserters.FormInserter<String> convertObjectToFormBody(Object dto) {
        try {
            Map<String, String> map = objectMapper.convertValue(dto, new TypeReference<>() {});
            MultiValueMap<String, String> params = new LinkedMultiValueMap<>();
            params.setAll(map);

            return BodyInserters.fromFormData(params);
        } catch (Exception e) {
            log.error("Url Parameter 변환중 오류가 발생했습니다. requestDto={}", dto, e);
            throw new IllegalStateException("Url Parameter 변환중 오류가 발생했습니다.");
        }
    }
```

2. 세션이 중간에 끊김
- ready를 받고, approve를 하는 중간에 세션이 끊기고 pg_token만 받는다.
- 해결: ready 이후 oid, uid 등을 기준으로 db에 저장한 이후에 approval_url에 query parameter로 추가, callback을 받은 후 db에서 select 이후 파라미터를 같이 넣고 보냄. 
```java
  this.approval_url = BASE_URL
          + "/success?partner_order_id=" + partner_order_id + "&"
          + "partner_user_id=" + partner_user_id  + "&"
          + "cid=" + cid;
```

3. Child popup /<-/> Parent 통신
- 함수 호출은 cross origin 때문에 불가.
- postMessage로 cross origin 통신 가능
- 해결: opener.postMessage("callbackFn", URL)

### etc
- WebClientConfig
```java

    @Bean
    public WebClient kakaopayClient() {
        return this.webClient()
                .defaultHeaders(httpHeaders -> {
                    httpHeaders.add(HttpHeaders.AUTHORIZATION, "KakaoAK " + ADMIN_KEY);
                    httpHeaders.add(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_FORM_URLENCODED_VALUE + ";charset=UTF-8");
//                    httpHeaders.add(HttpHeaders.ACCEPT, MediaType.APPLICATION_JSON_VALUE);
//                    httpHeaders.add(HttpHeaders.USER_AGENT, "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36");
//                    httpHeaders.add("X-Requested-With", "XMLHttpRequest");
                })
                .baseUrl(KAKAOPAY_BASE_URL)
                .build();
    }
```
