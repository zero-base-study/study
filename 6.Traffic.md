# 대용량 트래픽 처리에 관하여

## 지난 발표자료 요약

- LoadBalancer : 클라이언트에서 발생한 트래픽이 웹서버로 전달될 때 분산시켜주는 역할을 하는 장치 또는 기술 / 부하분산
- 일반적인 분산 처리 모델 : 위와 같은 3계층 구조에서의 클라이언트 계층, 비즈니스 로직 계층 데이터계층인 총 3계층으로 구성되어있음.
- 각 계층마다 문제가 생길 수 있어서 네이버에서는 이에 대한 분산처리 모델을 구축

네이버의 대용량 트래픽 처리 시스템

1. GCDN : 지리적으로 분산된 서버들을 연결한 네트워크(CDN)를 통해 리소스를 호출해서 사용할 수 있는데, 이를 웹 서버에서 제공하게 되면 트래픽 부하가 가중된다. 따라서 리소스 부하 분산을 위해 GCDN을 사용하여 메인 페이지 트래픽을 절감

2. SSI : SSI는 웹서버에서 지원하는 서버사이드 스크립트 언어인데, 서버사이드 스크립트 언어를 사용하여 WAS에 요청 횟수를 줄여서 웹서버의 성능을 좋게 만들 수 있음

3. Apache 커스텀 모듈 : APR은 운영체제에 독립적으로 HTTP 기반 통신을 처리할 수 있는 라이브러리이고, 사용자가 필요한 요구사항에 맞게 필요한 기능을 추가하거나 확장해서 사용하는 것을 커스텀 모듈이라고 함. APR을 기반으로한 커스텀 모듈을 사용하여 아파치에서 SSI를 사용하는 것처럼 WAS의 부담을 줄이도록 함. 예를 들면 API호출로 WAS를 거치지 않게 하거나, 모든 WAS가 다운되더라도 웹 서버만 정상이면 서비스를 제공할 수 있게 커스텀 모듈을 사용해서 WAS와 통신

4. 서킷 브레이커 : 외부 서비스의 장애로 인한 연쇄적 장애 전파를 막기 위해 자동으로 외부 서비스와 연결을 차단 및 복구하는 역할을 함, 서킷 브레이커 로직 구현에는 수많은 if-else 구문이 필요한데, Netflix OSS의 서킷브레이커 라이브러리가 있어서 이 라이브러리를 사용함. (Netflix OSS Hystrix를 사용, 추가로 Spring Cloud Netflix를 활용하면 서킷 브레이커를 쉽게 활용할 수 있음)

5. 서비스 디스커버리 : 동적으로 생성, 삭제되는 서버 인스턴스에 대한 IP 주소와 포트를 자동으로 찾아 설정할 수 있게 하는 기능

6. 모니터링 체계로는 Spring Boot Actuator로 성능 지표를 수집해 지속적으로 페이지의 서비스 상태를 모니터링

### 다른 방식의 트래픽 처리

메모리와 캐시를 사용하여 DB부하를 줄이는 성능 튜닝으로 대용량 트래픽을 처리할 수 있다.

### 서버 1대의 경우

메모리간 동기화, 동시성 문제 해결로 트래픽 처리

- Synchronized 키워드 사용

```
@Component
public class CellCache{

  private Map<String, CacheDetailDataVO> globalCacheMap =  new HashMap<>();

  public Map<String, CacheDetailDataVO> getGlobalCacheMap() {
    return this.globalCacheMap;
  }

  public synchronized void setGlobalCacheMapDetail(String key, CacheDetailDataVO data) {
    this.globalCacheMap.set(key, data);
  }

}
```

- Volatile 키워드 사용

```
@Component
public class CellCache{

  private volatile Map<String, CacheDetailDataVO> globalCacheMap =  new HashMap<>();

  public Map<String, CacheDetailDataVO> getGlobalCacheMap() {
    return this.globalCacheMap;
  }

  public void setGlobalCacheMapDetail(String key, CacheDetailDataVO data) {
    this.globalCacheMap.set(key, data);
  }

}
```

- Atomic 변수 사용
  : Compare And Swap 으로 동시성 만족

```
java.util.concurrent.atomic

AtomicInteger
AtomicLong
AtomicString
AtomicBoolean
AtomicReference
```

### 서버 여러대에 적용

- Eventual Consistency
  : Near Real Time Sync(+<2s)를 보장함, 고가용성을 보장하기 위한 일관성 모델

- Spring Cloud Config
  : 분산 시스템에서 외부화된 설정 정보를 서버 및 클라이언트에게 제공하는 시스템

#### 출처

- https://ko.wikipedia.org/wiki/웹_트래픽
- https://d2.naver.com/helloworld/6070967
- https://www.stevenjlee.net/2020/05/08/%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-3%EA%B3%84%EC%B8%B5-%EA%B5%AC%EC%A1%B0-3-tier-architecture/
- https://sasca37.tistory.com/271
- https://dev.classmethod.jp/articles/load-balancing-types-and-algorithm/#toc-7
- https://www.youtube.com/watch?v=XBXmHCy1EBA&ab_channel=SpringCampbyKSUG
