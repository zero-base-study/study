# Monolithic Architecture

## 개념

- 모든 도메인을 하나의 프로그램으로 개발
- 컴포넌트 간 메서드(함수) 호출을 통해 상호작용

## 장점

- 개발과 디버깅, 배포 모두 비교적 수월한 편
- 데이터베이스가 통합되어 데이터 일관성 유지 용이
- 대체로 성능이 뛰어남(컴포넌트끼리 메모리 내의 호출을 통해 상호작용하므로 응답 속도 빠름)

## 단점

- 배포까지의 사이클(테스트, 컴파일, 빌드 등)이 너무 오래 걸림
- 일부 컴포넌트의 프레임워크, 라이브러리 업데이트가 어려움(다른 컴포넌트에 영향을 주게 됨)
- 일부 컴포넌트의 장애가 전체 서비스로 전파되기 쉬움
- (MSA 방식에 비해) 컴포넌트 간 결합도가 높아 일부 기능 변경의 파급효과가 큼
  - 비교적 요구사항 변경에 따른 유지보수가 어려운 편
- 언어, 프레임워크에 종속적
  - ex) Spring을 통해 개발 —> 도메인마다 유리한 프레임워크 사용 불가
- 하나의 프로그램에 모든 기능 집약 —> 소스코드가 복잡해짐

# MSA(MicroService Architecture)

## 개념

- 각각의 도메인을 개별 프로그램(서비스)으로 개발
- 프로그램 간 API를 통해 통신하며 상호작용

## 장점

- 컴포넌트 수정, 개선 빠름(결합도가 낮으므로 개별 컴포넌트 변경의 파급효과가 작음)
- 컴포넌트 간 결합도가 낮아 각자의 관심사에 집중하기 용이한 환경 —> 관심사의 분리
- 서비스 확장 용이 —> 효율적인 리소스 사용
- 개별 장애 전파 최소화
- 프레임워크, 라이브러리의 업데이트가 유연해짐
- 각각의 서비스들을 서로 다른 기술 스택으로 개발 가능
- 다량의 트래픽을 수용해야 하는 기능에 자원을 집중할 수 있음 —> 유지비용 절감

## 단점

- 운영, 관리가 복잡해짐
- 데이터베이스 분산으로 인해 데이터의 일관성 유지가 어려움
- 비교적 낮은 성능(네트워크 지연에 따른 응답 속도 문제 발생 가능성 높음)
- 통합 테스트가 어려움
  - 컴포넌트 간 상호작용하며 발생하는 버그에 취약한 편

## MSA로 전환한 대표적인 기업

- 아마존, 넷플릭스, 우버 등

## MS 구조도

![MSA structure](https://github.com/zero-base-study/study/assets/128391669/0f5cc234-079d-43b3-a8af-065248e7d2fe)

출처: https://www.thoughtworks.com/insights/blog/well-factored-approach-securing-roi-your-service-investment-part-2

# REST API(REpresentational State transfer API)의 개념

- 전송 과정에서 view를 구현하지 않고, 데이터만 전송하는 API
- URI를 통해 리소스(대상)를, HTTP method를 통해 행위를 표현
- HTTP 표준 전송 규약을 따르므로, 다양한 플랫폼에 높은 이식성을 가짐
- HTTP 캐싱을 지원하므로, 클라이언트는 이전의 응답 데이터를 재사용할 수 있음
- 데이터만 전송함으로써 명확한 계층 구조를 이루고 관심사의 분리가 이루어짐
  - 클라이언트와 서버 간 결합도를 낮추고 각자의 영역에 집중할 수 있음
- 클라이언트의 상태를 저장하지 않는 무상태성을 가지므로, 항상 완전한 요청이 필요
  - 로그인 서비스 등 지속적인 상태 저장이 필요한 영역에 취약한 편
- 별도의 view 없이 데이터만 전달하므로, 데이터만 사용하는 MSA의 서비스 애플리케이션 간 상호작용에 적합

# MSA의 서비스 간 통신 방법

## REST API 이용

### 장점

- 단순하고 직관적인 API 제공
  - 서버의 구조와 프로세스에 대한 이해 없이도 클라이언트가 서버를 이용할 수 있음
- HTTP를 사용하므로, 이미 구축되어 있는 웹 인프라를 사용할 수 있음
- HTTP 표준 전송 규약을 따르므로, 개발 언어, 플랫폼, 기술 등의 제약을 받지 않고 높은 호환성을 가짐
- 다양한 데이터 포맷 지원(하지만 대체로 JSON을 사용)
- 웹 브라우저와의 호환성 높음 —> 웹 기반 클라이언트에서 이용 시 유리

### 단점

- 서비스 간 대량의 데이터를 주고받는 경우, HTTP와 JSON 간 오버헤드(작업에 소요되는 추가 자원, 시간)가 커질 수 있음
- 실시간 데이터 전송에 불리
- 타입이 명시적으로 정의되지 않아, 다양한 언어로 개발된 서비스 간 통신에서 특히 취약함
  - 타입 불일치나 유효하지 않은 값의 전송을 실행 시간에 발견할 가능성이 높아짐

## gRPC(Gogle Remote Procedure Call) 이용

### 장점

- 자체 제공 언어인 proto를 통해, 직렬화(포맷 간 변환)를 효율적으로 할 수 있음 —> 성능 향상
- 빠른 응답 속도를 기반으로 실시간 데이터 전송에 유리
- 강력한 타입 검사 기능을 가져, 대부분의 서비스 간 호환 문제를 컴파일 시간에 발견할 수 있음
  - 특히 타입 검사 과정에서 취약점을 보이는 동적 타입 언어의 데이터 전송 시 강점을 보임
- 서비스마다 다양한 언어를 사용해도 비교적 안정적

### 단점

- proto 외의 다른 데이터 포맷을 사용하기 어려움
- 웹 브라우저와의 호환성 낮음
- REST API에 비해 익히기 어려움(proto 언어 학습 필요)

# REST API를 통한 서비스 간 통신 예시

## Monolithic **Architecture**

### OrderController(내부의 OrderService에서 Menu 엔티티 조회)

```java
@RestController
@RequestMapping("shops/{shopId}/orders")
@RequiredArgsConstructor
public class OrderController {

    private final OrderService orderService;

    @PostMapping()
    public ResponseEntity<OrderResponseDto> createOrder
            (@PathVariable Long shopId,
             @Validated(OnCreate.class) @RequestBody OrderRequestDto orderRequestDto) {

        OrderResponseDto orderResponseDto = orderService.takeOrder(orderRequestDto);

        URI location = ServletUriComponentsBuilder
                .fromCurrentRequest()
                .path("/{id}")
                .buildAndExpand(orderResponseDto.getId())
                .toUri();

        HttpHeaders headers = new HttpHeaders();
        headers.setLocation(location);

        return ResponseEntity.created(location).headers(headers).body(orderResponseDto);

    }

}
```

## MicroService Architecture(단일 메뉴 주문)

### application.properties 또는 application.yml(Menu 정보를 받아 올 경로 지정)

```java
menu-service.url=http://menu-service/
```

### 클라이언트 요청(JSON)

```java
{
    "memberId": 3,
    "menuIdAndQuantityMap": {
        "2": 4
    },
    "orderRequest": "맛있게 조리해 주세요.",
    "deliveryRequest": "안전하게 배달해 주세요."
}
```

### MenuController(Menu 정보 반환)

```java
@RestController
@RequestMapping("shops/{shopId}/menus")
@RequiredArgsConstructor
public class ShopMenuController {

  @GetMapping("{id}")
  public ResponseEntity<MenuResponseDto> getMenu(@PathVariable Long id) {

    MenuResponseDto menuResponseDto = menuService.getMenu(id);

    return ResponseEntity.status(HttpStatus.OK).body(menuResponseDto);

  }

}
```

### OrderController(외부의 MenuController에서 Menu 정보 받아 옴)

```java
@RestController
@RequestMapping("shops/{shopId}/orders")
@RequiredArgsConstructor
public class OrderController {

    private final OrderService orderService;

    @Value("${menu-service.url}")
    private String menuServiceUrl;

    private final WebClient webClient = WebClient.create();

    @PostMapping()
    public ResponseEntity<OrderResponseDto> createOrder
            (@PathVariable Long shopId,
             @Validated(OnCreate.class) @RequestBody OrderRequestDto orderRequestDto) {

        Long menuId = orderRequestDto.getMenuIdAndQuantityMap()
                .keySet().iterator().next();

        String url = menuServiceUrl + shopId + "/menus/" + menuId;

        MenuResponseDto menuResponseDto = webClient.get()
                .uri(url)
                .retrieve()
                .bodyToMono(MenuResponseDto.class)
                .block();

        OrderResponseDto orderResponseDto
                = orderService.takeOrder(orderRequestDto, menuResponseDto);

        URI location = ServletUriComponentsBuilder
                .fromCurrentRequest()
                .path("/{id}")
                .buildAndExpand(orderResponseDto.getId())
                .toUri();

        HttpHeaders headers = new HttpHeaders();
        headers.setLocation(location);

        return ResponseEntity.created(location).headers(headers).body(orderResponseDto);

    }

}
```

## Micro Service **Architecture(여러 메뉴 주문)**

### application.properties 또는 application.yml(Menu 정보를 받아 올 경로 지정)

```java
menu-service.url=http://menu-service/
```

### 클라이언트 요청(JSON)

```java
{
    "memberId": 3,
    "menuIdAndQuantityMap": {
        "2": 4,
        "3": 2,
        "5": 1
    },
    "orderRequest": "맛있게 조리해 주세요.",
    "deliveryRequest": "안전하게 배달해 주세요."
}
```

### MenuController(여러 Menu 정보 반환)

```java
@RestController
@RequestMapping("shops/{shopId}/menus")
@RequiredArgsConstructor
public class ShopMenuController {

    @GetMapping()
    public ResponseEntity<List<MenuResponseDto>> getMenuListToOrder(@RequestParam List<Long> ids) {
        
        List<MenuResponseDto> menuResponseDtoList = menuService.getMenuListToOrder(ids);

        return ResponseEntity.status(HttpStatus.OK).body(menuResponseDtoList);

    }

}
```

### OrderController(외부의 MenuController에서 여러 Menu 정보 받아 옴)

```java
@RestController
@RequestMapping("shops/{shopId}/orders")
@RequiredArgsConstructor
public class OrderController {

    private final OrderService orderService;

    @Value("${menu-service.url}")
    private String menuServiceUrl;

    private final WebClient webClient = WebClient.create();

    @PostMapping
    public ResponseEntity<OrderResponseDto> createOrder
            (@PathVariable Long shopId,
             @Validated(OnCreate.class) @RequestBody OrderRequestDto orderRequestDto) {

        String menuIds = getMenuIdsAsString(orderRequestDto);

        List<MenuResponseDto> menuResponseDtoList =
                getMenuListFromMenuService(shopId, menuIds);

        OrderResponseDto orderResponseDto =
                orderService.takeOrder(orderRequestDto, menuResponseDtoList);

        return buildResponse(orderResponseDto);

    }

    private String getMenuIdsAsString(OrderRequestDto orderRequestDto) {

        Set<Long> menuIdSet = orderRequestDto.getMenuIdAndQuantityMap().keySet();

        return String.join(",", menuIdSet.stream().map(Object::toString)
                .collect(Collectors.toList()));

    }

    private List<MenuResponseDto> getMenuListFromMenuService(Long shopId, String menuIds) {

        String url = menuServiceUrl + shopId + "/menus?ids=" + menuIds;

        return webClient.get()
                .uri(url)
                .retrieve()
                .bodyToFlux(MenuResponseDto.class)
                .collectList()
                .block();

    }

    private ResponseEntity<OrderResponseDto> buildResponse
            (OrderResponseDto orderResponseDto) {

        URI location = ServletUriComponentsBuilder
                .fromCurrentRequest()
                .path("/{id}")
                .buildAndExpand(orderResponseDto.getId())
                .toUri();

        HttpHeaders headers = new HttpHeaders();
        headers.setLocation(location);

        return ResponseEntity.created(location).headers(headers).body(orderResponseDto);

    }

    @PatchMapping("{id}")
    public ResponseEntity<OrderResponseDto> changeOrderStatus
            (@PathVariable Long id, @Valid @RequestBody OrderStatusChangeDto orderStatusChangeDto) {

        OrderResponseDto orderResponseDto = orderService.changeOrderStatus(id, orderStatusChangeDto);

        return ResponseEntity.status(HttpStatus.OK).body(orderResponseDto);

    }

}
```
