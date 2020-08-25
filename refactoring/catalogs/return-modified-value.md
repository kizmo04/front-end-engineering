# 11.11 수정된 값 반환하기 (Return Modified Value)

데이터가 변수에 저장되어있고 다른 함수가 해당 변수에 접근해 값을 수정할 때, 함수가 변수에 접근하지 않고 수정되는 값을 반환시키게 해 변수의 값이 수정되는 부분을 명확히 분리시키는 리팩토링.

## 🗣 설명

### 🧐 As is

```js
let paymentRequiredCash = 0;
calculatePaymentRequiredCash();

function calculatePaymentRequiredCash() {
  for (let history of order.paymentHistories) {
    if (history.method !== PaymentMethod.CASH) {
      continue;
    }
    paymentRequiredCash += (history.type === PaymentType.DEPOSIT ? 1 : -1) * history.amount;
  }
}
```

### 😍 To be

```js
const paymentRequiredCash = calculatePaymentRequiredCash();

function calculatePaymentRequiredCash() {
  let paymentRequiredCash = 0;

  for (let history of order.paymentHistories) {
    if (history.method !== PaymentMethod.CASH) {
      continue;
    }
    paymentRequiredCash += (history.type === PaymentType.DEPOSIT ? 1 : -1) * history.amount;
  }

  return paymentRequiredCash;
}
```

### 📋 상세

가변형 변수의 가장 큰 단점은 해당 변수의 scope가 커지면 커질수록 어디서 이 값이 추적되는지 알기 힘들다는 것이다. 데이터 변경 흐름의 추적은 안그래도 어려운 일인데, 변수를 수정하는 함수가 여기저기 흩어져 있다면 코드를 이해하는 것은 더욱 어렵다.
이를 해결하기 위해 데이터가 갱신되는 로직을 작은 함수에 넣고, 값의 갱신이 끝날 때 값을 반환하게 하면 데이터 변경 흐름 추적이 조금 더 쉬워진다.

이 리팩터링은 값 하나를 계산하는 분명한 목적이 있는 함수에는 효과적이나, 여러 개를 갱신하는 함수에는 효과적이지 않다. 다른 리팩터링(함수 옮기기)의 준비 작업으로도 좋다.

### ⚙️ 절차

1. 함수가 수정된 값을 반환하게 하여 호출자가 그 값을 자신의 변수에 저장하게 하고, 테스트한다.
2. 피호출 함수 안에 반환할 값을 가리키는 새로운 변수를 선언하고, 테스트한다.
    * 이 값이 의도대로 이뤄졌는지 검사하고 싶으면 호출자에서 초깃값을 수정해본다. 제대로 됐으면 수정이 무시될 것이다.
3. 계산이 선언과 동시에 이뤄지도록 통합하고, 테스트한다.
    * 변수 선언에 함수 실행을 대입하라는 의미.
    * 언어가 지원하면 변수를 불변으로 지정한다.
4. 피호출 함수의 변수 이름을 새 역할에 어울리도록 바꾸고, 테스트한다.

## 📝 메모

### 😍 반복문을 파이프라인으로 변경해 변수를 완전히 제거한 To be

작은 함수로 데이터 변경 로직을 몰아넣는다면, 함수의 수정 방향이 명확하게 보이기도 한다. 실제로 위의 예제는 다른 리팩토링을 적용해 이렇게 변경하여 변수를 완전히 제거할 수 있다.

```js
const paymentRequiredCash = calculatePaymentRequiredCash();

function calculatePaymentRequiredCash() {
  return order.paymentHistories
    .filter(history => history.method === PaymentMethod.CASH)
    .reduce(
      (sum, history) =>
        sum += (history.type === PaymentType.DEPOSIT ? 1 : -1) * history.amount
      , 0
    );
}
```
