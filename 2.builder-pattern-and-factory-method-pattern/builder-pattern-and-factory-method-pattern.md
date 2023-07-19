# 빌더 패턴 & 팩토리 메서드 패턴

## 객체 생성 관련 대표적인 디자인 패턴

- 생성자 패턴: 생성자를 통해 객체를 생성
- 자바 빈 패턴: 객체 상태의 초기화 없이 객체 생성 후, setter를 통해 상태를 초기화/변경
- 프로토타입 패턴: 기존 객체를 복제해 새로운 객체를 생성
- 빌더 패턴: 객체 생성을 한 번에 하지 않고 순차적으로 진행
- 팩토리 메서드 패턴: 객체 생성 전용 인터페이스를 통해 새로운 객체를 생성
- 추상 팩토리 패턴: 여러 팩토리 메서드들을 모아, 여러 객체 간 연관된 객체 생성을 관리

## 객체 상태 관리 방법

### 객체의 상태를 직접 변경하는 방식

- field의 접근제한자를 public으로 선언
- 매우 편리하게 원하는 값을 초기화/변경/조회 가능
- 의도치 않은 객체 상태 변경에 매우 취약함
- 유효성 검사를 수행할 수 없음 —> 객체가 유효하지 않은 상태를 가질 위험성 내포

### 자바 빈 패턴(JavaBeans Pattern)

- 가장 기본이 되는 패턴이자, 대부분의 프레임워크 작동 시 개발자가 사용할 것으로 가정되는 패턴(자바 빈 패턴을 기반으로 작동)
- 다루기 편리하고 상황 변화에 유연함
- 객체의 불변성을 유지하기 어려움
- 객체 상태의 예측 가능성이 떨어짐
- 멀티 스레드 환경에서 특히 취약
- 상태 변경의 파급효과가 큰 Entity에 부적합
- 데이터 전송용 객체(DTO)에 적합

### 불변 객체(Immutable Object)

#### 불변 객체의 정의

- 생성 후 상태를 변경할 수 없는 객체

#### 불변 객체가 필요한 이유

- 같은 프로젝트를 진행하는 10명의 개발자가 각자의 코드에서 객체를 생성하고, 상태를 변경하고, 그 상태를 조회하고, 조회된 상태를 기반으로 다른 객체의 작업을 수행한다면?

#### 불변 객체의 조건

- 모든 field를 final로 선언
    - JPA를 사용할 경우, 실행 시간에 동적으로 Entity 클래스의 proxy를 생성해야 하기 때문에 적용 불가
    - 클래스의 모든 field의 접근 제한자를 private으로 선언하고, getter를 제외한 모든 method에서 새로운 객체를 반환하는 경우 적용 예외 —> 캡슐화로 해결
- 클래스를 final로 선언
    - 클래스의 모든 field의 접근 제한자를 private으로 선언하고, getter를 제외한 모든 method에서 새로운 객체를 반환하는 경우 적용 예외 —> 캡슐화로 해결
- setter를 제공하지 않음
- 객체가 가진 field의 참조 객체가 변경되지 않도록 관리(가변 객체의 주소를 field로 가질 수 없음) —> 현실적으로 어려운 이유
- 해당 클래스의 모든 method는 기존 상태를 변경하지 않고 항상 새로운 객체를 반환

#### 불변 객체를 생성하는 대표적인 디자인 패턴

- 빌더 패턴
- 팩토리 메서드 패턴

## 빌더 패턴(Builder Pattern)

### 특징

- 객체를 한 번에 생성하지 않고 순차적으로 생성함
- 대상 객체의 초기화할 field가 많은 경우, 객체 생성 과정이 복잡한 경우, 객체의 각 field마다 추가 로직이 필요한 경우 적합
- 주로 메서드 체이닝을 이용 —> 코드가 깔끔하고 가독성이 좋음
- 초기화할 필드명과 그에 대한 대입값을 명시함으로써 객체 생성 과정의 이해가 쉽고 명확함
- 자동 오버로딩을 통해 매개변수를 필요한 만큼 자유롭게 선택해서 객체 생성 가능
- 매개변수의 순서에 구애받지 않기 때문에 대입 순서를 지킬 필요가 없음
- 생성 대상 객체의 구조를 파악할 필요가 없기 때문에, 객체 간 결합도를 낮추고 객체의 자율성을 높임
- 불변성을 유지하기 어려운 PATCH 메서드의 데이터 일부 속성 수정 과정에서 강점을 보임
- 객체 생성 과정이 장황하기 때문에, 간단한 객체 생성에 적용하면 오버 엔지니어링의 가능성이 있음

### 예시 코드

- Lombok에서 제공하는 @Builder 어노테이션을 통해 Builder 클래스 자동 생성

```java
@Builder
public Menu(Long id, Shop shop, String name, Integer price, Integer salesRate, Integer stock,
            String flavor, Integer portions, Integer cookingTime, String menuType,
            String foodType, Boolean isPopularMenu, List<String> menuOptions,
            LocalDateTime registrationTime, LocalDateTime updateTime) {

    this.id = id;
    this.shop = shop;
    this.name = name;
    this.price = price;
    this.salesRate = salesRate;

    this.stock = stock;

    adjustStockState();

    this.flavor = flavor;
    this.portions = portions;
    this.cookingTime = cookingTime;
    this.menuType = menuType;
    this.foodType = foodType;
    this.isPopularMenu = isPopularMenu;
    this.menuOptions = menuOptions;
    this.registrationTime = registrationTime;
    this.updateTime = updateTime;

}
```

- 데이터의 일부 속성 수정 시 적용

```java
@Override
public MenuResponseDto changeMenu(Long id, MenuRequestDto menuRequestDto) throws Exception {

    Menu menu = menuRepository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("해당 메뉴를 찾을 수 없습니다. (menuId: " + id + ")"));

    int modifiedStock = menu.getStock() + menuRequestDto.getStock();

    if (modifiedStock < 0) {
        throw new OutOfStockException("재료 최소 수량: 0 (현재 재고: " + menu.getStock() + ")");
    }

    Menu changingMenu = Menu.builder()
            .id(id)
            .shop(menu.getShop())
            .name(menuRequestDto.getName() != null ? menuRequestDto.getName() : menu.getName())
            .price(menuRequestDto.getPrice() > 0 ? menuRequestDto.getPrice() : menu.getPrice())
            .salesRate(menuRequestDto.getSalesRate() == -1 ? 0 : menu.getSalesRate())
            .stock(modifiedStock)
            .flavor(menuRequestDto.getFlavor() != null ? menuRequestDto.getFlavor() : menu.getFlavor())
            .portions(menuRequestDto.getPortions() > 0 ? menuRequestDto.getPortions() : menu.getPortions())
            .cookingTime(menuRequestDto.getCookingTime() > 0 ? menuRequestDto.getCookingTime() : menu.getCookingTime())
            .menuType(menuRequestDto.getMenuType() != null ? menuRequestDto.getMenuType() : menu.getMenuType())
            .foodType(menuRequestDto.getFoodType() != null ? menuRequestDto.getFoodType() : menu.getFoodType())
            .menuOptions(menuRequestDto.getMenuOptions() != null ? menuRequestDto.getMenuOptions() : menu.getMenuOptions())
            .registrationTime(menu.getRegistrationTime())
            .updateTime(LocalDateTime.now())
            .build();

    Menu changedMenu = menuRepository.save(changingMenu);

    MenuResponseDto menuResponseDto = beanConfiguration.modelMapper()
            .map(changedMenu, MenuResponseDto.class);

    return menuResponseDto;

}
```

- 객체 생성 도중 추가 로직 삽입

```java
@Override
public MenuResponseDto changeMenu(Long id, MenuRequestDto menuRequestDto) throws Exception {

    Menu menu = menuRepository.findById(id)
            .orElseThrow(() -> new EntityNotFoundException("해당 메뉴를 찾을 수 없습니다. (menuId: " + id + ")"));

    MenuBuilder menuBuilder = Menu.builder()
            .id(id)
            .shop(menu.getShop())
            .name(menuRequestDto.getName() != null ? menuRequestDto.getName() : menu.getName())
            .price(menuRequestDto.getPrice() > 0 ? menuRequestDto.getPrice() : menu.getPrice())
            .salesRate(menuRequestDto.getSalesRate() == -1 ? 0 : menu.getSalesRate());

    int modifiedStock = menu.getStock() + menuRequestDto.getStock();

    if (modifiedStock < 0) {
        throw new OutOfStockException("재료 최소 수량: 0 (현재 재고: " + menu.getStock() + ")");
    }

    menuBuilder.stock(modifiedStock);

    Menu changingMenu = menuBuilder
            .flavor(menuRequestDto.getFlavor() != null ? menuRequestDto.getFlavor() : menu.getFlavor())
            .portions(menuRequestDto.getPortions() > 0 ? menuRequestDto.getPortions() : menu.getPortions())
            .cookingTime(menuRequestDto.getCookingTime() > 0 ? menuRequestDto.getCookingTime() : menu.getCookingTime())
            .menuType(menuRequestDto.getMenuType() != null ? menuRequestDto.getMenuType() : menu.getMenuType())
            .foodType(menuRequestDto.getFoodType() != null ? menuRequestDto.getFoodType() : menu.getFoodType())
            .menuOptions(menuRequestDto.getMenuOptions() != null ? menuRequestDto.getMenuOptions() : menu.getMenuOptions())
            .registrationTime(menu.getRegistrationTime())
            .updateTime(LocalDateTime.now())
            .build();

    Menu changedMenu = menuRepository.save(changingMenu);

    MenuResponseDto menuResponseDto = beanConfiguration.modelMapper()
            .map(changedMenu, MenuResponseDto.class);

    return menuResponseDto;

}
```

## 정적 팩토리 메서드 패턴(Static Factory Method Pattern)

### 특징

- 팩토리 클래스를 통해 팩토리 메서드들을 관리하는 팩토리 메서드 패턴에 비해 객체 생성 로직이 단순하고, 오버로딩이 적은 경우에 적합
- 객체 생성 로직이 복잡하거나 오버로딩이 많다면, 빌더 패턴을 사용하거나 팩토리 메서드 패턴을 통해 별도의 팩토리 클래스에 모아서 관리
- 대체로 생성자를 통해 객체의 상태를 변경하지 못하도록 생성자를 private으로 선언(싱글톤 패턴과 유사)
    - 생성자를 private으로 제한할 수 없는 빌더 패턴과 팩토리 메서드 패턴에 비해 강점을 가짐(무분별한 객체 생성 방지)
- 객체의 생성 권한을 클라이언트에게 맡기지 않고 해당 클래스 스스로 제어 —> 캡슐화를 통해 객체 간 결합도를 낮추고 객체의 자율성을 높임
- 객체를 생성할 서브타입을 실행시간에 결정할 수 있으므로, 객체 생성의 유연성 증가
- 메서드의 명칭에서 역할이 명확히 드러나기 때문에, 생성자 패턴에 비해 가독성이 좋고 메서드의 역할 이해가 명확해짐
- 빌더 패턴에 비해 일부 속성 변경 로직에 취약하고, 객체 초기화에 필요한 매개변수가 많아지면 가독성이 급격히 떨어짐(읽는 사람마다 다를 수 있으나, 개발자들의 경험 통계적으로 매개변수가 4개 이하인 경우에 대체로 적합하다는 평)

### 예시 코드

- 객체 생성을 위해 createOrderMenu 메서드 호출

```java
public List<OrderMenu> createOrderMenu(Map<Menu, Integer> menuToOrderMap, Order order) {

    List<OrderMenu> orderMenuList = new ArrayList<>();

    for (Menu menuToOrder : menuToOrderMap.keySet()) {

        OrderMenu orderMenu = OrderMenu.createOrderMenu
                (menuToOrder, order, menuToOrder.getPrice(), menuToOrderMap.get(menuToOrder));

        orderMenuList.add(orderMenu);

        menuToOrder.useStockForSale(orderMenu.getOrderQuantity());

        order.computeTotalOrderPrice(orderMenu.getTotalMenuPrice());

    }

    return orderMenuList;

}
```

- 직접 객체를 생성 후 반환

```java
private OrderMenu(Menu menu, Order order, Integer menuPrice, Integer orderQuantity) {

    this.menu = menu;
    this.order = order;
    this.menuPrice = menuPrice;
    this.orderQuantity = orderQuantity;
    totalMenuPrice = orderPrice * orderQuantity;

}

 public static OrderMenu createOrderMenu(Menu menu, Order order, Integer menuPrice, Integer orderQuantity) {
    return new OrderMenu(menu, order, menuPrice, orderQuantity);
}
```