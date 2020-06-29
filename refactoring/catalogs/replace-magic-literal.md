# 매직 리터럴 바꾸기 (Replace Magic Literal)

매직 리터럴이란 소스 코드에 등장하는 일반적인 리터럴 값.

## 🗣 설명

### 🧐 As is

```js
function potentialEnergy(mess, height) {
  return mass * 9.81 * height;
}
```

### 😍 To be

```js
const STANDARD_GRAVITY = 9.81;
function potentialEnergy(mess, height) {
  return mass * STANDARD_GRAVITY * height;
}
```

### 📋 상세

- 소스코드에 사용 된 코드만으로 의미가 불분명한 매직 리터럴을 상수로 정의하고 그 상수를 사용하도록 리팩토링 한다.
- 상수가 특별한 로직에 쓰이는 경우에는 함수로 바꿀 수 있다.
```js
  if(aValue === "M")

  ->
  const MALE_GENDER = "M";
  if(aValue === MALE_GENDER)

  ->
  if(isMale(aValue)) 
```
- 값자체로 의미를 전달 할 수 있거나, 값이 달라질 가능성이 없는 경우 그리고 리터럴이 함수 하나에서만 쓰이면서 맥락 정보를 제공 할 수 있다면 굳이 상수로 바꾸지 않는다.

### ⚙️ 절차

1. 상수를 선언하고 매직 리터럴을 대입한다.
2. 해당 리터럴이 사용되는 곳을 모두 찾는다.
3. 찾은 곳 각각에서 리터럴이 새 상수와 똑같은 의미로 쓰였는지 확인하여, 같은 의미라면 상수로 대체 한 후 테스트한다.
