# 11.4 객체 통째로 넘기기(Preserve Whole Object)

레코드를 통째로 넘겨 함수를 변화에 대응하기 쉽도록 변경하는 방법

## 🗣 설명

### 🧐 As is

```typescript
function withinRange(bottom: number, top: number) {
  return bottom >= temperatureRange.low && top <= temperatureRange.high;
}

const { low, high } = daysTempRange;
if (withinRange(low, high)) {
  alerts.push("방 온도가 지정 범위를 벗어났습니다.");
}
```

### 😍 To be

```typescript
function withinRange(tempRange: object) {
  const { low, high } = tempRange;
  return bottom >= low && top <= high;
}

if (!withinRange(daysTempRange)) {
  alerts.push("방 온도가 지정 범위를 벗어났습니다.");
}
```

### 📋 상세

레코드를 통째로 넘기면 변화에 대응하기 쉽다.  
예컨대 그 함수가 더 다양한 데이터를 사용하도록 바뀌어도 매개변수 목록은 수정할 필요가 없다.  
그리고 매개변수 목록이 짧아져서 일반적으로는 함수 사용법을 이해하기 쉬워진다.

### ⚙️ 절차

1. 매개변수들을 원하는 형태로 받는 빈 함수를 만든다.  
   → 마지막 단계에서 이 함수의 이름을 변경해야 하니 검색하기 쉬운 이름으로 지어준다.
2. 새 함수의 본문에서는 원래 함수를 호출하도록 하며, 새 매개변수와 원래 함수의 매개변수를 매핑한다.
3. 정적 검사를 수행한다.
4. 모든 호출자가 새 함수를 사용하게 수정한다. 하나씩 수정하며 테스트하자.  
   → 수정 후에는 원래의 매개변수를 만들어내는 코드 일부가 필요 없어질 수 있다. 따라서 죽은 코드 제거하기로 없앨 수 있다.
5. 호출자를 모두 수정했다면 원래 함수를 인라인한다.
6. 새 함수의 이름을 적절히 수정하고 모든 호출자에 반영한다.

## 📝메모

하지만 함수가 레코드 자체에 의존하기를 원치 않을 때는 이 리팩터링을 수행하지 않는데, 레코드와 함수가 서로 다른 모듈에 속한 상황이면 특히 더 그렇다.
