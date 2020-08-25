# 7.5 클래스 추출하기 (Extract Class)

큰 클래스에서 역할에 따라 일부 기능을 담당하는 클래스를 분리하는 것

## 🗣 설명

### 🧐 As is

```js
class Delivery {
  get zipCode() {
    return this._zipCode;
  }
  get baseAddress() {
    return this._baseAddress;
  }
  get detailAddress() {
    return this._detailAddress;
  }
}
```

### 😍 To be

```js
class Delivery {
  get zipCode() {
    return this._address.zipCode;
  }
  get baseAddress() {
    return this._address.baseAddress;
  }
  get detailAddress() {
    return this._address.detailAddress;
  }
}

class Address {
  get zipCode() {
    return this._zipCode;
  }
  get baseAddress() {
    return this._baseAddress;
  }
  get detailAddress() {
    return this._detailAddress;
  }
}
```

### 📋 상세

메서드와 데이터가 적은 클래스는 이해하기 쉽다. 이해하기 힘들고 복잡한 클래스를 단순화하고, 의존성을 명확하게 정리할 수 있다. 다음과 같을 때 이 리팩터링을 고려해볼 수 있다.

- 메서드와 데이터가 너무 많을 때
- 일부 데이터와 메서드를 따로 묶을 수 있을 때
  - 함께 변경되는 일이 많거나, 서로 의존하는 데이터들을 분리한다.
  - 제거해도 다른 필드나 메서드들이 논리적으로 문제가 없는 것은 분리한다.
- 서브클래스를 만들 때, 다음과 같은 신호가 있다면 분리하는 것이 좋다.
  - 작은 일부 기능만을 위해 서브클래스를 만들어야 할 때
  - 확장해야 할 기능이 무엇인지에 따라 서브클래스를 만드는 방식이 달라질 때

### ⚙️ 절차

1. 클래스의 역할을 분리할 방법을 정하고, 분리된 역할을 담당할 새 클래스를 만든다.
2. 원래 클래스의 생성자에서 새로운 클래스의 인스턴스를 생성해 필드에 저장한다.
3. 분리될 역할에 필요한 필드를 새 클래스로 옮기고 테스트한다<sup>1)</sup>.
4. 메서드들을 새 클래스로 옮기고 테스트한다<sup>2)</sup>. 이때 저수준 메서드(다른 메서드에게 호출을 많이 당하는 메서드)부터 옮긴다.
5. 양쪽 클래스의 인터페이스에서 불필요한 메서드를 제거하고 이름을 변경한다.
6. 새 클래스를 외부로 노출할지 정한다. 노출할 경우 새 클래스에 참조를 값으로 바꾸기<sup>3)</sup>를 적용할지 고민해본다.

## 📝 메모

- 메쉬원에는 이 리팩토링을 적용해야 하는 것들이 굉장히 많다
  - 리스트와 상세: 둘은 다른 모델을 다루고 있고, 관심사도 정확히 분리된다
  - 거대해진 뷰모델 스토어: 뷰모델 스토어에서 다루는 기능들을 하나로 뭉쳐서 쓰지 않는다면(ex. 탭이 다르거나, 모달이 새로 열리거나, ...) 분리하는 것이 낫다.

### 📖 참고

1) 8.2 필드 옮기기: 특정 값을 다른 필드의 하위값으로 옮기기
2) 8.1 함수 옮기기: 특정 메서드를 다른 메서드로 옮기기
3) 9.4 참조를 값으로 바꾸기: 필드가 immutable한 것이 좋을 때, 값을 setter가 참조를 통해 바꾸는 것이 아니라 새로 선언해 저장하게 하는 것
