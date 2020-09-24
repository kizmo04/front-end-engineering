# 11.12 오류 코드를 예외로 바꾸기(Replace Error Code with Exception)

프로그램의 정상 동작 범주에 들지 않는 오류를 나타내고 싶을 때 사용하는 리팩터링

## 🗣 설명

### 🧐 As is

```typescript
function localShippingRules(country: string) {
  const data = countryData.shippingRules[country];
  if (data) return new ShippingRules(data);
  else return -23;
}

function calculateShippingCosts(anOrder: object) {
  // 관련 없는 로직
  const shippingRules = localShippingRules(anOrder.country);
  if (shippingRules < 0) return shippingRules; // 오류 전파
  // 관련 없는 로직
}

const status = calculateShippingCosts(orderData);
if (status < 0) errorList.push({ order: orderData, errorCode: status });
```

### 😍 To be

```typescript
function localShippingRules(country: string) {
  const data = countryData.shippingRules[country];
  if (data) return new ShippingRules(data);
  else throw new OrderProceessingError(-23);
}

function calculateShippingCosts(anOrder: object) {
  // 관련 없는 로직
  const shippingRules = localShippingRules(anOrder.country);
  // 관련 없는 로직
}

try {
  calculateShippingCosts(orderData);
} catch (e) {
  if (e instanceof OrderProcessingError)
    errorList.push({ order: orderData, errorCode: status });
  else throw e;
}
```

### 📋 상세

예외는 프로그래밍 언어에서 제공하는 독립적인 오류 처리 메커니즘이다. 오류가 발견되면 예외를 던진다. 그러면 적절한 예외 핸들러를 찾을 때까지 콜스택을 타고 위로 전파된다. 예외를 사용하면 코드를 일일이 검사하거나 오류를 식별해 콜스택 위로 던지는 일을 신경쓰지 않아도 된다.
예외는 정교한 메커니즘이지만 대다수의 다른 정교한 메커니즘과 같이 정확하게 사용할 때만 최고의 효과를 낸다. 예외는 정확히 예상 밖의 동작일 때만 쓰여야 한다.

### ⚙️ 절차

1. 콜스택 상위에 해당 예외를 처리할 예외 핸들러를 작성한다.

   → 이 핸들러는 처음에는 모든 예외를 다시 던지게 해둔다.

   → 적절한 처리를 해주는 핸들러가 이미 있다면 지금의 콜스택도 처리할 수 있도록 확장한다.

2. 테스트한다.
3. 해당 오류 코드를 대체할 예외와 그 밖의 예외를 구분할 식별 방법을 찾는다.

   → 사용하는 프로그래밍 언어에 맞게 선택하면 된다. 대부분 언어에서는 서브클래스를 사용하면 될 것이다.

4. 정적 검사를 수행한다.
5. catch절을 수정하여 직접 처리할 수 있는 예외는 적절히 대처하고 그렇지 않은 예외는 다시 던진다.
6. 테스트한다.
7. 오류 코드를 반환하는 곳 모두에서 예외를 던지도록 수정한다. 하나씩 수정할 때마다 테스트한다.
8. 모두 수정했다면 그 오류 코드를 콜스택 위로 전달하는 코드를 모두 제거한다. 하나씩 수정할 때마다 테스트한다.

   → 먼저 오류 코드를 검사하는 부분을 함정으로 바꾼 다음, 함정에 걸려들지 않는지 테스트한 후 제거하는 전략을 권한다. 함정에 걸려드는 곳이 있다면 오류 코드를 검사하는 코드가 아직 남아있다는 뜻이다. 함정을 무사히 피했다면 안심하고 본문을 정리하자.

## 📝메모

저자는 예외 메커니즘을 사용하려면 예상 밖의 동작일 때에만 사용해야 한다고 강조한다.
즉, 정상 동작 범주에 드는 오류를 처리할 떄는 예외 대신 오류를 검출하여 프로그램을 정상 흐름으로 되돌리게끔 처리해야 한다.
