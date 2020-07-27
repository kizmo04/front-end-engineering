# 11.7 세터 제거하기 (Remove Setting Method)

객체 생성 후 수정하기를 원하지 않는 필드의 세터를 제거해서, 수정하지 않겠다는 의도를 명백하게 밝히는 기법.

## 🗣 설명

### 🧐 As is

```js
class Person {
  constructor() {}
  get name() {
    return this._name;
  }
  set name(arg) {
    this._name = arg;
  }
  get id() {
    return this._id;
  }
  set id(arg) {
    this._id = arg;
  }
}
```

### 😍 To be

```js
class Person {
  constructor(id) {
    this._id = id;
  }
  get name() {
    return this._name;
  }
  set name(arg) {
    this._name = arg;
  }
  get id() {
    return this._id;
  }
}
```

### 📋 상세

이 기법을 사용해야 하는 경우는 주로 2가지이다.

1. 사람들이 무조건 접근자 메서드를 통해서만 필드를 바꾸려 할 때. (ex: 생성자 안에서만 호출하는 세터가 있는 경우)
2. 생성 스크립트를 사용해 객체를 생성하려 할때.

```js
const martin = new Person();
martin.name = "마틴";
martin.id = "1234";
```

### ⚙️ 절차

1. 설정해야 할 값을 생성자에서 받지 않는다면 그 값을 받을 매개변수를 생성자에 추가한다. 그런 다음 생성자 안에서 적절한 세터를 호출한다.
2. 생성자 밖에서 호출하는 곳을 찾아 제거하고, 대신 새로운 생성자를 사용하도록 한다. 하나 수정할 때마다 테스트한다.
3. 세터 메서드를 인라인 한다. 가능하다면 해당 필드를 불변으로 만든다.
4. 테스트한다.
