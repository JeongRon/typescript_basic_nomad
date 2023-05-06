# typescript_basic_nomad

- (basic) Nomad - 타입스크립트로 블록체인 만들기
- https://nomadcoders.co/typescript-for-beginners

## 1. INTRODUCTION

- MS 가 VSCode 와 Typescript를 만들었기에 둘이 같이 사용하기 좋음

- Typescript

  - **타입의 안정성, 생산성** 장점
  - JavaScript 단점

    - (1) 말도 안되는 규칙을 허용해 주는 경우가 많다. (어떻게든 실행시켜 줌)
    - (2) 코드를 실행하기 전 까지 아무런 경고를 나타내 주지 않는다.

    ```js
    // (1) 말도 안되는 규칙을 허용해준다.

    [1, 2, 3, 4] + false;
    // => '1,2,3,4false'

    // (2) 코드를 실행하기 전 까지 아무런 경고를 나타내 주지 않는다.
    // 함수에서 꼭 들어야가 하는 매개변수와 매개변수 타입 지정 불가
    function divide(a, b) {
      return a / b;
    }
    // => divide(2, 3) => 0.666666
    // => divide("xxxxx") => NaN
    ```

- 타입스크립트란?
  - [타입스크립트 코드 테스트](https://www.typescriptlang.org/play)
  - [타입스크립트 핸드북](https://typescript-kr.github.io/pages/basic-types.html)
  1. TypeScript는 JavaScript에 추가적인 구문을 추가하여 editor와의 단단한 통합을 지원합니다. editor에서 초기에 오류를 잡을 수 있습니다.
  2. TypeScript 코드는 JavaScript가 실행되는 모든 곳(브라우저, Node.js 또는 Deno 및 앱 등)에서 JavaScript로 변환될 수 있습니다.
  3. TypeScript는 JavaScript를 이해하고 타입 추론(type inference)을 사용하여 추가 코드 없이도 훌륭한 도구를 제공합니다.

## 2. OVERVIEW OF TYPESCRIPT

- 다른 언어에서는 변수를 만든다면 타입을 지정해 준다.

- TypeScript System

  - (1) **명시적 정의** (변수 선언 시 타입 정의)
  - (2) 변수만 생성 => **타입 추론** (type inference)
  - (3) **readonly** 속성 넣기 => 수정 불가 / JS에는 없음
  - (4) 인덱스 순서에 맞게 [sting, number, boolean] **타입 지정** 가능
  - (5) **any** => 타입스크립트 보호 장치 빠져 나오는 방법
  - (6) **unknown** => 어떤 타입인지 모를 시, 혹은 정해지지 않을 시 사용
    - unknown 값으로 작업을 수행할 수 없으므로 any보다 더욱 안전함
  - (7) **void** => 함수가 아무것도 리턴하지 않게 함
  - (8) **never** => 일부 함수는 값을 반환하지 않음 / **throw**로 예외 처리하거나 실행 종료 의미

  ```typescript
  // 타입 추론
  let a = "hello";
  a = 123; // error

  // 명시적 정의
  let b: boolean = "x"; // error
  let b: bollean = false; // ok

  // 타입 추론
  let c = [1, 2, 3];
  c.push("1"); // error

  // 명시적 정의
  let c: number[] = [];
  c.push(1); // ok

  // 명시적 정의
  const player = {
    name: "nico",
  };
  player.hello(); // error

  // readonly : 수정 불가
  const number: readonly number[] = [1, 2, 3, 4];
  number.push(1); //  error

  // 3개의 items 인덱스 순서에 맞게 [sting, number, boolean] 타입이어야 함
  const player: [string, number, bollean] = ["nico", 1, true];

  // any : 타입스크립트 보호 장치 빠져 나오는 방법
  const a: any[] = [1, 2, 3, 4];
  const b: any = true;

  // unknown : 무슨 타입인지 정해지지 않음
  let a: unknown;

  if (typeof a === "number") {
    let b = a + 1;
  }
  if (typeof a === "string") {
    let b = a.toUpperCase();
  }

  // void : 함수의 return값 없음
  function hello(): void {
    console.log("hello");
  }

  // never : 예외 상황 throw 처리
  function hello(name: string | number): never {
    if (typeof name === "string") {
      console.log("OK");
    } else if (typeof name === "number") {
      console.log("OK");
    } else {
      throw new Error(msg);
    }
  }
  ```

## 3. FUNCTIONS

- **(1) call signature (타입 만들기)**

  - 파라미터 타입, 리턴 타입 미리 설계 가능

    ```typescript
    type Add = (a: number, b: number) => number;

    const add: Add = (a, b) => a + b;
    ```

---

- **(2) overloading**

  - 함수가 여러개의 call signature 을 가질때 발생

  ```typescript
  // overloading Example 1
  type Add = {
    (a: number, b: number): number;
    (a: number, b: string): number;
  };
  const add: Add = (a, b) => {
    if (typeof b == "string") return a;
    return a + b;
  };

  // overloading Example 2
  type Config = {
    path: string;
    state: object;
  };

  type Push = {
    (path: string): void;
    (config: Config): void;
  };

  const push: Push = (config) => {
    if (typeof config === "string") console.log(config);
    else console.log(config.path, confi.state);
  };

  // overloading Example 3 / difference parameter
  type Add = {
    (a: number, b: number): number;
    (a: number, b: number, c: number): number;
  };

  const add: Add = (a, b, c?: number) => {
    if (c) return a + b + c;
    return a + b;
  };
  ```

---

- **(3) polymorphism(다형성)**

  - concrete type / generic type 차이점 이해하기
  - call signature 에서 타입설정 알 수 없을 때, generic type 사용
  - Generic : 선언 시점이 아니라 생성 시점에 타입을 명시하여 하나의 타입만이 아닌 다양한 타입을 사용할 수 있도록 하는 기법

  ```typescript
  // Generic (1)
  type SuperPrint = <T>(arr: T[]) => T;
  const superPrint: SuperPrint = (arr) => arr[0];

  const a = superPrint([1, 2, 3, 4]);
  const b = superPrint([true, false, true]);
  const c = superPrint(["a", "b", "c"]);
  const d = superPrint([1, "a", true]);

  // Generic (2)
  type doubleGeneric = <T, M>(a: T[], b: M) => T;

  // Generic (3)
  type Player<E> = {
    name: string;
    extraInfo: E;
  };
  type Extra = {
    favFood: string;
  };
  type PlayerExtra = Player<Extra>;

  const nico: PlayerExtra = {
    name: "nico",
    extraInfo: {
      favFood: "kimchi",
    },
  };

  const lynn: Player<null> = {
    name: "lynn",
    extraInfo: null,
  };
  ```

## 4. CLASSES AND INTERFACES

- 객체지향 프로그래밍 개념

  - **constructor**의 매개변수를 지정해주면 this.firstName = firstName 같은 자바스크립트 코드로 자동 변환해준다.
  - **private** 키워드: 클래스 바깥에서 프로퍼티나 메서드에 접근할 수 없게 하는 키워드. 상속받은 클래스에서도 접근할 수 없다.(자바스크립트에서는 작동x)
  - **protected** 키워드: 자식 클래스에서는 프로퍼티나 메서드에 접근할 수 있게 하고, 외부에서는 접근할 수 없도록 하는 키워드.
  - **public** 키워드: 모든 클래스에서 접근 가능
  - **매서드** : 클래스 안에 존재하는 함수
  - **추상 클래스**: 다른 클래스가 상속 받을 수는 있지만 새로운 인스턴스는 만들 수 없는 클래스
  - **추상 매서드**
    - 추상 클래스 안에 만들 수 있는 메서드
    - 추상 클래스 안에서는 -> 추상 메서드의 call signature만 입력
    - 추상 클래스를 상속받는 클래스 -> 추상 매서드 call signature에 맞게 구현

  ```ts
  // (1) 객체 지향 선언
  class Player {
    constructor(
      private firstName: string,
      private lastName: string,
      public nickName: string
    ) {}
  }

  const nico = new Player("nico", "las", "니꼬");
  nico.firstName; // private -> Warning
  nico.nickName; // "니꼬"

  // (2) 추상 클래스 선언 및 활용
  // 추상 클래스 User
  abstract class User {
    constructor(
      protected firstName: string,
      protected lastName: string,
      protected nickName: string
    ) {}
    // 추상 매소드
    abstract getNickName(): void;
    // 매소드
    getFullName() {
      return console.log(`${this.firstName} ${this.lastName}`);
    }
  }

  // 추상 클래스를 상속 받은 클래스 Player -> 추상 매소드 구현
  class Player extends User {
    getNickName() {
      console.log(this.nickName);
    }
  }

  // Player 클래스 사용해서 인스턴스 만들기
  const nico = new Player("nico", "las", "니꼬");

  nico.getNickName(); // "니꼬"
  nico.getFullName(); // "nico las"
  ```

- 해시맵 / 사전 만들기 실습

  - 딕셔너리, 단어 클래스 만들기

  ```ts
  // (1) 타입 정의
  type Words = {
    // key, value 타입을 정의해 주는 방식
    [key: string]: string;
  };

  class Dict {
    // (2) property(words)를 만들고 원하는대로 초기화하기
    private words: Words;
    constructor() {
      this.words = {};
    }
    // (3) 클래스(Word)를 타입으로 사용
    // -> word 파라미터가 Word 클래스의 인스턴스이기를 원할 때 사용
    add(word: Word) {
      if (this.words[word.term] === undefined) {
        this.words[word.term] = word.def;
      }
    }
    def(term: string) {
      return this.words[term];
    }
  }

  class Word {
    constructor(public term: string, public def: string) {}
  }

  const dict = new Dict();

  const kimchi = new Word("kimchi", "한국 음식 김치");

  dict.add(kimchi);
  console.log(dict.def("kimchi"));
  ```

  - 코드 챌린지
    - Dict 클래스 : 단어를 삭제하는 메서드, 단어를 업데이트 하는 메서드 만들기
    - Word 클래스 : 단어의 정의를 추가하거나 수정하는 메서드, 단어를 출력하는 메서드 만들기
    - Class, Method, private, public 등등 객체지향 활용해서 만들어 보기
