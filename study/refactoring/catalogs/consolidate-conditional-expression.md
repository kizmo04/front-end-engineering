# 10.2 조건식 통합하기 (Consolidate Conditional Expression)

비교하는 조건이 다르지만 그 결과로 수행되는 동작이 같은 코드가 있는 경우, 조건 검사를 하나로 통합해 (6.1 함수 추출하기를 사용하면 좋다) 하려는 일의 의도를 더 명확히 하는 기법.

## 🗣 설명

### 🧐 As is

```js
if (anEmployee.seniority < 2) return 0;
if (anEmployee.monthsDisabled > 12) return 0;
if (anEmployee.isPartTime) return 0;
```

### 😍 To be

```js
if (isNotElligibleForDisability()) return 0;

function isNotElligibleForDisability() {
  return (
    anEmployee.seniority < 2 ||
    anEmployee.monthsDisabled > 12 ||
    anEmployee.isPartTime
  );
}
```

### 📋 상세

'and' 또는 'or'을 사용하면 여러개의 비교 로직을 하나로 합칠 수 있다.
이 기법이 중요한 이유는 두가지이다.
첫번째, 조건들을 통합함으로서 내가 하려는 일이 더욱 명확해진다.
두번째, 이 작업이 함수 추출하기까지 이어질 가능성이 높기 때문이다. (복잡한 조건식읠 함수로 추출하면 코드의 의도가 훨씬 분명하게 드러나는 경우가 많다. '무엇'을 하는지를 넘어서 '왜' 하는지를 표현함)
반면, 하나의 검사라고 생각할 수 없는, 진짜로 독립된 검사들이라고 판단되면 이 리팩토링을 해서는 안 된다.

### ⚙️ 절차

1. 해당 조건식들 모두에 부수효과가 없는지 확인한다.
   (부수효과가 있는 조건식들에는 11.1 질의 함수와 변경 함수 분리하기를 먼저 사용한다.)
2. 조건문 두개를 선택하여 두 조건문의 조건식들을 논리 연산자로 결합한다.
   (순차적으로 이뤄지는 조건문은 or로 결합하고, 중첩된 조건문은 and로 결합한다.)
3. 테스트한다.
4. 조건이 하나만 남을때까지 2~3 과정을 반복한다.
5. 하나로 합쳐진 조건식을 함수로 추출할지 고려해본다.

## 📝 메모

### 🧐 As is (&& 사용)

```js
if (anEmployee.onVacation) {
  if (anEmployee.seiority > 10) return 1;
}
return 0.5;
```

### 😍 To be (&& 사용)

```js
if (anEmployee.onVacation && anEmployee.seniority > 10) return 1;
return 0.5;
```
