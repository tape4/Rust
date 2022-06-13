# Rust

태그: 언어

# 보편적인 프로그래밍 개념

## 변수의 가변성

Rust의 기본 변수는 불변성이다.

변수가 불변성인 경우 일단 값이 이름에 bound 되면 해당 값을 변경할 수 없다.

```rust
fn main(){
	let x = 5;
	println!("The value of x is : {}", x);
	x = 6;
	println!("The value of x is : {}", x);
}
```

저장하고 `cargo run` 명령을 통해 실행시키면 오른쪽과 같은 에러 메시지가 나온다

```
error[E0384]: re-assignment of immutable variable `x`
 --> src/main.rs:4:5
  |
2 |     let x = 5;
  |         - first assignment to `x`
3 |     println!("The value of x is: {}", x);
4 |     x = 6;
  |     ^^^^^ re-assignment of immutable variable
```

에러가 나타내는 것은 `불변성 변수에 재할당` 이고, 원인은 `x`에 두 번째로 값을 할당했기 때문이다. Rust는 컴파일러가 변경되지 않은 값에 대한 보증을하고 실제로 이는 바뀌지 않는다. 그러므로 코드를 작성하거나 분석할 시에 변수의 값이 어떻게 변경되는지 추적할 필요가 없기 때문에 코드를 더 합리적으로 만들 수 있다.

---

하지만 가변성은 유용하게 사용할 수 있다. 변수는 기본적으로 불변성이지만 변수명의 접두어로 `mut` 를 추가하는 것을 통해 가변성 변수를 선언할 수 있다. 이 변수의 값이 변경을 허용하는 것에 추가로 향후 코드를 보는 사람에게 코드의 다른 부분에서 해당 변수의 값을 변경할 것이라는 의도를 전달할 수 있다.

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {}", x);
    x = 6;
    println!("The value of x is: {}", x);
}
```

실행시키면 오른쪽 결과를 얻게 된다.

---

```
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/variables`
The value of x is: 5
The value of x is: 6
```

`mut` 를 사용하여, `x`에 bind된 값을 5에서 6으로 변경할 수 있다. 불변성 변수만을 사용하는 것보다 가변성 변수를 사용하여 보다 쉽게 구현할 수 있는 경우 가변성 변수를 만들어 사용할 수도 있다.

이 결정에 있어서 버그를 예방하는 것 외에도 고려해야할 점이 있다. 예를 들어, 대규모 데이터 구조체를 다루는 경우 가변한 인스턴스를 사용하는 것이 새로 인스턴스를 할당하고 반환하는 것보다 빠를 수 있다. 데이터 규모가 작을수록 새 인스턴스를 생성하고 함수적 프로그래밍 스타일로 작성하는 것이 더 합리적이고, 그러므로 약간의 성능 하락을 통해 가독성을 확보할 수 있다면 더 가치있는 선택이다.

---

### 변수와 상수 간의 차이점들

변수의 값을 변경할 수 없다는 사항처럼 상수 또한 이름으로 bound된 후에는 값의 변경이 허용되지 않는다. 하지만 상수와 변수는 조금 다르다.

1. 상수에 대해서는 `mut` 을 사용할 수 없다. → 상수는 기본 설정이 불변성인 것이 아니라 그 자체가 불변성이다.
2. 상수를 사용하고자 하면 `let` 키워드 대신 `const` 키워드를 사용해야 하고, 값의 유형을 선언해야 한다. → [데이터 타입들](Rust%206c87bc32962b444cb4dc5be72e8029d7.md)
3. 상수는 전체 영역을 포함하여 어떤 영역에서도 선언될 수 있다. 이는 코드의 많은 부분에서 사용될 필요가 있는 값을 다루는데 유용하다.
4. 상수는 오직 상수 표현식만 설정될 수 있다. 함수 호출의 결과값이나 그 외에 실행 시간에 결정되는 값이 설정될 수 없다.

```rust
const MAX_POINTS: u32 = 100_100;
```

상수는 자신이 선언되어 있는 영역 내에서 프로그램이 실행되는 시간 동안 항상 유효하다. 따라서 프로그램의 다양한 곳에서 사용되는 값을 상수로 하면 유용하다. 예를 들면 사용자가 한 게임에서 획득할 수 있는 최대 점수, 빛의 속도 같은 값 등등...

---

### Shadowing

이전에 선언한 변수와 같은 이름의 새 변수를 선언할 수 있고, 새 변수는 이전 변수를 _shadows_ 하게 된다. 또는 이전 변수가 두 번째 변수에 의해 _shadowed_ 됐다고 표현한다. `let` 키워드를 사용해서 반복해서 같은 변수명으로 변수를 shadow 할 수 있다.

```rust
fn main() {
    let x = 5;

    let x = x + 1;

    let x = x * 2;

    println!("The value of x is: {}", x);
}
```

처음 `x`에 `5`를 bind 하고 이후 반복된 `let = x` 구문으로 `x`를 shadow하고 원본 값에 1을 더해서 `x`의 값은 `6`이 된다. 세 번째 `let` 문으로 또 `x`를 shadow하고 이전 값에 `2`를 곱해서 `x`는 `12`가 된다.

실행 결과

```
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/variables`
The value of x is: 12
```

---

이 방법은 변수를 `mut`으로 선언하는 것과는 차이가 있다. `let` 키워드를 사용하지 않고 변수에 새로 값을 대입하려고 하면 컴파일시 에러를 얻게 된다. 위 방법은 몇 번 값을 변경할 수 는 있지만 그 이후에 변수는 불변성을 갖게 된다.

또 다른 `mut` 과 shadowing의 차이점은 `let` 키워드를 다시 사용해서 효과적으로 새 변수를 선언하고, 값의 유형을 변경할 수 있으면서도 동일 이름을 사용할 수 있다는 점이다. 예를 들어 공백 문자들을 입력받아 얼마나 많은 공백 문자가 있는지 보여주고자 할 때, 실제로 지정하고자 하는 것은 공백의 개수일 것이다.

```rust
let spaces = "    ";
let spaces = spaces.len();
```

이와 같은 구조가 가능한 이유는 첫 `spaces` 변수가 문자열 유형이고 두 번째 `spaces` 변수는 첫 번째 것과 동일한 이름을 가진 새롭게 정의된 숫자 유형의 변수이기 때문이다. Shadowing은 `space_str`이나 `space_num` 과 같이 대체된 이름을 사용하는 대신 간단히 `spaces` 이름을 사용할 수 있게 해준다.

만약 `mut`을 사용하려고 했다면

```rust
let mut spaces = "    ";
spaces = spaces.len();
```

오른쪽처럼 변수의 유형을 변경할 수 없다는 에러를 컴파일과정에서 얻게 된다.

```
error[E0308]: mismatched types
 --> src/main.rs:3:14
  |
3 |     spaces = spaces.len();
  |              ^^^^^^^^^^^^ expected &str, found usize
  |
  = note: expected type `&str`
             found type `usize`
```

---

## 데이터 타입들

Rust에서 사용되는 모든 값들은 특정 타입을 갖는다. 그러니 어떤 형태의 데이터인지 Rust에게 알려줘야 Rust가 데이터를 어떻게 다룰지 정하게 된다.

데이터 타입은 크게

-   스칼라
-   컴파운드

둘로 나눌 수 있다.

Rust는 `statically typed language`다. 즉 정적타입 언어로 컴파일 시 변수의 타입이 결정되어 있어야 한다. 보통 컴파일러는 값과 사용방법에 따라 어떤 유형을 사용할지 추론할 수 있다. 타입의 선택 폭이 넓은 경우는 반드시 타입 명시해야 한다.

### 스칼라 ( Scalar )

스칼라는 하나의 값으로 표현되는 타입이다. Rust는 정수형, 부동소수점 숫자, boolean, 그리고 문자까지 총 네 가지 스칼라 타입이 있다.

> **정수형 ( Integer )**

정수형은 소수점이 없는 숫자다. `u32` 타입은 부호없는 32비트 변수임을 나타낸다. 부호 있는 타입은 `u` 대신 `i` 로 시작한다. 각각의 타입은 부호 혹은 미부호이며 명시된 크기를 갖는다. 부호와 미부호가 의미하는 바는 숫자가 양수 와 음수를 다룰 수 있는지를 나타낸다. 숫자가 부호를 둘 다 가져야 하는 경우는 부호가 필요하지만(Signed) 양수만 다룰 것이라면 부호가 필요없다.(Unsigned)

각 Signed변수는 $-(2^{n-1})$부터 $2^{n-1}-1$까지의 값을 나타낼 수 있다. 여기서 n은 타입의 비트 수다. 즉 `i8`은 $-(2^7)$에서 $2^7-1$까지의 값을 저장할 수 있다. 반대로 `u8`은 0에서 $2^8-1$ 즉 0에서부터 255까지 값을 저장할 수 있다.

추가적으로 `isized`, `usize`타입은 프로그램이 동작하는 컴퓨터 환경이 64-bits인지 아닌지에 따라 결정된다. 64-bit 아키텍쳐이면 64bit를, 32-bit 아키텍쳐이면 32bit를 갖게 된다.

정수형은 Number literals를 사용해 표현할 수 있다. byte literal을 제외하고 모든 Number literals은 `57u8` 과 같은 타입 접미사와 `1_000`과 같이 시각적인 구분을 위한 `_` 사용을 허용한다.

확실하게 정해진 경우가 아니라면 Rust의 기본 값인 `i32` 가 일반적으로 좋은 선택이다. 이유는 보통 가장 빠르기 때문이다. 심지어 64-bit 시스템에서도.

| Length | Signed | Unsigned |
| ------ | ------ | -------- |
| 8-bit  | i8     | u8       |
| 16-bit | i16    | u16      |
| 32-bit | i32    | u32      |
| 64-bit | i64    | u64      |
| arch   | isize  | usize    |

| Number literals | Example     |
| --------------- | ----------- |
| Decimal         | 98_222      |
| Hex             | 0xff        |
| Octal           | 0o77        |
| Binary          | 0b1111_0000 |
| Byte(u8 only)   | b’A’        |

---

> **수학적 연산 ( Numeric Operations )**

Rust가 지원하는 일반적인 기본 수학적 연산은 모든 숫자 타입에 적용된다. 더하기, 빼기, 곱하기, 나누기, 나머지 연산 등등...

```rust
fn main() {
    // addition
    let sum = 5 + 10;

    // subtraction
    let difference = 95.5 - 4.3;

    // multiplication
    let product = 4 * 30;

    // division
    let quotient = 56.7 / 32.2;

    // remainder
    let remainder = 43 % 5;
}
```

> **Boolean 타입**

대부분의 다른 언어처럼 boolean 타입은 Rust에서 둘 중 하나의 값만 가질 수 있다. `true`와 `false`.

rust내에서는 `bool`로 표현한다.

```rust
fn main() {
    let t = true;

    let f: bool = false; // with explicit type annotation
}
```

> **부동 소수점 타입 ( Floating-Point )**

Rust에는 부동소수점 숫자를 위한 두 가지 기본 타입이 있다. `f32`와 `f64`로 각각 32bit와 64bit의 크기를 갖는다. 기본타입은 `f64`인데, 그 이유는 최신 CPU 상에서는 `f32`와 `f64`의 속도가 대략 비슷하기 때문에 더 정밀한 표현이 가능한 `f64`가 기본 타입이다.

예제 :

```rust
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

> **문자 타입 ( Character )**

Rust는 문자 또한 지원한다. Rust의 `char`는 이 언어의 가장 근본적인 알파벳 타입이다.

스트링이 큰따옴표를 쓰는것에 반해서 `char`타입은 작은 따옴표를 쓴다.

```rust
fn main() {
   let c = 'z';
   let z = 'ℤ';
   let heart_eyed_cat = '😻';
}
```

Rust의 `char`타입은 Unicode Scalar를 표현하는 값이고 ASCII 보다 많은 표현을 할 수 있다. 한국어, 중국어, 일본어, 표의 문자, 이모티콘, 넓이가 0인 공백문자 모두 `char`타입으로 사용할 수 있다.

---

### 컴파운드 ( Compound Types )

컴파운드 타입 즉 복합 타입은 다른 타입의 다양한 값들을 하나의 타입으로 묶을 수 있다. Rust에는 두 개의 기본 컴파운드 타입이 있다.

-   튜플
-   배열

> **튜플 ( Tuple )**

튜플은 다양한 타입의 몇 개의 숫자를 집합시켜 하나의 컴파운드 타입으로 만드는 일반적인 방법이다.

괄호 안에 콤마로 구분되는 값들의 목록을 작성하여 튜플을 만들 수 있다. 튜플에 포함되는 각 값의 타입이 동일할 필요는 없다.

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

변수 `tup` 에는 튜플 전체가 bind 된다. 개별 값을 튜플 밖으로 꺼내오기 위해서는, pattern matching을 통해 튜플을 *destructuring*할 수 있다.

```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {}", y);
}
```

처음 프로그램은 튜플 (500, 6.4, 1)을 만들고 만든 튜플을 변수 `tup`에 bind한다. 이후 pattern 과 `let`을 통해 `tup`을 세개의 변수 `x`, `y`, `z`로 이동시킨다. 이것을 *destructuring*이라고 한다.

pattern matching을 통한 *destructuring*에 더해서, 마침표(`.`)뒤에 접근하고 싶은 값의 index를 기입하는것으로 튜플에 요소에 직접적으로 접근할 수 있다.

```rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

---

> **배열**

또다른 여러값을 집합체로 만드는 방법은 배열이다. 튜플과 다르게 모든 배열의 요소는 같은 타입이어야 한다. Rust의 배열이 다른 언어들의 배열과 다른 점은 Rust의 배열은 고정된 길이를 갖는다는 점이다. 한번 선언되면 그 배열의 크기는 커지거나 작아지지 않는다.

Rust에서 배열은 대괄호 안에 값들을 콤마로 구분하여 나열해서 만들 수 있다.

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
}
```

배열은 데이터를 `힙`이 아닌 `스택`에 할당하는 경우 또는 항상 고정된 수의 요소를 사용하는 경우에 유용하다. 백터 타입은 유사 집합체로 표준 라이브러리에서 제공되며 크기를 확장하거나 축소할 수 있다. 배열이나 벡터 중 뭘 선택해야 할지 확실하지 않은 상황이라면 벡터를 사용하는 것이 좋다.

벡터가 아닌 배열을 선택하게 되는 경우의 예시로 프로그램이 달 이름을 알고자 할 경우다. 프로그램이 달을 추가하거나 삭제하는 경우는 거의 없을 것이므로, 고정적으로 12개의 요소를 가질 테니 배열을 사용하면 된다.

```rust
#![allow(unused)]
fn main() {
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
}
```

> **배열 요소에 접근하기**

배열은 `스택`에 단일 메모리 뭉치로 할당된다. 인덱싱을 통해 배열의 요소에 접근할 수 있다.

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```

> **유효하지 않은 배열 요소에 대한 접근**

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
    let index = 10;

    let element = a[index];

    println!("The value of element is: {}", element);
}
```

`cargo run`으로 실행하면

```rust
$ cargo run
   Compiling arrays v0.1.0 (file:///projects/arrays)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/arrays`
thread '<main>' panicked at 'index out of bounds: the len is 5 but the index is
 10', src/main.rs:6
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```

컴파일 시에는 아무런 에러도 발생시키지 않지만, 프로그램이 실행되는 중에 에러가 발생하고 성공적으로 종료하지 못했다고 나온다.

인덱싱을 사용하여 요소에 접근하려고 하면 Rust는 지정한 인덱스가 배열길이보다 작은지 확인한다. 인덱스가 배열의 길이보다 길면 Rust는 프로그램이 오류와 함께 종료 될 때 Rust가 사용하는 용어인 패닉(Panic)을 한다.

---

## 함수 동작 원리

함수는 Rust에 녹아들어 있다. `fn` 키워드는 새로운 함수의 선언을 의미하며 `main`함수 는 프로그램에서 가장 먼저 실행하는 부분이다.

Rust코드는 snake case를 변수나 함수 이름의 형식규칙으로 사용한다. snake case에서 모든 문자는 소문자를 사용하고, 단어구분은 밑줄표시(\_)로 한다.

```rust
fn main() {
    println!("Hello, world!");

    another_function();
}

fn another_function() {
    println!("Another function.");
}
```

Rust 에서의 함수 선언은 `fn` 으로 시작하며, 함수 이름 뒤에 괄호를 적는것을 되어 있다. 중괄호는 컴파일러에게 함수의 시작과 종료지점을 알려준다.

실행 결과 :

```
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 0.28 secs
     Running `target/debug/functions`
Hello, world!
Another function.
```

함수의 이름과 괄호 형식을 기입하는 것을 통해 선언했던 어떤 함수든 호출 할 수 있다. `another_function`이 프로그램 내에 선언되어 있으므로, `main`함수에서 해당 함수를 호출할 수 있다. 주의할 점은, 소스 코드 내에서 `another_function`이 `main` 함수 뒤에 정의 했다는 점이다. 물론 `main` 함수 앞에도 정의할 수 있다. Rust는 함수의 위치를 신경쓰지 않는다. 어디든 정의만 되어 있으면 된다.

---

### **함수 매개변수**

함수는 함수 고유한 부분인 특별한 변수 매개변수를 갖는 형식으로 선언될 수 있다. 우리는 상수를 함수의 전달인자로 사용할 수 있다.

```rust
fn main() {
    another_function(5);
}

fn another_function(x: i32) {
    println!("The value of x is: {}", x);
}
```

`another_function`의 선언은 `x`라고 하는 하나의 매개변수를 갖는다. `x`의 타입은 `i32`로 정의 했다. `5`가 `another_function`으로 전달되면, `println!` 에 `5`를 전달한다. 함수의 선언부에서 반드시 매개변수의 타입을 정의해야 한다. 매개변수의 타입을 정의하는 이유는 함수의 정의에 타입을 명시하여 코드 내 다른 부분에서 사용하는 할 때 함수를 만든사람의 의도를 추측하지 않아도 되게 된다.

함수에 여러개의 매개변수를 사용하고 싶다면, 매개변수들을 쉼표로 구분해 사용할 수 있다. 또한 매개변수는 굳이 같은 타입일 필요가 없다.

```rust
fn main() {
    another_function(5, 6);
}

fn another_function(x: i32, y: i32) {
    println!("The value of x is: {}", x);
    println!("The value of y is: {}", y);
}
```

---

### 함수 본문

함수는 필요에 따라 표현식으로 끝나는 구문의 나열로 구성된다. 지금까지 대부분은 종결 표현식이 없는 함수만을 다뤘다. Rust가 표현식에 기반한 언어이기 때문에 이것을 이해하는것은 중요하다.

> **구문과 표현식**

`구문`은 어떤 명렬들의 나열로 값을 반환하지 않는 어떤 동작을 수행한다.

`표현식`은 결과 값을 산출해 낸다.

```rust
fn main() {
    let y = 6;
}
```

`let` 키워드를 통해 변수를 만들고 값을 할당하는 구문이다. `let y = 6;`은 특정한 값을 반환하지 않는다. 그냥 그 자체가 구문이다.

```rust
fn main() {
    let x = (let y = 6);
}
```

---

`let y = 6`구문은 반환 값이 없으므로 `x`에 bind 시킬 것이 없다. 이것이 C언어와의 차이점이다. `x = y = 6`과 같은 코드가 `x` 와 `y` 모두에 `6`을 대입할 수 있지만 Rust에서는 불가능하다.

Rust 코드의 대부분은 표현식이며 어떤 값을 산출해 낸다. `5 + 6`과 같은 간단한 수학연산도 결국 `11`을 산출하는 표현식이다.

---

표현식은 구문의 부분일 수도 있다. `let y = 6;` 이라는 구문에서 `6`은 `6`이란 값을 산출하는 표현식이다. 함수를 호출하는것과 메크로를 호출하는것은 표현식이다. 또한 새로운 범위를 생성하는데 사용한 `{}`도 표현식이다.

```rust
fn main() {
    let x = 5;

    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {}", y);
}
```

```rust
let y = {
        let x = 3;
        x + 1
    };
```

---

이 블럭에서는 `4`를 산출한다. 이 값은 `y`에 bound된다. 여기서 중요한 점은 `x + 1`줄의 마지막이 세미콜론으로 끝나지 않은 점이다. 표현식은 종결을 나타내는 세미콜론을 사용하지 않는다. **마지막 세미콜론을 표현식의 마지막에 추가하면, 이는 구문으로 변경되고 아무런 값도 반환하지 않는다.**

---

> **반환 값을 갖는 함수**

함수는 함수를 호출한 코드에 값을 반환할 수 있다. 반환하는 값을 명명할필요는 없지만 반환하는 값의 타입은 화살표(`->`)뒤에 선언해야 한다. Rust에서 반환값은 함수 본문의 마지막 표현식의 값과 동일하다. `return` 키워드와 값을 써서 함수로부터 일찍 반환할 수 있지만, 대부분의 함수들은 암묵적으로 마지막 표현식을 반환한다.

```rust
fn five() -> i32 {
    5
}

fn main() {
    let x = five();

    println!("The value of x is: {}", x);
}
```

`five` 함수에는 함수 호출, 메크로, 심지어 `let` 구문도 없이 그저 `5`란 숫자 하나가 있다. 이는 Rust에서 함수로 허용된다. 함수 반환 값의 타입이 `->i32`로 명시되어 있다는 점 또한 주목해야 한다.

`5`는 `five` 함수가 반환한 값이고, 이 때문에 반환 타입을 `i32`라고 한 것이다.

```rust
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```

---

이 코드는 `The value of x is: 6`을 출력한다. `x + 1` 끝에 세미콜론을 추가하여 표현식으로 구문을 끝냈다면 오류가 발생한다.

---

## 주석

Rust의 주석은 두 개의 슬래쉬로 시작해야 하고 해당 줄의 끝까지 계속 된다.

```rust
fn main() {
// Hello, world.
}
```

한 줄이 넘는 주석을 작성할 경우, `//`를 각 줄에 포함시켜 사용하면 된다.

```rust
fn main() {
// 응애
// 나 아기 주석
// 주석은 그런거 몰라
}
```

---

## 제어문

조건의 상태가 참인지 거짓인지에 따라 어떤 코드의 실행 여부를 결정하거나 조건이 만족되는 동안 반복수행을 하는 것은 기초 문법이다. 실행 흐름을 제어할 수 있는 가장 보편적인 작성방식은 `if`문과 반복문이다.

### if문

if문은 코드가 조건에 따라 실행할지 말지를 결정할수 있게 한다. “조건이 충족되면 이 코드 블럭을 실행하고, 만약 충족되지 않는다면 코드 블럭을 실행하지 말아라”

```rust
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

`if`문은 `if`란 키워드로 시작해 뒤에 조건이 온다. 예제에서 변수 `number`가 5보다 작은 값을 가지는지를 조건으로 하고 있다. 조건이 참일때 실행하는 코드 블록은 조건 바로 뒤 중괄호 사이다. `if` 식의 조건과 관련된 코드 블럭은 `match`식의 갈래(arms)와 마찬가지로 갈래(arms)로 불린다. 물론 `else` 식을 포함시킬 수 있는데, `else`는 조건이 거짓으로 산출될 경우 실행시킬 코드 블럭이다.

주의해야 할 점은 코드의 조건은 반드시 `bool` 이어야 한다. 만약 `bool` 이 아닐 경우 에러가 발생된다.

### else if 와 다수 조건

`if`와 `else`사이에 `else if` 식을 추가 결합해서 다양한 조건을 다룰 수 있다.

```rust
fn main() {
    let number = 6;

    if number % 4 == 0 {
        println!("number is divisible by 4");
    } else if number % 3 == 0 {
        println!("number is divisible by 3");
    } else if number % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }
}
```

너무 많은 `else if` 식의 사용은 코드를 이해하기 어렵게 하므로, 둘 이상일 경우 코드를 리펙토링하게 될 수도 있다. 이런 경우를 위해 `match`를 사용한다.

### let구문에서 if 사용

`if` 가 표현식이기 때문에 `let` 구문의 우측에 사용할 수 있다.

```rust
fn main() {
    let condition = true;
    let number = if condition {
        5
    } else {
        6
    };

    println!("The value of number is: {}", number);
}
```

코드 블럭은 코드블럭 마지막에 위치한 표현식을 산출하고, 숫자는 숫자 그 자체로 표현식이다. 예제의 경우 `if`식의 값은 실행되는 코드블럭에 따라 다르다. 따라서. `if`**식의 속한 각 갈래의 결과값은 반드시 동일한 타입이어야 한다.**

만약 다른 결과값이 다른타입이라면

```rust
fn main() {
    let condition = true;

    let number = if condition {
        5
    } else {
        "six"
    };

    println!("The value of number is: {}", number);
}
```

에러 메시지 :

```
error[E0308]: if and else have incompatible types
 --> src/main.rs:4:18
  |
4 |       let number = if condition {
  |  __________________^
5 | |         5
6 | |     } else {
7 | |         "six"
8 | |     };
  | |_____^ expected integral variable, found reference
  |
  = note: expected type `{integer}`
             found type `&str`
```

`if` 블록이 정수형을 산출하고 `else`블록은 문자열을 산출한다. 이것이 [에러를 갖는 이유](Rust%206c87bc32962b444cb4dc5be72e8029d7.md)는 변수가 가질 수 있는 타입은 오직 하나이기 때문이다. Rust는 컴파일 시에 `number`변수의 타입이 뭔지 **확실히** 정의해야 한다. Rust는 number의 타입을 실행 시에 정하도록 하지 않는다.

---

### 반복문과 반복

Rust는 몇 가지 반복문을 제공한다.

-   `loop`
-   `while`
-   `for`

> **loop**

`loop` 키워드는 Rust에게 그만두라고 명시하기 알려주기 전까지 코드 블럭을 반복 수행한다.

```rust
fn main() {
    loop {
        println!("again!");
    }
}
```

대부분의 터미널은 단축기 ctrl-c를 통해서 무한루프에 빠진 프로그램을 정지시킬 수 있다. 하지만 그 외에도 `break` 키워드를 위치시켜 프로그램이 언제 루프를 멈춰야 하는지 알려줄 수 있다.

---

> **while**

조건이 참인 동안 반복문을 수행한다. 조건이 참이 아니게 된 경우에 `break`를 호출해서 반복을 정지시킨다. 물론 `loop`문으로 만들 수 있지만 `if`, `else`, `break`를 혼합해야한다. 따라서 Rust에는 동일한 구조가 내장되어 있고 그것을 바로 `while` 반복문이라고 부른다.

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}!", number);

        number = number - 1;
    }

    println!("LIFTOFF!!!");
}
```

> **For문으로 Collection반복하기**

`while` 문을 통해 배열과 같은, collection의 각 요소에 걸쳐 반복수행 할 수 있다. 하지만 이 방식은 에러가 발생하기 쉽다. collection의 정확한 길이의 인덱싱을 사용하지 못하면 프로그램은 패닉을 발생한다. 또한 `while`문으로 구현하면 반복문을 다시 반복할 때마다 조건검사를 하기 때문에 더 느리다. 때문에 효율적인 방법으로 `for`반복문을 사용할 수 있다.

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a.iter() {
        println!("the value is: {}", element);
    }
}
```

이 코드는 `while` 으로 구현한 것과 동일한 결과값을 갖지만 코드의 안정성을 높이고 배열의 끝을 넘어가거나 충분한 길이를 지정해주지 못해 일부 요소들이 누락되어 발생할 수 있는 버그의 가능성을 제거할 수 있다.

때문에 `for` 반복문이 안전하고 간결하고 가장 보편적으로 사용되는 반복문이다. 또한 Rust에서 기본 라이브러리로 제공하는 `Range`를 사용할 수 있다. `Range`는 첫 숫자에서 두 번째 숫자 전까지 모든 숫자를 차례로 생성한다.

```rust
fn main() {
    for number in (1..4).rev() {
        println!("{}!", number);
    }
    println!("LIFTOFF!!!");
}
```

---

# 소유권

소유권(Ownership)은 Rust의 가장 특별한 특성이며, Rust가 Gabage collector없이 메모리 안정성을 보장하게 해준다.

모든 프로그램은 실행하는 동안 컴퓨터의 메모리를 사용하는 방법을 관리해야 한다. 일부 언어에는 프로그램을 실행할 때 더 이상 사용되지 않는 메모리를 지속적으로 찾는 가비지 컬렉션이 있거나 메모리를 명시적으로 할당하고 해제해야한다. 하지만 Rust는 다른 방식을 사용한다. 메모리는 컴파일러가 체크하는 일련의 규칙을 사용하여 소유권 시스템을 통해 관리된다. 규칙 중 하나를 위반하면 프로그램이 컴파일되지 않는다.

## 소유권이란?

### 스택과 힙

소유권을 설명하기 앞서 `스택`과 `힙`의 동작 방식에 대해 알 필요가 있다. 보통 다른 프로그래밍 언어들 안에서 자주 `스택`과 `힙`에 대해서 생각할 필요는 없다. 하지만 Rust와 같은 시스템 프로그래밍 언어에서는, 값이 `스택`에 있는지 `힙`에 있는지에 따라 언어의 동작 방식과 우리의 결정에 영향을 준다.

`스택`과 `힙`의 다른점은 각기 다른 방식으로 구조화 되있다는 점이다.

`스택`은 데이터에 접근하는 방식 덕분에 빠르게 동작한다. `스택`은 새로운 데이터를 넣어두기 위한 공간 혹은 데이터를 가져올 공간을 검색할 필요가 전혀 없다. 바로 그 공간이 항상 `스택`의 꼭대기(`top`)이기 때문이다. `스택`을 빠르게 해주는 또다른 특성은 `스택`에 담긴 모든 데이터가 결정되어 있는 고정된 크기를 갖고 있어야 한다는 점이다.

컴파일할 때 크기가 결정되어 있지 않거나 크기가 변경될 수 있는 데이터는 `힙`에 데이터를 저장한다. `힙`은 조금 더 복잡하다. 데이터를 `힙`에 넣을때, 먼저 저장할 공간을 요청한다. 그러면 운영체제는 충분히 큰 빈 곳을 찾아서 그곳을 ‘사용중' 이라고 표시하고 해당 지점의 포인터를 돌려준다. 이 절차를 힙 공간 할당하기(allocating on the heap)라고 부르고, 종종 그냥 할당(allocating)으로 줄여 부른다. `스택`에 포인터를 푸쉬 하는것은 할당에 해당하지 않는다. 포인터의 크기는 이미 고정된 크기를 갖기때문에 스택에 저장할 수 있지만, 실제 데이터를 사용하고자 할 때는 포인터를 따라가야 한다.

`힙`에 저장된 데이터에 접근하는 것은 `스택`에 저장된 데이터에 접근하는 것보다 느린데, 포인터가 가르킨 곳을 따라가야 하기 때문이다. 이와 마찬가지로, 프로세서는 `힙`처럼 멀리 떨어져 있는 데이터들 보다는 `스택`과 같이 붙어있는 데이터들에 대한 작업을 하면 더 빨라 진다. `힙`으로부터 큰 공간을 할당받는 것 또한 시간이 걸릴 수 있다.

코드의 어느 부분이 `힙`의 어떤 데이터를 사용하는지 추적하는 것, `힙`의 중복된 데이터의 양을 최소화 하는것, 그리고 `힙` 내에 사용하지 않는 데이터를 제거하여 공간이 모자라지 않게 하는 것은 모두 소유권과 관련된 문제다.

---

### 소유권 규칙

먼저, 소유권의 규칙이다.

1. Rust의 각각의 값은 해당값의 오너(owner)라고 불리는 변수를 갖고 있다.
2. 한 번에 딱 하나의 오너만 존재할 수 있다.
3. 오너가 스코프 밖으로 벗어날 때 값은 버려진다.(`drop`)

---

### 변수의 스코프

스코프란 프로그램 내에서 아이템이 유효함을 표시하기 위한 범위다.

```rust
let s = "hello";
```

변수 `s` 는 스트링 리터럴을 나타낸다. 스트링 리터럴의 값은 프로그램의 텍스트 내에 하드코딩되어 있다. 하드코딩이란 데이터를 코드 내부에 직접 입력하는 것이다. 변수는 선언된 시점부터 현재의 스코프가 끝날 때까지 유효하다.

```rust
{                      // s는 유효하지 않다. 아직 선언이 안되었음.
    let s = "hello";   // s는 이 지점부터 유효하다.

    // s를 가지고 뭔가 할 수 있다.
}                      // 이 스코프는 이제 끝이므로, s는 더이상 유효하지 않다.
```

두 가지 중요한 지점이 있다.

1. 스코프 안에서 `s`가 등장하면, 유효하다.
2. 이 유효기간은 스코프 밖으로 벗어날 때까지 지속된다.

이 스코프와 변수가 유효한 시점의 관계는 다른 프로그래밍 언어와 비슷하다.

---

### String 타입

소유권을 설명하기 위해서는 앞에서 본 변수들보다 더 복잡한 데이터 타입이 필요하다. 이전에 봐온 모든 데이터 타입들은 스택에 저장되었다가 스코프를 벗어날 때는 `스택`으로부터 `pop` 된다. 이제는 `힙`에 저장되는 데이터를 관찰하고 Rust가어떻게 동작하는지 설명할 예시로 `String`을 활용한다. 이는 표준 라이브러리나 본인이 새롭게 만들 다른 복잡한 데이터 타입에도 적용된다.

스트링 리터럴은 앞서 언급했듯이 하드코딩 되어 있다. 하지만 스트링 리터럴 텍스트를 필요로 하는 모든 경우에 대해 항상 적절하지는 않다. 그 이유로 문자열 값은 불변이기 때문이다. 또다른 이유로 모든 문자열이 우리가 프로그래밍 하는 시점에 다 알수 있는 것이 아니다. 예를 들면 사용자의 입력을 받아 저장하고 싶을 때다. 이를 위해 Rust에서는 두 번째 문자열 타입인 `String`을 사용할 수 있다. 이 타입은 전에 봐왔던 데이터 타입들과 다르게 `힙`에 할당되고 컴파일할 때 전체 크기를 몰라도 된다. 스트링 리터럴로부터 `from` 이라는 함수를 이용해서 `String`을 만들 수 있다.

```rust
let s = String::from("hello");
```

더블 콜론(`::`)은 우리가 `string_from`과 같은 이름을 쓰기 보다 `String` 타입 아래의 `from` 함수를 특정 지을 수 있도록 해주는 네임스페이스 연산자다. 이 `String` 타입 문자열은 변경이 가능하다.

```rust
let mut s = String::from("hello");

s.push_str(", world!"); // push_str()은 해당 스트링 리터럴을 스트링에 붙여준다.

println!("{}", s); // 이 부분이 `hello, world!`를 출력할 것이다.
```

`String` 타입은 변할 수 있는데 스트링 리터럴은 안되는 이유는 두 타입이 메모리를 쓰는 방식에 있다.

---

### 메모리와 할당

스트링 리터럴의 경우, 우리는 내용물을 컴파일할 때 알 수 있으므로 텍스트가 최종 실행파일에 직접 하드코딩 되었고, 이렇게 한다면 스트링 리터럴이 빠르고 효율적이다. 그러나 이는 문자열이 “불변"임을 전재로 한다.

`String` 타입은 변경 가능하고 커질 수 있는 텍스트를 지원하기 위해 만들어졌고, 지금 우리는 `힙`에서 컴파일할 때 알 수 없는 어느 정도의 크기의 메모리 공간을 할당받아 내용물을 저장해야한다. 즉

1. 런타임에 운영체제로부터 메모리를 요청해야 한다.
2. `String`의 사용이 끝났을 때 운영체제에게 메모리를 반납할 방법이 필요하다.

1번은 우리가 직접 수행한다. `String::from`을 호출하면, 구현부분에서 필요한 만큼의 메모리를 요청한다.

하지만 2번은 다르다. Garbage Collector(GC)를 갖고 있는 언어들은, GC가 더이상 사용하지 않는 메모리 조각들을 계속해서 찾고 지워주며, 우리는 이러한 생각을 할 필요가 없다. GC가 없을 경우, 할당받은 메모리가 더이상 필요없는 시점을 알아서 명시적으로 이를 반납하는 코드를 호출하는 것은 프로그래머의 책임이다. 프로그래머가 이를 잊어버리면 메모리를 낭비하는 셈이다. 너무 빨리 반납해버리면 유효하지 않은 변수를 갖게 될 것이다. 반납을 두 번 한다면 이것 또한 버그다. 프로그래머는 딱 한번의 `allocate` 와 한번의 `free` 쌍을 사용해야 한다.

Rust는 다른 방식으로 사용하지 않는 메모리를 처리한다. 메모리는 변수가 소속되어 있는 스코프 밖으로 벗어나는 순간 자동으로 반납된다.

```rust
{
		let s = String::from("hello"); // s는 여기서부터 유효하다.

    // s를 가지고 뭔가 할 수 있다.
}                                  // 이 스코프는 끝났고, s는 더 이상
                                   // 유효하지 않다.
```

`String`이 요구한 메모리를 운영체제에게 반납하는 지점이 있다. 바로 `s` 가 스코프 밖으로 벗어날 때다. 변수가 스코프 밖으로 벗어나면 Rust는 `drop`함수를 호출한다. Rust는 `}` 괄호가 닫힐때 자동적으로 `drop`을 호출한다.

---

### 이동(move) : 변수와 데이터가 상호작용 하는 방법

여러 개의 변수들은 Rust에서 서로 다른 방식으로 같은 데이터에 대해 상호작용할 수 있다.

```rust
let x = 5;
let y = x;
```

“정수값 5를 `x`에 bind 하고 `x`의 값의 복사본을 만들어 `y`에 묶는다” 이제 `x`와 `y` 두 개의 변수를 갖게 되었고, 둘다 `5`와 같다. 정수값이 결정되어 있는 고정된 크기의 단순한 값이고 `5`라는 값들이 스택에 푸쉬되기 때문에 실제로도 맞다. 하지만 `String` 타입은 조금 다르다.

```rust
let s1 = String::from("hello");
let s2 = s1;
```

이 코드는 위에 코드와 비슷해보이지만 다르게 동작한다.

`String` 타입은 오른쪽 사진과 같이 생겼다. `String`은 그림의 왼쪽과 같이 세 개의 부분으로 이루어져 있다.

1. 문자열의 내용물을 담고 잇는 메모리의 포인터
2. 내용물의 길이값
3. 내용물의 용량값

이 데이터는 스택에 저장된다. 하지만 내용물을 담은 오른쪽은 힙 메모리에 있다.

내용물의 길이값은 바이트 단위로 `String`의 내용물이 얼마나 메모리를 현재 사용하고 있는지 나타낸다.

내용물의 용량값은 운영체제로부터 얼마나 많은 양의 메모리를 할당 받았는지를 말한다. 이 길이와 용량의 차이는 중요하다.

![trpl04-01.svg](Rust/trpl04-01.svg)

---

`s2`에 `s1`을 대입하면 `String` 데이터가 복사되는데, 이는 `스택`에 있는 포인터, 길이값, 그리고 용량값이 복사된다는 의미다. 포인터가 가리키고 있는 `힙` 메모리 상의 데이터는 복사되지 않는다. 다시 말하면 메모리 내의 데이터 구조는 다음과 같다.

![trpl04-02.svg](Rust/trpl04-02.svg)

이 그림은 만약 Rust가 `힙` 메모리 상의 데이터까지도 복사한다면 벌어질 일이다.

만일 Rust가 이렇게 동작한다면, 힙 안의 데이터가 클 경우 `s2 = s1` 연산은 매우 느려질 가능성이 있다.

![trpl04-03.svg](Rust/trpl04-03.svg)

---

앞서 변수가 스코프 밖에서 벗어날 때, Rust는 자동적으로 `drop` 함수를 호출하여 해당 변수가 사용하는 `힙` 메모리를 제거한다고 했다. 하지만 복사후 두 데이터 포인터가 모두 같은 곳을 가르키고 있다. 이것은 문제다. `s2`와 `s1`이 이 스코프 밖으로 벗어나게 되면, 둘 다 같은 메모리를 해체하려 할 것이다. 이는 두번 해체(double free) 오류라고 알려져 있으며 앞서 언급했던 메모리 안정성 버그 중 하나다. 메모리를 두 번 해체하는 것은 메모리 손상(memory corruption) 의 원인이 되는데 이는 보안 취약성 문제를 일으킬 가능성이 있다.

메모리의 안정성을 보장하기 위해 Rust에서는 한 가지 디테일이 있다. 할당된 메모리를 복사하는 것을 시도하는 대신, Rust에서는 `s1`이 더이상 유효하지 않다고 간주한다. 그러므로 Rust는 `s1`이 스코프 밖으로 벗어났을 때 아무것도 해체할 필요가 없다. 따라서 다음처럼 `s1`을 `s2`가 만들어진 후에 사용하려고 하면 에러가 발생한다.

```rust
let s1 = String::from("hello");
let s2 = s1;
println!("{}, world!", s1);
```

에러가 발생하는 이유는 Rust가 유효하지 않은 참조자를 사용하는 것을 막기 때문이다. 보통 프로그래밍을 하는 동안 얕은 복사(shallow copy)와 깊은 복사(deep copy)라는 용어를 들어봤을 것이다. 데이터의 복사 없이 포인터와 길이값 및 용량값만 복사한다는 개념이 얕은 복사와 비슷하게 생각할 수도 있다. 하지만 Rust는 첫 번째 변수를 무효화 시키기도 하기 때문에, 이를 얕은 복사라고 하지 않고 이동(`move`)라 한다. 여기서 `s1`이 `s2`로 이동되었다 라고 말해야한다.

이렇게 한다면 오직 `s2`만 유효한 상황에서 스코프 밖으로 벗어나면 혼자 메모리를 해체할 것이고, 이제 아무런 문제가 없다.

여기에 더해서 Rust는 절대 자동적으로 데이터에 대한 “깊은” 복사본을 만들지 않는다. 그러므로, 어떠한 자동적인 복사라도 런타임 실행과정에서 효율적이라 생각할 수 있다.

`s1`이 무효화된 후의 메모리 구조

![trpl04-04.svg](Rust/trpl04-04.svg)

---

### 클론 (Clone): 변수와 데이터가 상호작용하는 방법

만일 `String`의 스택 데이터 만이 아니라, 힙 데이터를 깊이 복사하기를 정말 원한다면, `clone`이라 불리우는 공용 메소드를 사용하면 된다. 이 메소드는 많은 프로그래밍 언어들 사이에서 흔한 특성이다.

```rust
let s1 = String::from("hello");
let s2 = s1.clone();
println!("s1 = {}, s2 ={}", s1, s2);
```

이 코드는 잘 작동한다. 물론 `clone`은 데이터의 크기에 따라 어떤 비용이 많이 들어갈지 모른다.

---

### 복사 : 스택에만 있는 데이터

```rust
let x = 5;
let y = x;

println!("x = {}, y = {}", x, y);
```

이 코드는 잘 작동한다. 앞서 배운 내용과 충돌되는 것처럼 보인다. `clone`을 호출하지 않았지만 `x`도 유효하며 `y`로 이동하지도 않았다.

그 이유는 정수형과 같이 컴파일 할때 이미 결정되어 있는 크기의 타입은 모두 `스택`에 저장되기 때문에 실제 값의 복사본이 빠르게 만들어질 수 있다. 이는 변수 `y`가 생성된 후에 `x`가 더 이상 유효하지 않도록 해야할 이유가 없다는 것이다. 다시 말하면 여기서는 깊은 복사와 얕은 복사간의 차이가 없다는 것으로 `clone`을 호출하는 것이 보통의 얕은 복사와 아무런 차이가 없다는 것이다.

Rust는 정수형과 같이 스택에 저장할 수 있는 타입에 대해 달수 있는 `Copy` 트레잇이라고 불리우는 특별한 어노테이션(annotation)을 가지고 있다. 만일 어떤 타입이 `Copy` 트레잇을 갖고 있다면, 대입 과정 후에도 예전 변수를 계속 사용할 수 있다. Rust는 만일 그 타입 혹은 그 타입이 가지고 있는 부분 중에서 `Drop` 트레잇을 구현한 것이 있다면 `Copy` 트레잇을 어노테이션 없게끔 한다. 만일 어떤 타입이 스코프 밖으로 벗어날 때 어떤 특수한 동작을 필요로 하고 우리가 그 타입에 대해 `Copy` 어노테이션을 추가한다면, 컴파일 타임 오류를 보게 된다.

`Copy`가 되는 타입은 일반적인 규칙으로서 단순한 스칼라 값들의 묶음은 `Copy`가 가능하고, 할당이 필요하거나 어떤 자원의 형태인 경우 `Copy`를 사용할 수 없다. `Copy`가 가능한 몇가지 타입을 나열해보면

-   `u32` 와 같은 모든 정수형 타입들
-   `true` 와 `false` 값을 갖는 boolean 타입 `bool`
-   `f64` 와 같은 모든 부동 소수점 타입들
-   `Copy` 가 가능한 타입들로만 구성된 튜플들. `(i32, i32)` 는 `Copy`가 되지만, `(i32, String)` 은 안된다.

---

### 소유권과 함수

함수에게 값을 넘기는 의미는 값을 변수에 대입하는 것과 유사하다. 함수에게 변수를 넘기는 것은 대입과 마찬가지로 이동하거나 복사될 것이다.

```rust
fn main() {
    let s = String::from("hello");  // s가 스코프 안으로 들어왔다.

    takes_ownership(s);             // s의 값이 함수 안으로 이동했다...
                                    // ... 그리고 이제 더이상 유효하지 않다.
    let x = 5;                      // x가 스코프 안으로 들어왔다.

    makes_copy(x);                  // x가 함수 안으로 이동했다만,
                                    // i32는 Copy가 되므로, x를 이후에 계속
                                    // 사용해도 됩니다.

} // 여기서 x는 스코프 밖으로 나가고, s도 그 후 나간다. 하지만 s는 이미 이동되었고,
  // x는 스택프레임에서 pop된다.

fn takes_ownership(some_string: String) { // some_string이 스코프 안으로 들어왔다.
    println!("{}", some_string);
} // 여기서 some_string이 스코프 밖으로 벗어났고 `drop`이 호출된다. 힙 상의 메모리는
  // 해제되었다.

fn makes_copy(some_integer: i32) { // some_integer이 스코프 안으로 들어왔다.
    println!("{}", some_integer);
} // 여기서 some_integer가 스코프 밖으로 벗어났다. some_integer는 스텍프레임에서 pop된다.
```

---

### 반환 값과 스코프

값의 반환 또는 소유권을 이동시킨다.

```rust
fn main() {
    let s1 = gives_ownership();         // gives_ownership은 반환값을 s1에게
                                        // 이동시킨다.

    let s2 = String::from("hello");     // s2가 스코프 안에 들어왔다.

    let s3 = takes_and_gives_back(s2);  // s2는 takes_and_gives_back 안으로
                                        // 이동되었고, 이 함수가 반환값을 s3으로도
                                        // 이동시켰다.

} // 여기서 s3는 스코프 밖으로 벗어났으며 drop이 호출된다. s2는 스코프 밖으로
  // 벗어나 스택프레임에서 pop된다. s1은 스코프 밖으로 벗어나서 drop이 호출된다.

fn gives_ownership() -> String {             // gives_ownership 함수가 반환 값을
                                             // 호출한 쪽으로 이동시킨다.

    let some_string = String::from("hello"); // some_string이 스코프 안에 들어왔다.

    some_string                              // some_string이 반환되고, 호출한 쪽의
                                             // 함수로 이동된다.
}

// takes_and_gives_back 함수는 String을 하나 받아서 다른 하나를 반환한다.
fn takes_and_gives_back(a_string: String) -> String { // a_string이 스코프
                                                      // 안으로 들어왔다.

    a_string  // a_string은 반환되고, 호출한 쪽의 함수로 이동된다.
}
```

---

변수의 소유권은 모든 순간 똑같은 패턴을 따른다. 어떤 값을 다른 변수에 대입하면 값이 이동된다. 힙에 데이터를 갖고 있는 변수가 스코프 밖으로 벗어나면, 해당 값은 데이터가 다른 변수에 의해 소유되도록 이동하지 않는한 `drop`에 의해 제거될 것이다.

만약 함수에게 값을 사용할 수 있도록 하되 소유권은 갖지 않도록 하고 싶다면, 함수의 본체로부터 얻어진 결과와 더불어 넘겨주고자 하는 어떤 값을 다시 쓰고 싶어서 함께 반환받아야 한다면 짜증날 것이다.

아래와 같이 튜플을 이용해 값을 돌려받는 것이 가능하다.

```rust
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len()함수는 문자열의 길이를 반환한다.

    (s, length)
}
```

하지만 이건 너무 많이 나간 의례절차고 일반적으인 개념으로서 과한 작업이다. 하지만 Rust는 이를 위한 기능을 갖고 있으며 이를 참조라(references)라고 한다.

---

## 참조자(References)와 빌림(Borrowing)

위의 문제를 해결하기 위해 값의 소유권을 넘기는 대신 개체에 대한 참조자(reference)를 인자로 사용하는 `calculate_length` 함수를 정의하고 이용하는 방법이 있다.

```rust
fn main() {
    let s1 = String::from("hello");
    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

첫 번째로 변수 선언부와 함수 반환값에 있던 튜플 코드가 모두 사라졌다. 두 번째로, `calculate_length`함수에 `&s1`을 넘기고, 함수의 정의 부분에는 `String`이 아니라 `&String`을 이용했다는 점이다.

이 엠퍼센드(`&`)기호가 reference이며 이는 소유권을 넘기지 않고 참조할 수 있도록 해준다.

`String s1` 을 가리키고 있는 `&String s`

![trpl04-05.svg](Rust%206c87bc32962b444cb4dc5be72e8029d7/trpl04-05.svg)

---

```rust
let s1 = String::from("hello");
let len = calculate_length(&s1);
```

`&s1` 문법은 `s1` 의 값을 참조하지만 소유 하지는 않는 reference를 생성하도록 한다. 소유권을 갖고 있지는 않기 때문에, 이 reference가 가리키는 값은 reference가 스코프 밖으로 벗어났을 때도 메모리가 반납되지 않을 것이다.

비슷한 이치로, 함수 시그니처도 `&`를 사용하여 인자 `s`의 타입이 참조자라는 것을 나타내고 있다.

```rust
fn calculate_length(s: &String) -> usize { // s는 String의 참조자다
    s.len()
} // 여기서 s는 스코프 밖으로 벗어났다. 하지만 가리키고 있는 값에 대한 소유권이 없기
  // 때문에, 아무런 일도 발생하지 않는다.
```

변수 `s` 가 유효한 스코프는 여느 함수의 파라미터의 스코프와 동일하지만, 소유권을 갖고 있지 않으므로 이 reference가 스코프 밖을 벗어났을 때 reference가 가리키고 있는 값은 버리지 않는다. 또한 실제 값 대신 reference를 파라미터로 갖고 있는 함수는 소유권을 갖고 있지 않기 때문에 소유권을 되돌려주기 위해 값을 다시 반환할 필요도 없다.

---

함수의 파라미터로 reference를 만드는 것을 빌림(Borrowing)이라고 부른다. 실제 생활에서 어떤 사람이 뭔가를 소유하고 있다면 우리는 그것을 빌릴 수 있다. 용무가 끝났을 때는 그것을 돌려주어야 한다. 그런데 만일 우리가 빌린 무언가를 고치려고 시도한다면 에러를 발생시키며 작동이 안된다.

```rust
fn main() {
    let s = String::from("hello");

    change(&s);
}

fn change(some_string: &String) {
    some_string.push_str(", world");
}
```

```
error: cannot borrow immutable borrowed content `*some_string` as mutable
 --> error.rs:8:5
  |
8 |     some_string.push_str(", world");
  |     ^^^^^^^^^^^
```

---

변수가 기본적으로 불변인 것처럼 reference도 마찬가지다. 참조하는 어떤 것을 변경하는 것은 허용되지 않는다.

---

### 가변 참조자(Mutable References)

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

먼저 `s` 를 `mut` 로 바꿔야 한다. 그리고 `&mut s` 로 가변 reference를 생성하고 `some_string: &mut String` 으로 이 가변 reference를 받아야 한다.

가변 reference는 딱 한가지 제한이 있다. 특정한 스코프 내에 특정한 데이터 조각에 대한 가변 reference를 딱 하나만 만들 수 있다는 것이다. 오른쪽 코드는 에러가 난다.

```rust
let mut s = String::from("hello");

let r1 = &mut s;
let r2 = &mut s;
```

```
error[E0499]: cannot borrow `s` as mutable more than once at a time
 --> borrow_twice.rs:5:19
  |
4 |     let r1 = &mut s;
  |                   - first mutable borrow occurs here
5 |     let r2 = &mut s;
  |                   ^ second mutable borrow occurs here
6 | }
  | - first borrow ends here
```

---

이 제한 사항이 가지는 이점은 바로 Rust가 컴파일 할때 데이터 레이스(date race)를 방지할 수 있도록 해준다.

데이터 레이스는 다음 세 가지 동작이 발생했을때 나타나는 특정한 레이스 조건이다.

1. 두 개 이상의 포인터가 동시에 같은 데이터에 접근한다.
2. 그 중 적어도 하나의 포인터가 데이터를 쓴다.
3. 데이터에 접근하는데 동기화를 하는 어떠한 메커니즘도 없다.

데이터 레이스는 정의되지 않은 동작을 일으키고 런타임에 이를 추적하고자 할 때는 이를 고치기 어렵다. Rust는 데이터 레이스가 발생할 수 있는 코드가 컴파일 조차 안되기 때문에 문제의 발생을 막는다.

새로운 스코프를 만들기 위해 중괄호를 사용하는데, 이를 이용하면 가변 reference 를 동시에 만드는 것이 아니게 해준다. 따라서 여러개의 가변 reference를 만들 수 있도록 해준다.

```rust
let mut s = String::from("hello");

{
    let r1 = &mut s;

} // 여기서 r1은 스코프 밖으로 벗어났으므로, 우리는 아무 문제 없이 새로운 참조자를 만들 수 있다.

let r2 = &mut s;
```

---

가변 reference와 불변 reference를 혼용할 경우에 대한 규칙이 있다.

아래의 코드는 오류가 발생한다.

```rust
let mut s = String::from("hello");

let r1 = &s; // 문제 없음
let r2 = &s; // 문제 없음
let r3 = &mut s; // 큰 문제
```

---

우리는 불변 reference를 만들었다면 동시에 가변 reference를 만들 수 없다. 불변 reference의 사용자는 이를 사용하는 동안에 값이 갑자기 바뀌라고는 예상하지 않는다. 하지만 여러개의 불변 reference는 만들 수 있는데, 데이터를 읽기만 하는것은 다른 것들이 그 데이터를 읽는데에 어떠한 영향도 주지 못하기 때문이다.

```
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as
immutable
 --> borrow_thrice.rs:6:19
  |
4 |     let r1 = &s; // 문제 없음
  |               - immutable borrow occurs here
5 |     let r2 = &s; // 문제 없음
6 |     let r3 = &mut s; // 큰 문제
  |                   ^ mutable borrow occurs here
7 | }
  | - immutable borrow ends here
```

---

### 댕글링 참조자(Dangling References)

포인터가 있는 언어에서는 자칫 잘못하면 댕글링 포인터(dangling pointer) 를 만들기 쉬운데, 댕글링 포인터란 어떤 메모리를 가리키는 포인터를 보존하는 동안, 그 메모리를 해제함으로써 다른 개체에게 사용하도록 줘버렸을 지도 모를 메모리를 참조하고 있는 포인터를 말한다. 이와 반대로, Rust에서는 컴파일러가 모든 reference가 댕글링 reference가 되지 않도록 보장해준다. 만일 우리가 어떤 데이터의 reference를 만들었다면, 컴파일러는 그 reference가 스코프 밖으로 벗어나기 전에는 데이터가 스코프 밖으로 벗어나지 않을 것임을 확인해 줄 것이다.

```rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s
}
```

위 코드의 오류 메세지다.

```
error[E0106]: missing lifetime specifier
 --> dangle.rs:5:16
  |
5 | fn dangle() -> &String {
  |                ^^^^^^^
  |
  = help: this function's return type contains a borrowed value, but there is no
    value for it to be borrowed from
  = help: consider giving it a 'static lifetime

error: aborting due to previous error
```

해석해보면 “이 함수의 변환 타입은 빌린 값을 포함하고 있는데, 빌려온 실제 값은 없다.”

```rust
fn dangle() -> &String { // dangle은 String의 참조자를 반환한다

    let s = String::from("hello"); // s는 새로운 String이다

    &s // String s의 참조자를 반환한다.
} // 여기서 s는 스코프를 벗어나고 버려진다. 이것의 메모리는 사라진다.
  // 위험!
```

---

`s`가 `dangle`안에서 만들어졌기 때문에, `dangle`의 코드가 끝이나면 `s`는 할당 해제 된다. 하지만 이것의 reference를 반환하려고 했다. 이는 곧 reference가 무효화된 `String`을 가리키게 될 것이므로 Rust는 에러를 발생시켜 이런 짓을 못하게 한다.

여기서 해법은 `String`을 직접 반환하는 것이다.

```rust
fn no_dangle() -> String {
    let s = String::from("hello");

    s
}
```

---

### 참조자(References)의 규칙

정리를 해보면

1. 어떠한 경우이든 간에 다음 중 하나만을 가질 수 있다.
    - 하나의 가변 Reference
    - 임의 개수의 불변 Reference
2. reference는 항상 유효 해야만 한다.

---

## 슬라이스(Slices)

슬라이스는 소유권을 갖지 않는 또 다른 타입이다. 슬라이스는 컬렉션(collection) 전체가 아닌 컬렉션의 연속된 일련의 요소들을 참조할 수 있게 한다.

스트링을 입력받아 그 스트링에서 첫번째 단어를 반환하는 함수를 작성해본다고 하자. 만약 함수가 공백문자를 찾지 못한다면, 이는 스트링 전체가 한 단어라는 의미이고, 이때는 전체 스트링이 반환되어야 한다.

그렇다면 함수의 시그니처에 대해 생각해봤을때 무슨 타입으로 반환 해야 할까? > `fn first_word(s: &String) → ?`

일단 함수는 `&String` 을 파라미터로 받는다. 소유권을 원하지 않으므로 이렇게 해도 상관이 없다. 여태 배운바로는 스트링의 일부를 표현할 방법이 없다. 하지만 약간의 꼼수로 단어의 끝부분의 인덱스를 반환할 수 있다.

```rust
fn first_word(s: &String) -> usize {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return i;
        }
    }
    s.len()
}
```

입력된 `String` 을 요소별로 보면서 그 값이 공백인지 확인할 필요가 있기 때문에 `String` 은 `as_byte` 메소드를 이용해서 바이트 배열로 바꾼다. 그 다음으로 `iter` 메소드를 이용해 바이트 배열의 반복자를(iterator) 를 생성한다.

`enumerate` 메소드는 `iter` 의 결과값을 직접 반환하는 대신 이를 인덱스와 함께 감싸서 튜플로 만들어 반환한다. 따라서 우리는 이 튜플을 해체하기 위해 패턴을 사용할 수 있다. i 는 튜플 내의 인덱스에 대응하고 `&item` 은 튜플 내의 한 바이트에 대응한다. `.iter().enumerate()`의 요소에 대한 레퍼런스를 갖는 것 이므로, `&` 를 사용했다.

이후 바이트 리터럴 문법을 이용해 공백문자를 찾는다. 공백 문자를 찾았다면, 이 위치를 반환한다. 그렇지 않으면 `s.len()` 을 통해 스트링의 길이값을 반환한다.

위 코드는 `usize` 를 반환하고 있다. 이는 `&String` 의 내용물 안에서만 의미가 있다. 이것이 바로 문제다. `String` 으로 부터 분리되어 있는 숫자 이기 때문에 나중에 `String` 이 변하게 되면 결과값이 유효한지 보장할 수 없다.

---

```rust

fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s); // word는 5를 갖게 될 것이다.

    s.clear(); // 이 코드는 String을 비워서 ""로 만들게 된다.

    // word는 여기서 여전히 5를 갖고 있지만, 5라는 값을 의미있게 쓸 수 있는 스트링은 이제 없다.
    // word는 이제 완전 유효하지 않다!
}
```

왼쪽 프로그램은 아무론 오류 없이 컴파일되고, `s.clear()` 를 호출한 뒤 `word`를 사용한다 해도 컴파일이 될 것이다. `word` 와 `s` 는 전혀 연결되어 있지 않은 변수이므로, `word` 는 여전히 `5` 를 담고 있다. 첫번째 단어를 추출하기 위해 `s` 와 `5` 를 사용할 수 있지만 이후에 `s`의 내용물이 변경 되었기 때문에 이러한 사용은 버그가 될 것이다.

이러한 오류는 매우 자주 발생할 수 있는 오류다. 만약 두번째 단어를 추출하는 `second_word` 함수를 작성했을때 더더욱 다루기 어려워진다. 이 함수의 시그니처는 아마 다음과 같을 것이다.

`fn second_word(s: &String) → (usize, usize) {`

첫 단어와 다르게 두번째 단어는 시작지점과 끝지점의 인덱스를 추출할 것이다. 매개변수로 들어온 데이터로부터 계산 되었지만 그 상태와 전혀 묶여 있지 않은걸 하나 더 갖게 되었다. 데이터와 그 데이터로부터 추출한 특정 데이터의 동기화를 유지할 필요가 있다는 것이다.

이런 상황을 위해 Rust 는 스트링 슬라이스(String slice)를 제공한다.

---

### 스트링 슬라이스

스트링 슬라이스는 `String` 의 일부분에 대한 레퍼런스다.

```rust
let s = String::from("hello world");
let hello = &s[0..5];
let world = &s[6..11];
```

이는 스트링 전체 `String` 의 레퍼런스를 갖는 것과 비슷하지만, 그 뒤에 `[0..5]` 라는 코드가 붙어있다. 전체 `String` 에 대한 레퍼런스가 아니라 `String` 의 일부분에 대한 레퍼런스다.

대괄호 내에 `[starting_inx..ending_index]` 를 이용해 특정한 범위의 슬라이스를 만들 수 있다. 내부적으로 슬라이스 데이터 구조는 시작 위치와 슬라이스의 길이를 저장한다. 이 길이의 값은 `ending_index` 에서 `starting_index` 를 뺀 값이다. 따라서 `let world = &[6..11];` 의 경우 `world` 는 `s` 의 6번째 바이트를 가리키고 있는 포인터와 길이값 5를 갖고 있는 슬라이스가 될 것이다.

Rust의 .. 문법을 사용할 때 첫번째 인덱스 즉 0번 인덱스에서부터 시작하길 원한다면, 두 개의 마침표 전의 값을 생략할 수 있다. 다음 두 줄은 동일한 표현이다.

-   `let slice = &s[0..2];`
-   `let slice = &s[..2];`

비슷한 이치로 `String` 의 마지막 바이트까지 포함한다면 끝 숫자를 생각할 수 있다.

-   `let slice = &s[3..s.len()];`
-   `let slice = &s[3..];`

둘 다 응용하면 전체 스트링의 슬라이스를 만들기 위해 양쪽 값을 모두 생략할 수 있다.

-   `let slice = &s[0..s.len()];`
-   `let slice = &s[..];`

“스트링 슬라이스” 를 나타내는 타입은 `&str` 으로 쓴다.

`String` 의 일부를 참조하는 스트링 슬라이스

![trpl04-06.svg](Rust%206c87bc32962b444cb4dc5be72e8029d7/trpl04-06.svg)

---

자 이번엔 스트링 슬라이스를 함수의 반환 타입으로 하고 `first_word` 함수를 작성해 보자.

```rust
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

공백 문자를 찾으면, 스트링의 시작과 공백 문자의 인덱스를 각각 시작과 끝 인덱스로 사용하여 스트링 슬라이스를 반환한다. 처음 `first_word` 함수와 다른점은 반환값이 처음 전달인자로 들어온 데이터와 묶여있는 하나의 값을 반환했다는 것이다.

`second_word` 함수에서도 마찬가지로 잘 작동할 것이다.

```rust
fn second_word(s: &String) -> &str {
```

자 이제 엉망이 되기 힘든 훨씬 직관적인 API를 갖게 되었다. 이제 컴파일러가 `String` 에 대한 레퍼런스들이 유효한 상태로 남아있게끔 보장해줄 것이다. [이 코드](Rust%206c87bc32962b444cb4dc5be72e8029d7.md)는 논리적으로 맞지 않지만 이상없이 컴파일되고 실행된다. 이후에 변경된 스트링에 대한 첫번째 단어의 인덱스에 접근하고자 할 경우에나 오류가 발생할 것이다. 스트링 슬라이스는 이러한 버그를 불가능하게 만들고 코드 내에서 발생할 수 있는 문제를 훨씬 일찍 알게 해준다.

---

[위 코드](Rust%206c87bc32962b444cb4dc5be72e8029d7.md)와 다르게 정상적으로 오류를 발생시킨다.

```rust
fn main() {
    let mut s = String::from("hello world");
    let word = first_word(&s);
    s.clear(); // Error!
    println!("the first word is: {}", word);
}
```

---

Borrowing 에서 배웠듯이 무언가에 대한 불변 reference를 만들었을 경우, 가변 reference를 만들 수 없다.

`clear` 함수가 `String` 을 잘라내기 위해 이 함수는 가변 reference를 갖기 위한 시도를 할것이고, 이미 불변 reference가 존재하기 때문에 이는 실패한다.

오류 메세지

```
17:6 error: cannot borrow `s` as mutable because it is also borrowed as
            immutable [E0502]
    s.clear(); // Error!
    ^
15:29 note: previous borrow of `s` occurs here; the immutable borrow prevents
            subsequent moves or mutable borrows of `s` until the borrow ends
    let word = first_word(&s);
                           ^
18:2 note: previous borrow ends here
fn main() {

}
^
```

---

### 스트링 리터럴은 슬라이스다

스트링 리터럴은 하드코딩되어 바이너리 안에 저장된다고 했었다. 이제 슬라이스에 대해 알았으니 스트링 리터럴에 대해 더 자세히 이해할 수 있다.

`let s = “Hello, world!”;`

여기서 `s` 의 타입은 `&str` 이다. 이것은 바이너리의 특정 지점을 가리키고 있는 슬라이스다. 왜 스트링 리터럴이 불변 reference인지도 설명할 수 있다. `&str` 은 불변 reference이기 때문이다.

### 파라미터로서의 스트링 슬라이스

이제 스트링 리터럴과 `String` 타입 모두의 슬라이스를 얻을 수 있다는 것을 알게 되었다면 `first_word` 함수를 더 개선시킬 수 있다.

이전 `first_word` 함수

```rust
fn first_word(s: &String) -> &str {
```

개선 후 `first_word` 함수

```rust
fn first_word(s: &str) -> &str {
```

이렇게 개선하는 이유는 `&String` 과 `&str` 모두에 대한 같은 함수를 적용시킬 수 있게 해주기 때문이다.

만약 스트링 슬라이스를 갖고 있다면, 바로 넘길 수 있다. `String` 을 가지고 있다면, 이 `String` 의 전체 슬라이스(`[..]`)를 넘기면 된다. 함수가 `String` 의 참조자, 즉 `&String` 대신 스트링 슬라이스를 갖도록 정의하는 것은 API를 아무런 손해없이 더 일반적이고 유용하게 해준다.

---

### 그 밖의 슬라이스

스트링 슬라이스는 말 그대로 스트링에 특정되어 있다. 하지만 더 일반적인 슬라이스 타입도 있다.

```rust
let a = [1, 2, 3, 4, 5];
```

스트링의 일부를 참조하고 싶어할 수 있는 것처럼 배열의 일부를 참조하고 싶을 수 있다.

그렇다면 다음과 같이 쓸 수 있다.

```rust
let slice = &a[1..3];
```

이 슬라이스는 `&[i32]` 타입을 갖는다. 이는 스트링 슬라이스가 동작하는 방법과 똑같이, 슬라이스의 첫번째 요소에 대한 reference와 슬라이스의 길이를 저장하는 방식으로 동작한다. 이 외에도 다른 모든 종류의 컬렌션들에 대해 이렇게 슬라이스를 이용할 수 있다.

---

## 정리

소유권, 빌림, 그리고 슬라이스의 개념은 Rust 프로그램의 메모리 안정성을 컴파일 타임에 보장하는 것이다. Rust는 다른 시스템 프로그래밍 언어와 같이 메모리 사용에 대한 제어권을 주지만, 데이터의 소유자가 스코프 밖으로 벗어났을 때 소유자가 자동적으로 데이터를 버리도록 하는 것은 곧 프로그래머가 메모리 사용 제어를 위해 추가적인 코드 작성이나 디버깅을 하지 않아도 된다는 뜻이다.

---

# 구조체

구조체(`struct`)는 사용자들이 연관된 여러 값들을 묶어서 의미있는 데이터 단위를 정의할 수 있게해준다. 튜플과 구조체를 비교해 보고, 구조체를 어떻게 사용하는지, 메소드와 구조체 데이터 동작과 관련된 연관 함수의 정의 방법에 대해 알아보자. 구조체와 열거형에 대한 개념은 프로그램 도메인 상에서 새로운 타입을 만들기 위한 기초 재료로서, Rust의 컴파일 시점 타입 검사 기능을 최대한 활용한다.

## 구조체를 정의하고 초기화 하기

구조체는 [튜플](Rust%206c87bc32962b444cb4dc5be72e8029d7.md)과 비슷하다. 튜플처럼 구조체의 구성요소들을 각자 다른 타입을 지닐 수 있다. 그러나 튜플과는 다르게 각 구성요소를 명명할 수 있다. 즉 값이 의미하는 바를 명확하게 인지할 수 있다. 바로 이 점이 튜플보다 유연하게 다룰 수 있는 차이점이다. 구조체 내의 특정 인스턴스 값을 지정하거나 데이터의 순서에 의존할 필요가 없다.

```rust
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}
```

구조체를 정의할 때는 `struct` 키워드를 먼저 입력하고 명명할 구조체의 이름을 입력하면 된다. 구조체의 이름은 함께 묶이게 되는 구성요소들의 의미를 내포할 수 있도록 짓는 것이 좋다. 이름 뒤에 중괄호 안에서는 필드(_field_)라 불리는 각 구성요소들의 타입과 접근할 수 있는 이름을 정의한다.

```rust
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
```

정의한 구조체를 사용하려면, 각 필드의 값을 적은 인스턴스(_instance_)를 생성해야 한다. 구조체의 이름을 명시해서 사용할 수 있고, 필드를 식별할 수 있는 이름인 키와 그 키에 저장하고자 하는 값의 쌍(`key:value`)을 이어지는 중괄호 안에 추가해 생성할 수 있다.

구조체를 정의할 때 **필드들의 순서가 정의한 필드의 순서와 같을 필요는 없다.** 다시 말하면 구조체 정의는 무엇이 들어가야 하는지 대략적으로 정의된 양식으로 생각하면 되고, 인스턴스는 그것에 특정한 값을 넣어 실체화한 것이다.

```rust
	let mut user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};

user1.email = String::from("anotheremail@example.com");
```

구조체에서 특정한 값을 읽어오려면, 점(`.`) 표기법을 사용하면 된다. 사용자의 이메일 값을 얻고자 한다면, `user1.email` 과 같은 방식으로 접근할 수있다. 변경이 가능한 구조체 인스턴스에 들어있는 값을 바꾸고자 할 때는 점(`.`) 표기법을 사용하여 특정 필드에 새 값을 할당할 수 있다.

`mut` 키워드를 사용해 생성한 인스턴스내의 모든 필드는 반드시 `변경 가능(mutable)` 해야 한다. Rust에서는 특정 필드만 변경할 수 있도록 하지 않는다. 다른 표현식과 마찬가지로, 함수 본문의 마지막에 새 인스턴스 구조체를 표현식(expressions)으로 생성하여 새 인스턴스를 바로 반환 할 수 있다.

```rust
fn build_user(email: String, username: String) -> User {
    User {
        email: email,
        username: username,
        active: true,
        sign_in_count: 1,
    }
}
```

주어진 `email` 과 `user_name` 으로 `User` 인스턴스를 반환하는 `build_user` 함수를 보여준다.

`active` 필드는 `true` 값을 가져오고 `sign_in_count` 는 `1` 값을 가져온다.

구조체 필드와 동일한 이름으로 함수 매개 변수의 이름을 지정하는것이 합리적이긴 하지만, email 및 username 필드 이름과 변수를 반복해서 쓰는것은 비효율적이다. 구조체에 더 많은 필드가 많다면, 귀찮을 것이다. 다행이도 관련한 좋은 기능이 있다.

---

### 변수명이 필드명과 같을 때 간단하게 필드 초기화하기

변수명과 구조체의 필드명이 같다면, 필드 초기화 축약법(_field init shorthand_) 을 이용할 수 있다. 이를 활용하면 구조체를 생성하는 함수를 더 간단히 작성할 수 있다. `build_user` 함수에는 `email` 과 `username` 이라는 매개변수가 있다. 함수는 `User` 구조체가 구현된 인스턴스를 반환한다.

```rust
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

매개변수인 `email` 과 `username` 이 `User` 구조체의 필드명과 같기 때문에, `build_user` 함수에서 `email` 과 `username` 을 명시하는 부분을 두 번씩 적을 필요가 없다.

`email` 과 `username` 이 구조체의 필드와 이름이 같아서 함수 내에서 특별히 명시하지 않고 초기화 한다. `email` 필드와 `email` 매개 변수의 이름이 같기 때문에 `email: email` 대신 `email` 만 작성하면 된다.

이러한 방식은 간결한 코드를 작성하는데 도움이 되고, 많은 필드의 값이 정의 되어야 할때 유용하다.

---

### 구조체 갱신법

존재하는 인스턴스에서 기존 값은 대부분은 재사용하고 몇몇 값만 바꿔 새로운 인스턴스를 정의하는 방법은 유용하다.

```rust
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    active: user1.active,
    sign_in_count: user1.sign_in_count,
};
```

왼쪽은 `user2`에 `email` 과 `username` 은 새로 할당하고, 나머지 필드들은 `user1` 의 값들을 그대로 사용하는 방식으로 새 `user` 인스턴스를 생성한다

하지만 구조체 갱신법은 입력으로 주어진 인스턴스와 변화하지 않은 필드들을 명시적으로 할당하지 않기 위해 .. 구문을 사용한다. 아래 코드는 `user1` 인스턴스와 `active`, `sign_in_count` 필드의 값은 같고, `email` 과 `username` 필드들의 값은 다른 `user2` 인스턴스를 생성할 때 구조체 갱신법을 사용한 것을 보여준다.

```rust
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    ..user1
};
```

---

### 튜플 구조체

튜플 구조체는 이름은 없고 필드마다 타입은 다르게 정의 가능하다. 구조체명을 통해 의미를 부여할 수 있으나 필드의 타입만 정의할 수 있고 명명은 할 수 없는, 튜플 구조체라 불리는 튜플과 유사한 구조체도 정의할 수 있다.

튜플 구조체는 일반적인 구조체 정의 방법과 같이 `struct` 키워드를 통해 정의할 수 있고, 튜플의 타입 정의가 키워드 뒤에서 이루어진다. 다음는 튜플 구조체인 `Color`, `Point` 의 정의와 사용 예시다.

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);
```

`Color` 와 `Point` 는 서로 다른 튜플 구조체이기 때문에 서로가 다른 타입이라는 것을 기억해야 한다. 구조체 내의 타입이 모두 동일하더라도 각각의 구조체는 고유의 타입이기 때문에 둘은 다른 타입이다.

---

### 필드가 없는 유사 유닛 구조체

어떠한 필드도 없는 구조체 역시 정의할 수 있다. 이는 유닛 타입인 () 와 비슷하게 동작하고, 그 때문에 유사 유닛 구조체(_unit-like struct_)라 불린다. 유사 유닛 구조체는 특정한 타입의 트레잇(trait)을 구현해야하지만 타입 자체에 데이터를 저장하지 않는 경우에 유용하다. 이 내용들은 이후에 다룬다.

---

### 구조체 데이터의 소유권

이 코드에서의 `User` 구조체 정의에서는 `&str` 문자 슬라이스 타입 대신 `String` 타입을 사용했다. 이는 의도적인 선택으로, 구조체 전체가 유효한 동안 구조체가 그 데이터를 소유하게 하고자 함이다.

구조체가 소유권이 없는 데이터의 reference 를 저장할 수는 있지만 나중에 다룰 라이프타임(_lifetime_)을 전제로 한다. 라이프 타임은 구조체가 존재하는 동안 참조하는 데이터를 계속 존재할 수 있도록 한다. 라이프타임을 사용하지 않고 reference를 저장하고자 하면 에러가 발생한다.

---

## 구조체를 이용한 예제 프로그램

사각형의 넓이를 입력받아서 사각형의 넓이를 계산하는 함수를 만들어보자

```rust
fn main() {
    let length1 = 50;
    let width1 = 30;

    println!(
        "The area of the rectangle is {} square pixels.",
        area(length1, width1)
    );
}

fn area(length: u32, width: u32) -> u32 {
    length * width
}
```

다음 코드의 결과값을 보면 이상없이 결과값을 출력한다.

하지만 `area` 함수를 호출해서 사각형의 넓이를 알아냈지만 길이와 너비는 함께 하나의 사각형에 대한 정보이기 때문에 연관되어 있다.

```rust
fn area(length: u32, width: u32) -> u32 {
```

`area` 함수는 사각형의 넓이를 계산하기로 되어있는데, 작성한 함수는 두 개의 파라미터를 갖고 있다. 파라미너틑 서로 연관된 데이터지만 프로그램 내에 어디에도 그것을 표현한 곳이 없다. 길이와 너비를 함께 묶는다면 더 읽기 쉽고 관리하기도 좋을 것이다.

이를 보완하기 위해 `tuple` 을 사용할 수 있다.

---

```rust
fn main() {
    let rect1 = (50, 30);

    println!(
        "The area of the rectangle is {} square pixels.",
        area(rect1)
    );
}

fn area(dimensions: (u32, u32)) -> u32 {
    dimensions.0 * dimensions.1
}
```

자 프로그램이 더 좋아졌다. `tuple` 은 이제 단 하나의 인자만 넘기게 되었다.

하지만 아직까지도 문제점이 있다. `tuple` 은 요소에 대한 이름이 없어서, 튜플 내의 값을 인덱스로 접근해야 하기 때문에 계산이 더 혼란스러워 졌다.

면적 계산에 대해서는 길이와 너비를 혼동하는 것이 큰 문제가 아니지만, 만약 화면에 사각형을 그리고 싶다면 문제가 된다. `length` 가 인덱스 `0` 이고 `width` 가 인덱스 `1` 이라는 점을 꼭 기억해야 한다. 만일 누군가 이 코드를 이용해서 작업한다면, 그 또한 이 사실을 알아내야 한다.

코드 내에 데이터의 의미를 전달하지 않았기 때문에, 이 값들을 잊어먹거나, 혼동하여 에러를 발생시키는 일이 쉽게 발생할 것이다.

---

```rust
struct Rectangle {
    length: u32,
    width: u32,
}

fn main() {
    let rect1 = Rectangle { length: 50, width: 30 };

    println!(
        "The area of the rectangle is {} square pixels.",
        area(&rect1)
    );
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.length * rectangle.width
}
```

자 이제 구조체를 이용해 데이터에 이름을 붙여 의미를 부여할 수 있다.

옆 코드를 보면 구조체는 전체를 위한 이름 뿐만 아니라 부분들을 위한 이름들도 가지고 있다.

구조체를 정의하고 `Rectangle` 이라 명명했다, `{}` 안에서 `length` 와 `width` 를 필드로 정의 했는데 둘다 `u32` 타입이다. 그런 다음 `main` 함수 안에서 길이 50높이 30인 특정한 `Rectangle` 인스턴스(instance)를 생성했다.

`area` 함수는 이제 하나의 파라미터를 갖도록 정의되었다, 이는 `rectangle` 이라는 이름이고 `Rectangle` 구조체 인스턴스의 불변 reference 다. 구조체의 소유권을 얻기보다는 빌리기만 하면 되므로 `main` 은 그 소유권을 유지하고 `rect1` 을 계속 사용할 수 있다.

`area` 함수는 `Rectangle` 인스턴스 내의 `length` 와 `width` 필드에 접근 할 수 있다. 코드를 보면 알 수 있겠지만 `length` 와 `width` 필드를 사용해 `Rectangle` 의 넓이를 계산한다는 뜻이다. 이것으로 길이와 너비가 서로 연관되어 있음을 잘 전달할 수 있고 0과 1 을 사용한 `tuple` 인덱스 값을 이용하는 대신에 값들에 대해서 직접적으로 이름을 서술한다. 따라서 훨씬 더 명확하다.

---

### 파생 트레잇(derived trait)으로 유용한 기능 추가하기

만약 프로그램을 디버깅하는 동안 구조체 내의 모든 값을 보기 위해서 Rectangle 의 인스턴스를 출력할 수 있다면 아주 유용할 것이다. 하지만 다음과 같은 코드는 오류를 발생시킨다.

```rust
println!("rect1 is {}",rect1);
```

println! 매크로는 다양한 종류의 포멧을 출력할 수 있으며, 기본적으로 {} 는 println! 에게 Display 라고 알려진 포멧팅을 이용하라고 전달해준다. 여태것 봐온 기본 타입들은 Display 가 기본적으로 구현되어 있다. 하지만 구조체를 사용하는 경우, Println! 가 출력을 형식화하는 방법이 명확하지 않다. 사용자가 쉼표를 이용하길 원할수도 있고, 중괄호로 구분하는것을 원할수도 있기때문에 Rust는 따로 추론을 해서 출력해주지 않는다.

```
note: `Rectangle` cannot be formatted with the default formatter; try using
`:?` instead if you are using a format string
```

에러 메세지를 보면 `println!` 매크로 호출을 `println!(”rect1 is {:?}”, rect1);` 으로 해보라고 하고 있다. `{}` 안에 `:?` 명시자를 넣는 것은 `println!` 에게 `Debug` 라 불리는 출력 포맷을 사용하고 싶다고 전달해준다. `Debug` 는 개발자에게 유용한 방식으로 구조체를 출력할 수 있도록 해줘서 디버깅 하는 동안 그 값을 볼 수 있게 해주는 트레잇이다. 하지만 여전히 에러가 난다.

```
error: the trait bound `Rectangle: std::fmt::Debug` is not satisfied
note: `Rectangle` cannot be formatted using `:?`; if it is defined in your
crate, add `#[derive(Debug)]` or manually implement it
```

하지만 이번에도 에러 메세지에 힌트가 있다. Rust는 디버깅 정보를 출력하는 기능을 포함하고 있는 것이 맞지만, 구조체에 대하여 해당 기능을 활성화하도록 명시적인 사전동의를 해야 한다. 따라서 구조체 정의 바로 전에 `#[derive(Debug)]` 어노테이션을 추가해준다.

```rust
#[derive(Debug)]
struct Rectangle {
    length: u32,
    width: u32,
}

fn main() {
    let rect1 = Rectangle { length: 50, width: 30 };

    println!("rect1 is {:?}", rect1);
}
```

출력 시키면 다음과 같은 결과를 얻는다 `rect1 is Rectangle { length: 50, width: 30 }`

이게 제일 좋은 출력은 아니지만 이 인스턴스의 모든 필드의 값을 보여준다. 만약 더 큰 구조체를 다룰땐 `println!` 스트링 내에 `{:?}` 대신 `{:#?}` 를 사용할 수 있다. 만약 `{:#?}` 스타일을 사용하게 되면 다음과 같은 결과를 얻는다.

```
rect1 is Rectangle {
    length: 50,
    width: 30
}
```

---

## 메소드 문법

메소드(_method_) 는 함수와 유사하다. 똑같이 `fn` 키워드와 이름으로 선언되고, 파라미터와 반환값을 갖고 있다. 또한 어딘가로부터 호출 되었을때 실행될 코드를 담고 있다. 하지만 메소드는 함수와는 달리 구조체, 열거형이나 트레잇 객체안에 정의되고 첫번째 파라미터가 언제나 `self` 인데, 이는 메소드가 호출되고 있는 구조체의 인스턴스를 나타낸다.

### 메소드 정의하기

[이전](Rust%206c87bc32962b444cb4dc5be72e8029d7.md)에서 `Rectangle` 인스턴스를 파라미터로 가지고 있는 `area` 함수를 바꿔서 `Rectangle` 구조체 위에서 정의된 `area` 메소드를 만들 수 있다.

```rust
#[derive(Debug)]
struct Rectangle {
    length: u32,
    width: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.length * self.width
    }
}

fn main() {
    let rect1 = Rectangle { length: 50, width: 30 };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```

`Rectangle` 의 내용 안에 함수를 정의하기 위해서, `impl` (_구현: implementation_) 블록을 만든다. 그 다음 `area` 함수를 `impl` 중괄호 안으로 옮기고 시그니처 및 본체 내의 모든 곳에 있는 첫번째 파라미터를 `self` 로 변경한다. `area` 함수를 호출하고 여기에 `rect1` 을 인자를 넘기는 대신에, `Rectangle` 인스턴스 상의 `area` 메소드를 호출하기 위해 메소드 문법 (method syntax) 를 이용할 수 있다. 메소드 문법은 점을 추가하고 그 뒤를 이어 메소드 이름, 괄호, 인자들을 적으면 된다.

`area` 의 시그니처 에는 이전에는 `rectangle: &Rectangle` 이 사용되었지만 지금은 `&self` 가 사용되었다. 이는 메소드가 `impl` `Rectangle` 내용물 안에 위치하고 있어서 Rust가 `self` 의 타입이 `Rectangle` 이라는 사실을 알고 있기 때문이다. 하지만 이전에 `&Rectangle` 이라고 썼던 것 처럼, `self` 앞에도 `&` 를 사용할 필요가 있음을 주목해야 한다. 메소드는 `self` 의 소유권을 가질수도, 이것처럼 `self` 를 불변하게 빌릴수도, 혹은 다른 파라미터와 비슷하게 변경이 가능하도록 빌려올 수도 있다.

이 함수에서는 `&Rectangle` 을 이용한 것과 같은 이유로 `&self` 를 선택했다. 소유권을 가져오는걸 원하지 않고, 구조체 내의 데이터를 읽기만 하고, 쓰고 싶지는 않다. 만약 메소드가 동작하는 과정에서 메소드 호출에 사용된 인스턴스가 변경되길 원한다면 첫번째 파라미터로 `&mut self` 를 써야한다. 그냥 `self` 를 첫번째 파라미터로 사용하여 인스턴스의 소유권을 가져오는 메소드를 작성하는 일은 드물다. 이러한 일은 보통 해당 메소드가 `self` 를 다른 무언가로 변형시키고 이 변형 이후에 호출하는 측에서 원본 인스턴스를 사용하는 것을 막고 싶을 때 종종 쓰인다.

함수 대신 메소드를 사용하며 얻을 수 있는 이점은 모든 메소드를 사용할때 `self` 를 타이핑 하지 않아도 된다는 점과 조직화에 관한 점이다. 이 코드를 향후 사용할 사람들이 `Rectangle` 함수를 사용할 수 있는 지점을 찾는 것보다 하나의 `impl` 블록 내에 해당 타입의 인스턴스로 할 수 있는 것을 보는게 더 편리하다.

<aside>
💡 “->“ 연산자는요?

</aside>

C++ 같은 언어에서는, 메소드 호출을 위해서 두 개의 연산자가 사용된다. 만일 어떤 객체의 메소드를 직접 호출하는 중이라면 `.` 를 이용하고 어떤 객체의 포인터에서의 메소드를 호출하는 중이고 이 포인터를 역참조할 필요가 있다면 `->` 를 사용한다. 만일 `object` 가 포인터라면 `object->something()` 은 `(*object).something()` 과 비슷하다.

하지만 Rust 는 -> 연산자와 같은 연산자를 가지고 있지 않다. 대신 Rust는 자동 참조 및 역참조(_automatic referencing and dereferencing_) 이라는 기능을 가지고 있다.

예를 들자면 `object.something()` 이라고 메소드를 호출했을 때, Rust는 자동적으로 `&`나 `&mut`, 혹은 `*` 를 붙여서 `object` 가 해당 메소드의 시그니처와 맞도록 한다. 다음은 동일한 표현이다.

```rust
p1.distance(&p2);
(&p1).distance(&p2);
```

첫번째 표현이 훨씬 깔끔해보인다. 이러한 자동 참조 동작은 메소드가 명확한 수신자-즉 `self` 의 타입을 가지고 있기 때문에 동작한다. 수신자와 메소드의 이름이 주어질 때, Rust는 해당 메소드가 읽는지(`&self`) 혹은 변형 시키는지 (`&mut self`), 아니면 소비하는지(`self`)를 결정론적으로 알아낼 수 있다. Rust가 메소드 수신자를 암묵적으로 빌리도록 하는 사실은 실사용 환경에서 소유권을 인간공학적으로 만드는 중요한 부분이다.

---

### 더 많은 파라미터를 가진 메소드

이번에는 `Rectangle` 의 인스턴스가 다른 `Rectangle` 인스턴스를 가져와서 이 두번째 `Rectangle` 의 필드들이 `self` 의 필드들보다 전부 작다면 `True` 를 반환하고, 그렇지 않으면 `false`를 반환하고 싶다. 만약 `can_hold` 메소드를 정의했다면 다음처럼 사용할 수 있다.

```rust
fn main() {
    let rect1 = Rectangle { length: 50, width: 30 };
    let rect2 = Rectangle { length: 40, width: 10 };
    let rect3 = Rectangle { length: 45, width: 60 };

    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
}
```

예상 결과값은 `true` 와 `false`다. `rect2` 필드의 모든 값은 `rect1` 필드의 모든 값보다 작기 때문에 `true`를 반환하고 `rect3`은 `rect1`에 비해 가로로 더 넓기 때문에 `false`다.

메소드 `can_hold` 는 `impl Rectangle` 블록 내에 정의할 것이다. 이 메소드는 `self` 외에 또다른 `Rectangle` 의 불변 reference를 파라미터로 갖을 것이다. 파라미터의 타입은 위 코드를 보면 알 수 있다. rect1.can_hold(&rect2) 는 `&rect2` 를 넘기고 있는데, 이는 `Rectangle` 의 인스턴스인 `rect2`의 불변성 빌림이다. 우리는 `rect2` 를 그냥 읽기만 하길 원하기 때문에 타당하다. `can_hold` 의 반환값은 `boolean`이 될 것이며 이 구현은 self의 길이와 너비가 다른 `Rectangle` 의 길이와 너비와 각각을 비교해 검사할 것이다. `impl Rectangle`의 코드는 다음과 같을 것이다.

```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.length * self.width
    }
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.length > other.length && self.width > other.width
    }
}
```

---

### 연관 함수

`impl` 블록의 또다른 유용한 기능은 `self` 파라미터를 갖지 않는 함수도 `impl` 내의 정의하는 것이 가능하다. 이를 연관 함수 (_associated functions_) 라고 부르는데, 그 이유는 이 함수가 해당 구조체와 연관되어 있기 때문이다. 이들은 메소드가 아니라 함수인데, 이는 함께 동작할 구조체의 인스턴스를 가지고 있지 않기 때문이다. 우리는 이미 `String::from` 연관 함수를 사용해본 적이 있다.

연관 함수는 새로운 구조체의 인스턴스를 반환해주는 생성자로서 자주 사용된다. 예를 들면, 하나의 차원값 파라미터를 받아서 이를 길이와 너비 양쪽에 사용하여, 정사각형 `Rectangle` 을 생성할 때 같은 값을 두번 명시하도록 하는 것보다 쉽게 해주는 연관 함수를 제공할 수 있다.

```rust
impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle { length: size, width: size }
    }
}
```

이 연관 함수를 호출하기 위해서는 `let sq = Rectangle::sqaure(3);` 처럼 구조체 이름과 함께 `::` 문법을 이용한다. 이 함수는 구조체의 네임스페이스 내에 있다. `::` 문법은 연관 함수와 모듈에 의해 생성된 네임스페이스 두 곳 모두에서 사용되는데, 모듈은 나중에 다룬다.

## 정리

구조체는 우리에게 의미있는 커스텀 타입을 만들 수 있도록 해준다. 구조체를 이용함으로써, 우리는 연관된 데이터들을 서로 연결해서 유지할 수 있고 각 데이터에 이름을 붙여 코드를 더 명확하게 사용할 수 있다. 메소드는 구조체의 인스턴스가 가지고 있는 동작을 명시하도록 해주고, 연관 함수는 이용 가능한 인스턴스 없이 구조체에 특정 기능을 네임스페이스 내에 넣을 수 있도록 해준다.

---

# 열거형과 패턴 매칭

열거(_enumerations_)은 열겨형(_enums_)이라고도 한다. 열거형은 하나의 타입이 가질 수 있는 값들을 열거 함으로써 타입으로 정의할 수 있도록 한다. `Option` 이라는 독특한 열거형은 어떤 값을 가질 수도 있고, 갖지 않을 수도 있다. `match` 를 이용해 열거형의 값에 따라 쉽게 다른 코드를 실행하기 위한 패턴 매칭을 사용할 수 있다. 또한 `if let` 은 코드에서 열거형을 간결하게 다르기 위한 구문이다.

---

## 열거형 정의하기

열거형이 구조체보다 유용하고 적절하게 사용되는 상황이 있다. IP 주소를 다뤄야 하는 경우가 해당된다. 현재 IP 주소에는 두 개의 주요한 표준이 있다. (버전4, 버전6) 프로그램에서 다룰 IP 주소의 경우는 이 두 가지가 전부이다. 이때 모든 가능한 값들을 나열(_enumerate_) 할 수 있으며, 이 경우를 열거라고 한다.

IP 주소는 버전 4나 버전 6중 하나고, 동시에 두 버전이 될 수는 없다. IP 주소의 속성에 따라 열거형 자료 구조가 적절하다. 왜냐하면 열거형의 값은 variants 중 하나만 될 수 있기 때문이다. 버전 4나 버전 6은 근본적으로 IP 주소이기 때문에, 이 둘은 코드에서 모든 종류의 IP 주소에 적용되는 상황을 다룰 때 동일한 타입으로 취급하는 것이 좋다.

`IpAddrKind` 라는 열거형을 정의하면서 포함할 수 있는 IP 주소인 v4 와 v6 를 나열해서 이 개념을 코드에 표현할 수 있다. 이것을 열거형의 _variants_ 라고 한다.

```rust
enum IpAddrKind {
	v4,
	v6,
}
```

이제 `IpAddrKind` 은 우리의 코드 어디에서나 쓸 수 있는 데이터 타입이다.

### 열거형 값

다음처럼 `IpAddrKind` 의 두 개의 variants 에 대한 인스턴스를 만들 수 있다.

```rust
let four = IpAddrKind::V4;
let six = IpAddrKind::V6;
```

열거형의 variants 는 열거형을 정의한 식별자에 의해 네임스페이스가 생기며, 두 개의 콜론을 사용하여 둘을 구분할 수 있다. `IpAddrKind::V4` 와 `IpAddrKind::V6` 의 값은 동일한 타입이기 때문에 우용한다. 이제 `IpAddrKind` 타입을 인자로 받는 함수를 정의할 수 있다.

```rust
fn route(ip_type: IpAddrKind) {}
```

그리고 variant 중 하나를 사용해서 함수를 호출할 수 있다.

```rust
route(IpAddrKind::V4);
route(IpAddrKind::V6);
```

하지만 지금은 IP 주소의 버전은 알 수 있지만 실제 IP 주소 데이터를 저장할 방법이 없다. 이 문제는 구조체를 사용해 처리할 수 있다.

```rust
enum IpAddrKind {
    V4,
    V6,

struct IpAddr {
    kind: IpAddrKind,
    address: String,
}

let home = IpAddr {
    kind: IpAddrKind::V4,
    address: String::from("127.0.0.1"),
};

let loopback = IpAddr {
    kind: IpAddrKind::V6,
    address: String::from("::1"),
};
```

---

위 방법과 다르게 열거형 variant에 데이터를 직접 넣는 방식을 사용해서 위 방법보다 더 간결하게 사용할 수 있다. `IpAddr` 열거형의 정의는 `V4`, `V6` variant 는 관련된 `String` 타입의 값을 갖는다.

```rust
enum IpAddr {
    V4(String),
    V6(String),
}

let home = IpAddr::V4(String::from("127.0.0.1"));
let loopback = IpAddr::V6(String::from("::1"));
```

이로서 variant 는 다른 타입과 다른 양의 연관된 데이터를 가질 수 있다. 버전 4 타입의 IP 주소는 항상 0~255 사이의 숫자 4개로 된 구성요소를 갖게 될 것이다. V4 주소에 4개의 u8 값을 저장하길 원하고, V6 주소는 하나의 `String` 값으로 표현되길 원하다면, 구조체로는 구현할 수 없다.

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1"));
```

---

두 가지의 다른 종류의 IP 주소를 저장하는 것은 흔한 일이기 때문에 표준 라이브러리에 “`IpAddr`” 이라는 사용가능한 정의가 있다. 표준라이브러리는 어떻게 정의하고 있는지 보자.

```rust
struct Ipv4Addr {
    // details elided
}

struct Ipv6Addr {
    // details elided
}

enum IpAddr {
    V4(Ipv4Addr),
    V6(Ipv6Addr),
}
```

위에서 정의하고 사용했던 것과 동일한 열거형의 variant 를 갖고 있지만, variant 에 포함된 주소 데이터는 두 가지 다른 구조체로 되어 있으며 각 variant 마다 다르게 정의하고 있다.

코드를 보면 알 수 있듯이 열거형 variant에 어떤 종류의 데이터라도 넣을 수 있다. 예를 들면 문자열, 숫자 타입, 혹은 구조체. 다른 열거형 조차도 포함할 수 있다. 또한 표준 라이브러리 타입들은 어떤 경우에는 생각한 것보다 복잡하지 않다.

현재 스코프에 표준 라이브러리를 가져오지 않았기 때문에 표준 라이브러리에 `IpAddr` 정의가 있더라도 동일한 이름의 타입을 만들고 사용할 수 있다.

---

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

이 열거형에는 다른 데이터 타입을 갖는 네 개의 variants 가 있다.

-   `Quit` 은 연관된 데이터가 없다
-   `Move` 는 구조체 처럼 name fields 를 가지고 있다.
-   `Write` 는 하나의 싱글 `String` 을 포함한다.
-   `ChangeColor` 는 세 개의 i32 를 포함한다.

이처럼 variants 로 열거형을 정의하는 것은 다른 종류의 구조체들을 정의하는 것과 비슷하다. 열거형과 구조체의 다른점은 모든 variants 가 `Message` 타입으로 그룹화 한다는 것이다.

---

만약 이 모든 데이터 타입을 각각 구조체로 정의해서 사용한다면, 이 데이터 타입 중 어느 한 가지를 인자로 받는 함수를 정의하기는 힘들 것이다. 하지만 `Message` 열거형은 하나의 타입으로 이것이 가능하다.

그렇지만 열거형과 구조체의 비슷한 점도 있다. 구조체에 `impl` 을 사용해서 메소드를 정의한 것처럼, 열거형에도 정의할 수 있다.

열거형의 값을 가져오기 위해 메소드 안에서 self 를 사용할 것이다. 다음 예제에서 변수 `m` 은 `m.call()` 이 실행될 때 `call` 매소드안에서 `self` 가 될 것이다.

```rust
impl Message {
    fn call(&self) {
        // 메소드 내용은 여기 정의할 수 있습니다.
    }
}

let m = Message::Write(String::from("hello"));
m.call();
```

---

### Option 열거형과 Null 값 보다 좋은 점들

이번에는 표준 라이브러리에서 열거형으로 정의된 또 다른 타입인 `Option` 에 대한 사용 예를 볼 것이다. `Option` 타입은 값이 있거나 없을 수도 잇는 아주 흔한 상황을 나타내기 때문에 많이 사용된다. 이 개념을 타입 시스템의 관점으로 말하자면, 컴파일러가 특정 값이 있을때와 없을때, 모든 경우를 처리했는지 체크할 수 있다. 이렇게 함으로써 버그를 방지할 수 있고, 이것은 다른 프로그래밍 언어에서 매우 흔하다.

프로그래밍 언어 디자인은 어떤 특성들이 포함되어있는지의 관점에서 생각할 수도 있지만, 포함되지 않는 특성들도 역시 중요하다. Rust는 다른 언어들에서 흔하게 볼 수 있는 **`null` 특성이 없다.** `null` 은 값이 없다는 것을 표현하는 하나의 값이다. `null` 을 허용하는 언어에서, 변수는 항상 `null` 상태이거나, `null` 이 아닌 상태가 된다. `null` 값으로 발생하는 문제는, `null` 값을 `null` 이 아닌 값처럼 사용하려고 할 때 여러 종류의 오류가 발생할 수 있다는 점이다. null 이나 `null` 이 아닌 속성은 어디에나 있을 수 있고, 너무나도 쉽게 오류를 발생시킬 수 있다.

그러나 `null` 을 완전히 배척하기엔 유용하다. : `null` 은 현재 어떤 이유로 유효하지않고, 존재하지 않는 하나의 값이다.

Rust에는 `null` 이 없지만, 값의 존재 혹은 부재의 개념을 표현할 수 있는 열거형이 있다. 이 열거형은 `Option<T>` 이며, 다음과 같이 [표준 라이브러리에 정의](https://doc.rust-lang.org/std/option/enum.Option.html)되어 있다. 관련 메소드도 설명되어 있다.

```rust
enum Option<T> {
    Some(T),
    None,
}
```

`Option<T>` 열거형은 매우 유용하고, 기본적으로 포함되어 있기 때문에, 명시적으로 가져오지 않아도 사용할 수 있다. 그 variants 도 마찬가지다. `Option::` 를 앞에 붙이지 않고, `Some` 과 `None` 을 바로 사용할 수 있다. `Option<T>` 는 일반적인 열거형이고, `Some(T)` 과 `None` 도 여전히 `Option<T>` 의 variants 다.

`<T>` 는 `Option` 열거형의 `Some` variant 가 어떤 타입의 데이터라도 가질 수 있다는 것을 의미한다. 이는 Rust의 문법으로 제너릭 타입 파리미터이며 아직 다루지 않았다.

```rust
let some_number = Some(5);
let some_string = Some("a string");

let absent_number: Option<i32> = None;
```

다음 코드는 숫자 타입과 문자열 타입을 갖는 `Option` 값에 대한 예시다.

`Some` 이 아닌 `None` 을 사용한다면, `Option<T>` 이 어떤 타입을 지는지 Rust에게 알려줘야 한다. 컴파일러는 `None` 만 보고는 `Some` variant 가 어떤 타입인지 알 수 없기 때문이다.

`Some` 값을 얻게 되면, 값이 있다는 사실과 `Some` 이 갖고 있는 값에 대해 알 수 있다. `None` 값을 사용하면, `null`과 비슷한 의미를 갖게 된다. : 유효한 값을 갖고 있지 않다.

---

그렇다면 왜 `Option<T>` 이 `null` 보다 좋을까? 간단하게 말하면 `Option<T>` 와 `T`(`T`는 어떤 타입이든 될 수 있음)는 다른 타입이고, 컴파일러는 `Option<T>` 값을 명확하게 유효한 값처럼 사용할 수 없게 한다. 예를 들면 다음 코드는 컴파일 되지 않는다.

```rust
let x: i8 = 5;
let y: Option<i8> = Some(5);

let sum = x + y;
```

이는 아래와 같은 에러 메세지가 출력된다.

```
error[E0277]: the trait bound `i8: std::ops::Add<std::option::Option<i8>>` is
not satisfied
 -->
  |
7 | let sum = x + y;
  |           ^^^^^
  |
```

이 에러 메세지는 Rust가 `Option<i8>` 와 `i8` 를 어떻게 더해야 하는지 모른다는 것을 의미한다. **둘은 다른 타입이기 때문이다.** Rust에서 `i8` 과 같은 타입의 값을 가질 때, 컴파일러는 항상 유효한 값을 갖고 있다는 것으로 보장한다. 때문에 값을 사용하기 전에 `null` 인지 확인할 필요도 없이 값을 사용할 수 있다.

하지만 `Option<T>` 을 사용할 경우엔 값이 있을지 없을지에 대한 생각을 할 필요가 있으며 컴파일러는 값을 사용하기 전에 이런 케이스가 처리되었는지 확인 해 줄것이다.

따라서 다음을 계산하려면 `T`에 대한 연산을 수행하기 전에 `Option<T>` 를 `T`로 변환해야 한다. 이렇게 하면 `null` 과 관련된 가장 흔한 이슈를 발견하는데 도움을 준다. `null` 일때 `null` 이 아니라고 가정할 경우처럼 말이다.

`null` 일 수 있는 값을 사용하기 위해서, 명시적으로 값의 타입을 `Option<T>` 로 만들어 줘야 한다.그 다음에 값을 사용할 때 명시적으로 `null` 인 경우를 처리해야 한다. 이것으로 인해 값의 타입이 `Option<T>` 가 아닌 모든 값이 `null` 이 아니라고 안전하게 가정할 수 있다. 이것은 `null` 을 너무 많이 사용하는 문제를 제한하고, Rust 코드의 안정성을 높이기 위한 의도된 디자인 결정사항이다.

그럼 `Option<T>` 타입인 값을 사용하기 위해서는 각 variant를 처리할 필요가 있다. `Some(T)` 값일 경우만 실행되는 코드가 필요하고, 이 코드는 안에 있는 `T` 를 사용할 수 있다. 다른 코드에서는 `None` 값일 때 실행되는 코드가 필요하기도 하며, 이 코드에서는 사용할 수 있는 `T` 값이 없다. `match` 표현식은 제어 흐름을 위한 구분으로, 열거형과 함께 사용하면 이런 일을 해결할 수 있다.

---

## match 흐름 제어 연산자

Rust는 `match` 라고 불리는 매우 강력한 흐름 제어 연산자를 가지고 있는다. 이는 우리에게 일련의 패턴에 대해 어떤 값을 비교한 뒤 어떤 패턴에 매치되었는지를 바탕으로 코드를 수행하도록 해준다. 패턴은 리터럴 값, 변수명, 와일드카드, 그리고 많은 다른 것들로 구성될 수 있다. `match` 의 힘은 패턴의 표현성으로부터 오며 컴파일러는 모든 가능한 경우가 다루어지는지를 검사한다.

`match` 표현식을 동전 분류기와 비슷한 종류로 생각해보면 쉽다. 동전들은 다양한 크기의 구멍들이 있는 트랙을 미끄러져 내려가고, 각 동전은 그것에 맞는 첫 번째 구멍을 만났을 때 떨어진다. 동일한 방식으로, 값들은 `match` 내의 각 패턴을 통과하고, 해당 값에 “맞는" 첫 번째 패턴에서, 그 값은 실행 중에 사용될 연관된 코드 블록으로 떨어질 것이다.

다음 코드는 익명의 미국 동전을 받아서 동전 계수기와 동일한 방식으로 그 동적이 어떤 것이고, 센트로 해당 값을 변환하는 함수를 작성한 것이다.

`value_in_cents` 함수 내의 `match`를 쪼개 보자. 먼저 `match` 키워드 뒤에 표현식을 썼는데 다음코드는 `coin` 값이다. 이는 `if` 를 사용한 표현식과 매우 유사하지만, 큰 차이점이 있다. `if` 를 사용하는 경우, 해당 표현식은 `boolean` 값으로 반환할 필요가 있다. 하지만 `match` 는 어떤 타입이든 가능하다.

다음은 `match` 갈래(arm)들이다. 하나의 갈래는 패턴과 어떤 코드들로 이루어져 있다. 여기서 첫 번째 갈래의 값은 `Coin::Penny`로 되어있는 패턴을 가지고 있고, 그 후에 패턴과 실행되는 코드를 구분해주는 `=>` 연산자가 있다. 다음 경우 코드는 그냥 값 `1`이다. 각 갈래의 그다음 갈래와 쉼표로 구분한다.

`match` 표현식이 실행될 때 각 갈래의 패턴에 대해 순차적으로 비교한다. 그러다 어떤 패턴이 그 값과 매치되면, 그 패턴과 연관된 코드가 실행된다. 만일 패턴이 값과 매치되지 않는다면, 다음 갈래로 실행을 계속한다.

각 갈래와 연관된 코드는 표현식이고, 이 매칭 갈래에서의 표현식의 결과 값은 전체 `match` 표현식에 대해 반환되는 값이다.

각 갈래가 그냥 값을 리턴하는 다음 예제 처럼 매치 갈래의 코드가 짧다면 중괄호를 사용하지 않는다. 만일 매치 갈래 내에서 여러 줄의 코드를 실행시키고 싶다면, 다음과 같이 중괄호를 사용할 수 있다.

```rust
Coin::Penny => {
		println!("Lucky penny!");
		1
},
```

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
}

fn value_in_cents(coin: Coin) -> u32 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

---

### 값들을 바인딩하는 패턴들

매치 갈래의 또 다른 유용한 기능은 패턴과 매치된 값들의 부분을 바인딩할 수 있다는 것이다. 이는 열거형 variant로부터 어떤 값들을 추출할 수 있는 방법이다.

한 가지 예로 열거형 variant 중 하나를 열거형 값을 가지도록 해보자. 1999년부터 2008년까지, 미국은 각 50개 주마다 한쪽 면의 디자인이 다른 쿼터 동전을 만들었다. 다른 동전들은 모두 일정하니 오직쿼터 동전들만 이 특별한 값을 갖는다. 이 정보를 `Quarter` variant 내에 `UsState` 값을 포함하도록 `enum` 을 변경함으로써 추가할 수 있다.

```rust
enum UsState {
    Alabama
    Alaska,
    // ... etc
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}
```

```rust
fn value_in_cents(coin: Coin) -> u32 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        },
    }
}
```

만일 우리가 `value_in_cents(Coin::Quarter(UsState::Alaska))` 를 호출했다면, `coin` 은 `Coin::Quarter(UsState::Alaska)`가 될 것이다. 각각의 매치 갈래들과 이 값을 비교할때, `Coin::Quarter(state)` 에 도달할 때까지 아무것도 매치되지 않는다. 이 시점에서, `state`에 대한 바인딩은 값 `UsState::Alaska` 가 될 것이다. 그러면 이 바인딩을 `println!` 표현식 내에서 사용할 수 있고, 따라서 `Quarter`에 대한 `Coin` 열거형 variant로부터 내부의 주에 대한 값을 얻었다.

---

### Option<T>를 이용하는 매칭

`Option<T>`을 사용할 때 `Some` 케이스로부터 내부의 T 값을 얻을 필요가 있었다. `Coin` 열거형을 가지고 했떤 것처럼 `match` 를 이용해여 `Option<T>` 를 다룰 수 있다. 동전을 비교하는 대신, `Option<T>`의 variant를 비교할 것이지만 match 표현식이 동작하는 방법은 동일하다.

`Option<i32>` 를 파라미터로 받아서, 내부에 값이 있으면 그 값에 `1` 을 더하는 함수를 작성하고 싶다고 하자. 만약 내부의 값이 없으면, 이 함수는 `None` 값을 반환하고 다른 어떤 연산도 수행하는 시도를 하면 안된다.

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(i) => Some(i + 1),
    }
}
let five = Some(5);
let six = plus_one(five);
let none = plus_one(None);
```

`match` 와 열거형을 조합하는 것은 다양한 경우에 유용하다. 처음엔 까다롭지만 일단 익숙해지면 이를 모든 언어에서 쓸수 있도록 원할 것이다.

---

### 매치는 하나도 빠뜨리지 않는다

다음 `plus_one` 함수를 생각해보자.

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        Some(i) => Some(i + 1),
    }
}
```

여기서는 `None` 케이스를 다루지 않았고, 따라서 이 코드는 버그를 일으킬 것이다. 다행히도 Rust가 이를 컴파일 하고자 시도하면 다음과 같은 에러를 얻게 된다.

```
error[E0004]: non-exhaustive patterns: `None` not covered
 -->
  |
6 |         match x {
  |               ^ pattern `None` not covered
```

Rust는 우리가 다루지 않는 모든 가능한 경우를 알고 있고, 심지어 우리가 어떤 패턴을 잊어먹었는지도 알고 있다. Rust에서 매치는 하나도 빠뜨리지 않는다(exhaustive) : 코드가 유효해지기 위해서는 모든 마지막 가능성까지 샅샅이 다루어야 한다. 특히 `Option<T>`의 경우, Rust가 우리에게 `None` 케이스를 다루는 것을 까먹는것을 알려줄때 `Null` 일지도 모를 값을 가지고 있음을 가정해 실수하는 일을 방지해준다.

---

### \_ 변경자(placeholder)

Rust는 모든 가능한 값을 나열하고 싶지 않을 경우에 사용할 수 있는 패턴을 갖고 있다. 예를 들어, `u8`은 0에서부터 255까지 유효한 값을 가질 수 있따. 마일 우리가 1, 3, 5, 그리고 7 값에 대해서만 신경 쓰고자 한다면, 나머지 0, 2, 4, 6, 8 그리고 9부터 255까지를 모두 나열하고 싶지는 않을 것이다. 이럴때 \_ 패턴을 이용할 수 있다.

`_` 패턴은 어떠한 값과도 매칭된다. 갈래 제일 뒤에 이를 집어 넣음으로써, `_` 는 그전에 명시하지 않은 모든 가능한 경우에 대해 매칭될 것이다. `()`는 단지 단위 값이므로, `_` 케이스에서는 어떤 일도 일어나지 않을 것이다.

```rust
let some_u8_value = 0u8;
match some_u8_value {
    1 => println!("one"),
    3 => println!("three"),
    5 => println!("five"),
    7 => println!("seven"),
    _ => (),
}
```

하지만 `match` 표현식은 우리가 단 한가지 경우에 대해 고려하는 상황에서는 장황할 수 있따. 이러한 상황에서는 `if let` 을 사용할 수 있다.

---

## if let을 사용한 간결한 흐름 제어

`if let` 문법은 `if` 와 `let`을 조합하여 하나의 패턴만 매칭 시키고 나머지 경우는 무시하는 값을 다룬다.

다음은 `Option<u8>` 값을 매칭 하지만 그 값이 `3`일경우에만 코드를 실행시키고 싶어하는 코드다.

```rust
let some_u8_value = Some(0u8);
match some_u8_value {
    Some(3) => println!("three"),
    _ => (),
}
```

정확하게 `Some(3)`에 매칭 되는 경우에만 뭔가를 하고 다른 `Some<u8>` 값 혹은 `None` 값인 경우에는 아무것도 하지 않고 싶다. 이러한 `match` 표현식을 만족하기 위해 `_ => ()` 를 추가해야 하는데, 이는 비효율적이다.

그 대신 `if let` 을 이용한 코드를 사용할 수 있다.

```rust
let some_u8_value = Some(0u8);
if let Some(3) = some_u8_value {
    println!("three");
}
```

왼쪽 코드와 오른쪽 코드는 완벽하게 같은 동작을 한다. `if let` 은 `=` 로 구분된 패턴과 표현식을 입받는다.

---

`if let` 을 사용하는 것은 우리의 코드 길이를 확실하게 줄여준다. 하지만, `match`의 특성인 하나도 빠짐없는 검사를 잃게 되었다. `match`와 `if let` 사이에서 선택하는 것은 각각의 상황에 따라, 그리고 간결함과 전 조사 사이의 적절한 거래인지에 따라 달린 문제다. 바꿔 말하면 우리는 `if let` 을 어떤 값이 하나 패턴에 매칭 되었을 때 코드를 실행하고, 다른 값들에 대해서는 무시하는 `match` 문을 문법적 설탕(syntax sugar)으로 생각할 수 있다.

`if let` 과 함께 `else` 를 포함시킬 수 있다. `else` 뒤에 나오는 코드 블록은 `match` 표현식에서 `_` 케이스 뒤에 나오는 코드 블록과 동일하다.

만약 다음 코드에서 쿼터가 아닌 동전의 수를 구하고 싶은 동시에 쿼터 동전일 경우 또한 알려주고 싶다면 다음과 같이 코드를 작성할 수 있다.

match 문

```rust
match coin {
    Coin::Quarter(state) => println!("State quarter from {:?}!", state),
    _ => count += 1,
}
```

if let 과 else 표현식

```rust
let mut count = 0;
if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
```

---

## 정리

표준 라이브러리의 `Option<T>` 타입의 에러를 방지하기 위해 어떤 식으로 타입 시스템을 이용할 수 있는지 배웠다. 열거형 값들의 내부 데이터를 추출하고 싶을때는 `match` 와 `if let`, 둘 중 어느 것을 사용할지는 프로그래머가 다루고 싶어 하는 경우가 얼마나 많은지에 따라 달라진다. API 내에서 사용할 커스텀 타입을 생성하는 것은 타입 안정성을 보장한다. 컴파일러는 우리의 특정 함수들이 예상하는 특정 타입의 값만 갖도록 만들어줄 것이다.

---

# 모듈

러스트로 프로그램을 작성할때 코드가 `main` 함수 안에만 있을 필요는 없다. 코드가 커짐에 따라 재사용 및 더 나은 조직화를 위해 결국 어떤 기능을 다른 함수로 이동시킬 것이다. 코드를 더 작은 덩어리로 쪼갬으로서, 각각의 덩어리들은 개별적으로 이해하기 더 수월해진다. 하지만 함수가 너무 많아지는것을 대비해 러스트는 조직화된 방식으로 코드를 재사용할 수 있게 해주는 모듈(module) 시스템을 갖추고 있다.

코드 몇줄을 함수로 추출하는 것과 같은 방식으로, 함수 (혹은 구조체나 열거형 같은 다른 코드들)을 다른 모듈로 뽑아낼 수 있고, 우리는 이것들의 정의가 모듈의 바깥쪽에서 볼 수 있도록 하거나 (`public`) 혹은 보이지 않게 하도록 (`private`) 선택할 수 있다. 모듈이 어떤 식으로 동작하는지에 대한 개요다.

-   `mod` 키워드는 새로운 모듈을 선언한다. 모듈 내의 코드는 이 선언 바로 뒤에 중광호로 묶여서 따라오거나 다른 파일에 놓일 수 있다.
-   기본적으로, 함수, 타입, 상수, 그리고 모듈은 `private` 다. `pub` 키워드가 어떤 아이템을 `public` 하게 만들어줘서 이것의 네임스페이스 바깥쪽에서 볼 수 있도록 한다.
-   `use` 키워드는 모듈이나 모듈 내의 정의들을 스코프 안으로 가져와서 이들을 더 쉽게 참조할 수 있도록 한다.

---

## mod와 파일 시스템

항상 cargo를 이용해 새로운 프로젝트를 만들었지만, 바이너리 크레이드(crate)를 만드는 대신에 라이브러리 크레이트를 만들 것이다. 라이브러리 크레이트란 다른 사람들의 프로젝트에 디펜던시(dependency)로 추가할 수 있는 프로젝트다.

지금부터 몇가지 일반적인 네트워크 기능을 제공하는 라이브러리의 뼈대를 만들 것이다. 여기서는 모듈들과 함수들의 조직화에 집중하고, 함수의 본체에 어떤 코드가 들어가야 하는지는 신경쓰지 않는다. 이 라이브러리를 `commuicator` 라고 부를 것이다. 라이브러리를 만들기 위해서는 `--bin` 대신 `--lib` 옵션을 사용한다

```rust
$ cargo new communicator --lib
$ cd communicator
```

---

cargo 가 _src/main.rs_ 대신 _scr/lib.rs_ 를 생성했다.

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn it_works() {
    }
}
```

`--bin` 옵션을 사용했을때 “Hello, world!” 바이너리를 만들어준 것과는 사뭇 다르다.

`#[]` 와 `mod tests` 문법은 이후에 다루도록 한다.

_src/main.rs_ 파일이 없기 때문에, `cargo run` 커맨드로 cargo가 실행할 것이 없다. 따라, 여기서는 라이브러리 크레이트의 코드를 컴파일하기 위해 `cargo build`를 사용할 것이다.

---

### 모듈 정의

우리의 `communicator` 네트워크 라이브러리를 위해서, 먼저 `connect` 라는 이름의 함수가 정의되어 있는 `network` 라는 이름의 모듈을 정의하겠다. Rust 내 모듈 정의는 모두 `mod` 로 시작된다.

```rust
mod network {
    fn connect() {
    }
}
```

`mod` 키워드 뒤에, 모듈의 이름 `network` 가 쓰여지고 중괄호 안에 코드 블록이 온다. 이 블록 안의 모든 것은 네임스페이스 `network` 안에 있다. 이 모듈 안에 `connect` 라는 이름의 함수 하나가 있다. 이 함수를 `network` 모듈 바깥의 스크립트에서 호출하고자 한다면, 모듈을 특정할 필요가 있으므로 네임스페이스 문법 `::` 를 사용해야 한다.

`connect()` → X

`network::connect()` → 0

---

또한 _src/lib.rs_ 파일 내에 여러 개의 모듈을 나란히 정의할 수도 있다. 예를 들어 `connect` 라는 이름의 함수를 갖고 있는 `client` 모듈을 정의할 수 있다.

이제 `network::connect` 함수와 `client::connect` 함수를 갖게 되었다. 둘은 완전히 다른 기능을 갖을 수 있고, 서로 다른 모듈에 정의되어 있기 때문에 함수 이름이 서로 부딪힐 일이 없다.

지금은 라이브러리를 만드는 중이기 때문에, 처음 만들어진 파일은 _src/lib.rs_ 다. 하지만 모듈을 만드는 것에 관하여 _src/lib.rs_ 는 특별할 것이 없다. 라이브러리 크레이트의 _src/lib.rs_ 내에 모듈을 만드는 것과 똑같은 방식으로 바이너리 크레이트의 _src/main.rs_ 내에도 모듈을 만들 수 있다.

```rust
mod network {
    fn connect() {
    }
}

mod client {
    fn connect() {
    }
}
```

---

```rust
mod network {
    fn connect() {
    }

    mod client {
        fn connect() {
        }
    }
}

```

모듈 안에 다른 모듈을 집어넣는 것도 가능한데, 이는 우리의 모듈이 커짐에 따라 관련된 기능이 잘 조직화 되도록 하고 각각의 기능을 잘 나누도록 하는데 유용하다. 코드를 어떻게 조직화 할 것인가에 대한 선택은 코드의 각 부분간의 관계에 대해 어떻게 생각하고 있는지에 따라 달라진다. 예를 들면 왼쪽 코드처럼 `client` 모듈과 `connect` 함수가 `network` 네임스페이스 내에 있다면 라이브러리 사용자가 더 쉽게 이해할 수 있다.

이제 `network::connect` 와 `network::client::connect` 함수를 갖게 되었다. 다시 말하면 `connect` 라는 이름의 두 함수는 서로 다른 네임스페이스에 있으므로 부딪힐 일이 없다.

---

```rust
communicator
 ├── network
 └── client
```

[이 예제](Rust%206c87bc32962b444cb4dc5be72e8029d7.md) 에서의 조직화 계층 구조.

```rust
communicator
 └── network
     └── client
```

바로 위 예제 에서의 조직화 계층 구조.

더 복잡한 프로젝트는 많은 수의 모듈을 갖고 있을 수 있고, 이들은 지속적인 트래킹을 위해 논리적으로 잘 조직화 해야한다. 프로젝트 내에서 “논리적으로"가 의미하는 것은 우리에게 달려 있는 것이며, 우리와 우리의 라이브러리 사용자들이 프로젝트 도메인에 대해 어떻게 생각하는지에 따라 달라진다.

---

### 모듈을 다른 파일로 옮기기

모듈은 계층적인 구조를 형성하는데, 이는 파일 시스템과 매우 닮아있다. Rust 에서는 프로젝트를 잘게 나누기 위해 여러 개의 파일 상에서 모듈 시스템을 사용할 수 있어, 모든 것들이 _src/lib.rs_ 나 _src/main.rs_ 안에 존재할 필요는 없다.

```rust
mod client {
    fn connect() {
    }
}

mod network {
    fn connect() {
    }

    mod server {
        fn connect() {
        }
    }
}
```

세 개의 모듈 `client`, `network`, `network::server` 가 모두 _src/lib.rs_ 에 정의되어 있다.

이들은 다음과 같은 모듈 계층을 가지고 있다.

```rust
communicator
 ├── client
 └── network
     └── server
```

만약 모듈속의 여러개의 함수가 있고, 그 함수들이 길어진다면, 작업하고자 하는 코드를 찾으려고 파일을 스크롤 하기가 까다로울 것이다. 함수들은 하나 혹은 그 이상의 `mod` 블록 안에 있기 때문에 함수 내의 코드 라인 또한 길어질 것이다. 따라서 `client`, `network`, `server` 모듈을 *src/lib.rs*로부터 떼어내어 각자를 위한 파일들에 위치시키는 것이 효율적이다.

---

먼저 `client` 모듈의 코드를 `client` 모듈의 선언 부분만 남겨둔다. 그러니까 코드는 오른쪽처럼 바뀔 것이다.

여전히 `client` 모듈을 선언하고 있지만, 코드 블록 대신 세미콜론을 사용함으로써 Rust에게 `client` 모듈의 스코프 내에 정의된 코드를 다른 위치에서 찾으라고 말한다. 다시 말하면 `mod client;` 라는 코드의 뜻은 다음과 같다.

```rust
mod client {
    // contents of client.rs
}
```

이제 모듈과 같은 이름의 외부 파일을 만들어야 한다. `client.rs` 파일을 _src/_ 디렉토리에 생성한다. 그런뒤 앞에서 제거했던 `client` 모듈 내의 `connect` 함수를 입력한다.

```rust
fn connect() {
}
```

이미 _src/lib.rs_ 안에 `client` 모듈을 `mod` 를 이용하여 선언했기 때문에, 이 파일 안에는 `mod` 선언이 필요 없다. 이 파일은 단지 `client` 모듈의 내용물만 제공한다. 만일 `mod client` 를 여기에 적는다면, 이는 `client` 모듈 내에 서브모듈 `client` 를 만들게 된다.

```rust
mod client;

mod network {
    fn connect() {
    }

    mod server {
        fn connect() {
        }
    }
}
```

---

Rust는 기본적으로 _src/lib.rs_ 만 찾아본다. 만약에 더 많은 파일을 프로젝트에 추가하고 싶다면, _src/lib.rs_ 내에서 다른 파일을 찾아보라고 Rust에게 알려줘야 한다.

이것이 왜 `mod client` 라는 코드가 _src/lib.rs_ 내에 정의되야 하고, 그리고 _src/client.rs_ 내에는 정의될 수 없는지에 대한 이유다.

---

같은 방법으로 `network` 모듈 또한 새로운 _src/network.rs_ 파일로 나눌 수 있다.

```rust
mod client;

mod network;
```

위 코드와 오른쪽 코드를 이상없이 작성하고 `cargo bulid`를 한다면 정상적으로 실행된다. 하지만 우리는 추출할 수 있는 또하나의 `serevr` 모듈이 있다. 이것또한 같은 방법으로 추출하면

```rust
fn connect() {
}

mod server {
    fn connect() {
    }
}
```

---

```rust
fn connect() {
}

mod server;
```

_src/server.rs_ 파일을 만들고 추출해둔 `server` 모듈의 내용물을 입력한다.

```rust
fn connect() {
}
```

`cargo build` 를 실행하면 다음과 같은 에러를 얻게 된다.

```rust
$ cargo build
   Compiling communicator v0.1.0 (file:///projects/communicator)
error: cannot declare a new module at this location
 --> src/network.rs:4:5
  |
4 | mod server;
  |     ^^^^^^
  |
note: maybe move this module `network` to its own directory via `network/mod.rs`
or maybe `use` the module `server` instead of possibly redeclaring it
```

---

이 에러는 이 위체에 새로운 모듈을 선언할 수 없다 고 말해주며 *src/network.rs*의 `mod server;` 라인을 지적한다. 에러 설명을 보면

```rust
note: maybe move this module `network` to its own directory via `network/mod.rs`
```

이전에 우리가 사용했던 똑같은 파일 이름으로 추출하는 패턴 대신 에러 메세지에서 제안하는 것을 해볼 수 있다.

1. 부모 모듈의 이름에 해당하는. `network` 라는 이름의 새로운 디렉토리를 만든다.
2. _src/network.rs_ 파일을 이 새로운 _network_ 디렉토리 안으로 옮기고, 파일 이름을 _src/network/mod.rs_ 로 고친다.
3. 서브모듈 파일 *src/server.rs*를 _network_ 디렉토리 안으로 옮긴다.

이후 `cargo build` 를 다시 실행하면 이상없이 컴파일 된다.

모듈 레이아웃은 여전히 아래와 같다. 이는 [이 코드](Rust%206c87bc32962b444cb4dc5be72e8029d7.md)에서 만든 것과 정확하게 동일하다.

```rust
communicator
 ├── client
 └── network
     └── server
```

이에 대응하는 파일 레이아웃은 다음과 같다.

```rust
├── src
│   ├── client.rs
│   ├── lib.rs
│   └── network
│       ├── mod.rs
│       └── server.rs
```

---

[위 과정](Rust%206c87bc32962b444cb4dc5be72e8029d7.md)에서 에러가 생기는 이유는 _src_ 디렉토리 안에 _server.rs_ 파일이 있으면, Rust는 `server` 가 `network` 의 서브모듈이라고 인식할 수 없기 때문이다.

예를 들자면 다음과 같은 구조를 보자.

```rust
communicator
 ├── client
 └── network
     └── client
```

이 예제에서는 `client`, `network`, `network::client` 라는 세 개의 모듈이 있다. 모듈을 파일로 추출하기 위해서 앞서 배웠던 단계를 취하면 `client` 모듈을 위한 _src/client.rs_ 를 만들게 될 것이다. `network` 모듈을 위해서는 _src/network.rs_ 파일을 만들 것이다. 하지만 `network::client` 모듈을 *src/client.rs*로 추출하는 것은 불가능하다. 그 이유는 최상위 층에 `client` 모듈이 이미 있기 때문이. 만일 `client` 와 `network::client` 모듈 둘다 *src/client.rs*파일에 집어 넣는다면 Rust는 이 코드가 `client` 를 위한 것인지, 아니면 `network::client` 를 위한 것인지 알아낼 방법이 없다.

따라서 `network` 모듈의 `network::client` 서브 모듈을 위한 파일을 추출하기 위해서는 _src/network.rs_ 파일 대신 `network` 모듈을 위한 디렉토리를 만들 필요가 있다. 그후 `network` 모듈의 내용을 _src/network/mod.rs_ 파일로 옮기고, 서브모듈 `network::client`는 _src/network/client.rs_ 파일을 갖게 할 수 있다. 이렇게 하면 _src/client.rs_ 는 명확하게 `client` 모듈이 소유한 코드고, _src/network/client.rs_ 는 `network::client`가 소유한 코드다.

---

### 모듈 파일 시스템의 규칙

파일에 관한 모듈의 규칙을 정리해보자.

-   만일 `foo` 라는 이름의 모듈이 서브모듈을 가지고 있지 않다면, _foo.rs_ 라는 이름의 파일 내에 `foo` 에 대한 선언을 집어넣어야 한다.
-   만일 `foo`가 서브모듈을 가지고 있다면, _foo/mod.rs_ 라는 이름의 파일에 `foo` 에 대한 선언을 집어넣어야 한다.

이 규칙들은 모두 제귀적으로 적용된다. 만약 `foo` 라는 이름의 모듈이 `bar` 라는 이름의 서브 모듈을 갖고 있고 `bar` 는 서브 모듈이 없다면, _src_ 디렉토리 안에는 아래와 같은 파일들이 있어야 한다.

```rust
├── foo
│   ├── bar.rs (contains the declarations in `foo::bar`)
│   └── mod.rs (contains the declarations in `foo`, including `mod bar`)
```

이 모듈들은 부모 모듈의 파일에 `mod` 키워드를 사용하여 선언되어 있어야 한다.

---

## pub으로 가시성(visibility) 제어하기

앞에서 `network` 와 `network::server` 코드를 각각 _src/network/mod.rs_ 와 _src/network/server.rs_ 파일 안으로 이동시켰다. 그리고 `cargo build` 로 프로젝트를 빌드할 수 있긴 했지만, 사용하지 않고 있는 `client::connect`, `network::connect`, `network::server::connect` 함수에 대한 경고 메세지를 보게 된다.

```
warning: function is never used: `connect`, #[warn(dead_code)] on by default
src/client.rs:1:1
  |
1 | fn connect() {
  | ^

warning: function is never used: `connect`, #[warn(dead_code)] on by default
 --> src/network/mod.rs:1:1
  |
1 | fn connect() {
  | ^

warning: function is never used: `connect`, #[warn(dead_code)] on by default
 --> src/network/server.rs:1:1
  |
1 | fn connect() {
  | ^
```

이러한 경고들이 나오는 이유는 각각의 함수들을 선언만 하고 사용하고 있지 않기 때문이다. 하지만 지금 당장은 우리가 프로젝트 내에서 사용해야만 하는 것이 아닌, 누군가가 사용할 수 있도록 만들어진 함수들의 라이브러리를 만드는 중이므로, 이런 `connect` 함수 등이 사용되지 않는 것은 큰 문제가 아니다. 이 함수들을 만든 의도는 함수들이 지금 이 프로젝트가 아닌 또다른 프로젝트에 사용될 것이란 점이다.

그렇다면 `connect` 라이브러리를 다른 프로젝트에서 사용해보자. 다음 코드를 담은 _src/main.rs_ 파일을 만들어서 같은 디렉토리에 라이브러리 크레이트와 나란히 바이너리 크레이트를 만든다.

```rust
extern crate communicator;

fn main() {
    communicator::client::connect();
}
```

`communicator` 라이브러리 크레이트를 가져오기 위해 `extern crate` 명령어를 사용한다. 이제 패키지는 두 개의 크레이트를 담고 있다. cargo는 _src/main.rs_ 를 바이너리 크레이트의 루트 파일로 취급하는데, 이 바이너리 크레이트는 _src/lib.rs_ 가 루트 파일인 라이브러리 크레이트와는 별개다. 이러한 경우는 꽤 흔한 일이다. 대부분의 기능은 라이브러리 크레이트 안에 구현되어 있고, 바이너리 크레이트는 이 라이브러리 크레이트를 이용한다. 결과적으로, 다른 프로그램 또한 그 라이브러리 크레이트를 이용할 수 있다.

`communicator` 라이브러리 밖의 크레이트가 안을 참조하는 시점에서,여태 만들어왔던 모든 모듈들은 `communicator` 라는 이름을 갖는 모듈 내에 있다. 크레이트의 최상위 모듈을 루트 모듈 (_root modul_) 이라 부른다.

만약 프로젝트의 서브모듈 내에서 외부 크레이트를 이용하고 있을지라도, `extern carte` 이 루트 모듈에 와 있어야 한다. (_src/main.rs_ 혹은 _src/lib.rs_) 그러면 서브모듈 안에서 마치 최상위 모듈의 아이템을 참조하듯 외부 크레이트로부터 아이템들을 참조할 수 있다.

현 시점에서 바이너리 크레이트는 `client` 모듈로부터 `connect` 함수를 호출한다. 하지만 `cargo build` 를 실행하면 경고들 이후에 에러를 표시한다.

```
error: module `client` is private
 --> src/main.rs:4:5
  |
4 |     communicator::client::connect();
  |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

이 에러는 `client` 모듈이 비공개(_private_) 임을 알려주고 있다. Rust의 모든 코드의 기본 상태는 비공개(_private_)다. 즉, 다른 사람은 이 코드를 사용할 수 없다. 만약 프로그램 내에서 _private_ 함수를 이용하지 않는다면, 그 프로그램이 그 함수를 이용할 수 있는 유일한 곳이기 때문에, Rust는 그 함수가 사용된 적이 없다며 경고한다.

`client::connect` 함수를 _public_ 으로 지정한 뒤에는 바이너리 크레이트 상에서 이 함수를 호출하는 것이 가능해지고, 그 함수가 사용된 적이 없다는 경고 또한 사라질 것이다. 함수를 _public_ 으로 표시하는 것은 Rust에게 그 함수가 우리의 프로그램 외부의 코드에 코드에 의해 사용될 것이라고 알려준다. Rust는 이제부터 이론적으로 외부의 사용이 가능해진 이 함수를 “사용되었다" 라고 간주한다. 따라서 어떤 것이 _public_ 으로 표시한다면, Rust는 그것이 우리의 프로그램 내에서 미사용한 것에 대한 경고를 멈춘다.

---

### 함수를 공개로 만들기

Rust에게 어떤 아이템을 *public*으로 설정하기 위해서, 해당 아이템의 선언 시작 부분에 `pub` 키워드를 추가한다. 이제 `client::connect` 의 미사용 경고와 바이너리 크레이트에서 발생한 `modul ‘client’ is private` 에러를 제거해보자.

```rust
pub mod client;

mod network;
```

```
error: function `connect` is private
 --> src/main.rs:4:5
  |
4 |     communicator::client::connect();
  |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

제거하고자 하는 에러들은 제거되고 새로운 에러가 나왔다. 새로운 에러는 `functions ‘connect’ is private` 이고, *src/client.rs*를 수정해서 `client::connect`도 *private*로 만들어 보자.

---

```rust
pub fn connect() {
}
```

이후 cargo build 를 하면,

```
warning: function is never used: `connect`, #[warn(dead_code)] on by default
 --> src/network/mod.rs:1:1
  |
1 | fn connect() {
  | ^

warning: function is never used: `connect`, #[warn(dead_code)] on by default
 --> src/network/server.rs:1:1
  |
1 | fn connect() {
  | ^
```

우선, 코드가 컴파일되었고 제거하고자 했던 `client::connect` 의 미사용에 대한 경고가 사라졌다.

미사용 코드 경고가 있다고 해서 해당 아이템에 대해서 항상 *private*로 만들어야 하는 것은 아니다. 만약 해당 함수를 공개 API의 일부분으로 사용하고 싶지 않다면, 미사용 코드 경고는 해당 코드가 더 이상 필요 없고 지워도 안전하다는 것을 알려준다. 또한 이 경고는 라이브러리 내에서 해당 함수가 호출된 곳을 실수로 지웠을때 발생할 수 있는 버그를 알려줄 수도 있따.

하지만 지금은, 다른 함수들이 크레이트의 공개 API의 일부분이 되길 원하고 있고, 이들에게 `pub` 을 표시해서 남은 경고를 제거할 수 있다. _src/network/mod.rs_ 를 다음처럼 수정해보자.

```rust
pub fn connect() {
}

mod server;
```

그리고 컴파일 하면, 여전히 에러가 발생한다. `network::connect` 를 `pub` 으로 설정해 줌으로써 함수가 모듈 내에서는 *public*이지만, 이 함수를 포함하는 `network` 모듈이 _private_ 이기 때문이다. 따라서 _src/lib.rs_ 를 수정해서 `network` 를 _public_ 으로만들어야 한다.

```
warning: function is never used: `connect`, #[warn(dead_code)] on by default
 --> src/network/mod.rs:1:1
  |
1 | pub fn connect() {
  | ^

warning: function is never used: `connect`, #[warn(dead_code)] on by default
 --> src/network/server.rs:1:1
  |
1 | fn connect() {
  | ^
```

---

```rust
pub mod client;

pub mod network;
```

이제 컴파일 하면 딱 하나의 경고가 남았다. 이제까지 배운 내용으로 해결할 수 있다.

```
warning: function is never used: `connect`, #[warn(dead_code)] on by default
 --> src/network/server.rs:1:1
  |
1 | fn connect() {
  | ^
```

---

### 비공개 규칙(Privacy Rules)

정리하면, 아이템 가시성에 관한 규칙은 다음과 같다.

1. 만일 어떤 아이템이 _public_ 라면, 이는 부모 모듈 어디에서든 접근 가능하다.
2. 만일 어떤 아이템이 _private_ 라면, 같은 파일 내에 있는 **부모 모듈** 및 **이 부모의 자식** 모듈에서만 접근이 가능하다.

---

### 이름 가져오기 (Importing Names)

우리는 다음과 같이 `nested_modules` 함수를 호출하는 것처럼 모듈 이름을 호출 구문의 일부분으로 사용하여 해당 모듈 내에 정의된 함수를 호출하는 방법을 다뤘다.

```rust
pub mod a {
    pub mod series {
        pub mod of {
            pub fn nested_modules() {}
        }
    }
}

fn main() {
    a::series::of::nested_modules();
}
```

코드를 보면 알 수 있듯이 완전하게 경로를 지정한 이름을 참조하는것의 코드가 너무 길어진다. 다행히도 Rust는 이러한 호출을 더 간결하게 만들어주는 키워드를 가지고 있다.

### Use 를 이용한 간결하게 가져오기

Rust의 `use` 키워드는 스코프 내에서 호출하고 싶어하는 함수의 모듈을 가져옴으로써 긴 함수 호출을 줄여준다. `a::series::of` 모듈을 바이너리 크레이트의 루트 스코프로 가져온 예제다.

```rust
pub mod a {
    pub mod series {
        pub mod of {
            pub fn nested_modules() {}
        }
    }
}

use a::series::of;

fn main() {
    of::nested_modules();
}
```

`use` 구문안에 모듈이 아닌 함수를 적어 스코프 내로 함수를 가져올 수 있다.

```rust
pub mod a {
    pub mod series {
        pub mod of {
            pub fn nested_modules() {}
        }
    }
}

use a::series::of::nested_modules;

fn main() {
    nested_modules();
}
```

`use a::series::of;` 코드는 `of` 모듈을 참조하고 싶은 곳마다 `a::series::of` 전부를 사용하기 보다는 `of` 를 사용할 수 있도록 해준다. `use` 키워드는 정확하게 명시한 것만 스코프 내로 가져온다. 즉 모듈의 자식들까지 스코프 내로 가져오지는 않는다. 그렇기 때문에 `nested_modules` 함수를 호출하고자 할 때 `of::nested_modules` 를 사용해야 한다. 함수까지 같이 `use` 키워드로 가져온다면 함수 이름만으로도 사용할 수 있다.

---

열거형 또한 모듈과 비슷한 일종의 네임스페이스를 형성하고 있기 때문에, 열거형의 variant 또한 `use` 를 이용해서 가져올 수 있다. 어떠한 `use` 구문이건 하나의 네임스페이스로부터 여러 개의 아이템을 가져오려 한다면, 다음 처럼 중괄호와 쉼표를 구문의 마지막 위치에 사용해 아이템들을 나열 할 수 있다.

```rust
enum TrafficLight {
    Red,
    Yellow,
    Green,
}

use TrafficLight::{Red, Yellow};

fn main() {
    let red = Red;
    let yellow = Yellow;
    let green = TrafficLight::Green;
}
```

`Grren` variant에 대해서는 여전히 `TrafficLight` 네임스페이스를 적어줬다. 이는 `use` 구문 내에 `Green`을 포함하지 않았기 때문이다.

---

### \* 를 이용한 모두(glob) 가져오기

네임스페이스 내의 모든 아이템을 가져오기 위해서 `*` 문법을 사용할 수 있다.

`*` 는 _글롭(glob)_ 이라고 부르며, 이는 네임스페이스 내에 공개된(_public_) 모든 아이템을 가져온다. 하지만 글롭을 사용할 때는 신중해야 한다.글롭은 매우 편리하지만, 예상한 것보다 더 많은 아이템을 끌어와서 이름간의 충돌의 원인이 될 수도 있다.

```rust
use TrafficLight::*;

fn main() {
    let red = Red;
    let yellow = Yellow;
    let green = Green;
}
```

---

### super 를 사용해서 부모 모듈에 접근하기

`cargo new [이름] --lib` 명령어로 라이브러리 크레이트를 만들때, cargo는 기본적으로 `tests` 모듈을 만들어준다. `communicator` 프로젝트 내에 다음과 같이 있다고 해보자.

```rust
pub mod client;

pub mod network;

#[cfg(test)]
mod tests {
    #[test]
    fn it_works() {
    }
}
```

`tests` 라는 이름의 모듈이 모듈들의 뒤에 있고, `it_works` 라는 이름의 함수를 가지고 있다. 조금 특별한 주석(annotation)이 있지만, `tests` 모듈은 그냥 또다른 모듈일 뿐이다. 따라서 모듈 계층 구조은 다음과 같다.

```rust
communicator
 ├── client
 ├── network
 |   └── client
 └── tests
```

---

`tests` 모듈안 `it_works` 함수 안에서 `client::connect` 함수를 호출해보자.

```rust
fn main() {
#[cfg(test)]
mod tests {
    #[test]
    fn it_works() {
        client::connect();
    }
}
```

`cargo test` 명령으로 테스트하면 컴파일에 실패한다.

```rust
$ cargo test
   Compiling communicator v0.1.0 (file:///projects/communicator)
error[E0433]: failed to resolve. Use of undeclared type or module `client`
 --> src/lib.rs:9:9
  |
9 |         client::connect();
  |         ^^^^^^^^^^^^^^^ Use of undeclared type or module `client`
```

---

이 코드는 분명히 `communicator` 라이브러리 크레이트 안에 있기 때문에 `communicator::`를 붙일 필요가 없음에도 불구하고 컴파일에 실패한다. 컴파일에 실패한 이유는 경로가 항상 현재 모듈을 기준으로 상대적이기 때문이다. 따라서 `client` 모듈은 `communicator` 크레이트 안에 있는 것이지, `tests` 모듈 내에는 없기 때문이다. 물론 `use` 구문은 기본적으로 크레이트 루트에 대한 상대적인 경로로 인식한다. 아무튼 지금 당장은 `tests` 모듈은 이 스코프 내에서 `client` 모듈이 필요하다. 그렇다면 어떤 방법으로 한 모듈 위로 거슬러 올라가 `tests` 모듈 안에서 `client::connect` 함수를 호출 할 수 있을까? 다음과 같이 앞에 콜론 두 개를 사용하여 Rust에게 루트부터 시작하여 전체 경로를 나열하겠다고 알려주는 방법이 있다. 혹은 `super` 를 사용하여 계층 구조 상에서 현재 모듈로부터 한 모듈 거슬러 올라갈 수도 있다.

```rust
::client::connect();
```

```rust
super::client::connect();
```

---

이 두가지 옵션은 이번 예제에서는 차이가 없는 것처럼 보이지만, 만약 모듈 계층 구조가 깊어진다면, 매번 루트에서 경로를 시작하는 것은 코드를 길게 만들 것이다. 그런 경우에는 `super` 를 사용해서 현재 모듈에서 형제 모듈을 가져오는 것이 좋은 방법일 것이다. 더해서, 만약 여러 군데에 루트로부터 시작되는 경로를 명시한 뒤에 서브트리를 다른 곳으로 옮기는 식으로 여러분의 모듈을 재정리 한다면, 여러 군데의 경로를 갱신해야하는 참사가 발생할 수 있다.

`tests` 모듈안에 각각 `super::`를 타이핑해야 하는 것은 짜증날 수도 있지만, `use` 와 조합해서 한번만 사용할 수 있다. 분명 앞서 `use` 모듈은 루트부터 시작해 전체경로를 나열해야 한다고 했지만, `supper::` 과 함께 사용한다면 `use` 에 제공한 경로를 변경시켜서 이제 부모 모듈에 상대적인 경로가 되게 해준다.

이러한 이유들로 `tests` 모듈 내에서는 보통 `super::something` 이 가장 좋은 해결책이다.

```rust
#[cfg(test)]
mod tests {
    use super::client;

    #[test]
    fn it_works() {
        client::connect();
    }
}
```

`cargo test` 를 다시 실행시키면, 테스트가 통과되고 테스트 결과 출력의 첫번째 부분이 다음과 같이 나타난다.

```
$ cargo test
   Compiling communicator v0.1.0 (file:///projects/communicator)
     Running target/debug/communicator-92007ddb5330fa5a

running 1 test
test tests::it_works ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured
```

---

## 정리

코드를 조직화하기 위한 몇가지 새로운 기술을 알게 되었다. 관련된 기능들을 함께 묶여주는 이 기술들을 사용해 파일이 너무 길어지지 않게 하고, 해당 라이브러리 사용자들에게 깔끔한 공개 API를 제공할 수 있다.

---

# 일반적인 컬렉션

Rust의 표준라이브러리에는 *Collection*이라 불리는 여러개의 유용한 데이터 구조들이 존재한다. 대부분의 데이터 타입들은 하나의 특정한 값을 나타내지만, *Collection*은 다수의 값을 담을 수 있다. 내장된 배열 (build-in array)와 튜플 타입과는 달리, 이 *Collection*들이 가리키고 있는 데이터들은 힙에 저장된다. 이는 곧 데이터량이 컴파일 타임에 결정되지 않아도 되며 프로그램이 실행될 때 늘어나거나 줄어들 수 있다는 의미다. 각각의 *Collection*은 서로 다른 용량과 비용을 가지고 있고, 현재 상황에 따라 적절한 *Collection*을 선택하는 능력은 필수다. Rust에서 자주 사용되는 세 가지 컬렉션에 대해 다루겠다.

-   *벡터(Vector)*는 여러 개의 값을 서로 붙어 있게 저장할 수 있도록 해준다.
-   *스트링(String)*은 문자(charactor)의 모음이다. `String` 타입은 이전에 다루었지만 이번에 더 깊이 다룬다.
-   *해쉬맵(hash map)*은 어떤 값을 특정한 키와 연관지어 준다. 이는 *맵(map)*이라 불리는 데이터 구조의 특정한 구현 형태다.

---

## 벡터

벡터, `Vec<T>` 를 사용하면 모든 값을 메모리에 나란히 배치하는 단일 데이터 구조에 하나 이상의 값을 저장할 수 있다. 벡터는 같은 타입의 값만을 저장한다.

### 새 벡터 만들기

비어있는 새 백터를 만들기 위해서는, 아래와 같이 `Vec::new` 함수를 호출해 만들 수 있다.

```rust
let v: Vec<i32> = Vec::new();
```

위 코드에서 벡터의 타입을 적었다. 벡터에 어떠한 값도 넣지 않았기 때문에, Rust는 어떤 요소를 저장할지 모른다. 이것이 중요한 점이다. 벡터는 제네릭(_generic_)을 이용해 구현되어있다. 제네릭을 이용하여 사용자만의 타입을 만드는 방법은 이후에 다룬다. 현 시점에서는 `Vec`은 어떠한 종류의 값이라도 저장할 수 있고, 특정한 벡터는 특정한 타입의 값을 저장할 때 꺽쇠 괄호(`<>`)안에 적어야 한다.

일단 값을 집어넣으면 Rust는 프로그래머가 지정하고자 하는 값의 타입을 대부분 유추할 수 있으므로, 현실적인 코드에서는 이러한 타입을 명시할 필요가 거의 없다. 초기값들을 갖고 있는 `Vec<T>`를 생성하는 것이 더 일반적이고, Rust는 편의를 위해 `vec!` 매크로를 제공한다. 이 매크로는 사용자가 지정한 값을 저장하고 있는 새로운 벡터를 만든다. 다음은 `1`, `2`, `3`을 저장하고 있는 새로운 `Vec<i32>`를 생성할 것이다.

```rust
let v = vec![1, 2, 3];
```

제공되는 값들의 타입이 `i32` 이기 때문에, Rust는 `v`가 `Vec<i32>` 타입이라는 것을 유추할 수 있다. 따라서 코드에서 타입에 대해 언급할 필요가 없다.

---

## 벡터 갱신하기

벡터를 만들고 요소들을 추가하기 위해서는 `push` 매소드를 사용할 수 있다.

앞서 변수에 대해 다룰때 배웠듯이 어떤 변수에 대해 그 변수가 담고 있는 값이 변경될 수 있도록 하려면, `mut` 키워드를 사용하여 해당 변수를 가변으로 만들어 주어야 한다. 우리가 집어넣을 숫자는 모두 `i32` 타입이고, Rust는 데이터로부터 이 타입을 추론할 수 있으므로, `Vec<i32>` 라고 적을 필요가 없다.

```rust
let mut v = Vec::new();

v.push(5);
v.push(6);
v.push(7);
v.push(8);
```

---

### 벡터 드롭 = 벡터의 요소 드롭

struct 와 마찬가지로, 벡터도 스코프 밖으로 벗어나면 해제된다.

벡터가 드롭될 때 벡터의 내용물 또한 전부 드롭되는데, 이는 벡터가 가지고 있는 정수들이 모두 제거된다는 것이다. 이러한 점이 벡터의 요소들에 대한 reference를 만들때는 좀 더 복잡해 질 수 있다.

```rust
{
    let v = vec![1, 2, 3, 4];
    // v를 가지고 뭔가 한다.
} // <- v가 스코프 밖으로 벗어났고, 여기서 해제된다
```

---

### 벡터 요소 읽기

벡터 내의 저장된 값을 참조하는 두 가지 방법이 있다. 인덱스 문법을 사용하거나, `get` 메소드를 사용해 벡터의 값에 접근할 수 있다.

두 방법 모두 인덱스 `2`를 사용하며 앞에서부터 세번째 값에 대한 참조를 하고 있다. 인덱스는 0에서부터 시작이기 때문이다. 첫번째 방법은 `&`와 `[]` 를 사용해 *reference*를 얻었고, 두번째는 `get` 함수에 인덱스를 파라미터로 넘겨서 `Option<&T>` 를 얻었다.

```rust
let v = vec![1, 2, 3, 4, 5];

let third: &i32 = &v[2];
let third: Option<&i32> = v.get(2);
```

---

이렇게 두 가지 옵션을 제공하는 이유는, 사용자가 벡터가 가지고 있지 않은 인덱스값을 사용하고자 했을 때 프로그램이 어떻게 동작할 것 인가를 선택할 수 있기 때문이다. 다음 예제는 5개의 요소를 가지고 있는 벡터가 있고, 100 인덱스에 있는 요소에 접근하려고 시도한 경우다.

이 프로그램을 실행하면, 첫번째의 `[]` 메소드는 존재하지 않는 요소를 참조하기 때문에 `panic!` 을 일으킨다. 이 방법은 프로그램이 벡터의 끝을 넘어서는 요소에 접근하려 할때 프로그램이 에러를 발생하도록 할 때 사용할 수 있다.

`get` 함수에서는 벡터 범위를 벗어난 인덱스가 주어졌을때 패닉 없이 `None` 이 반환된다. 백터의 범위 밖에 있는 요소에 접근할 필요가 있다면 이 방법을 사용할 수 있다. 사용자의 코드는 `Some(&element)` 혹은 `None` 에 대해 다루는 로직을 갖춰야 한다. 만약 너무 많은 입력을 받아서 `None` 값을 받았을 경우, 사용자에게 현재 몇개의 아이템이 있으며, 유효한 값을 입력할 기회를 줄 수도 있다. 이런 편이 프로그램이 `panic!` 을 일으키는것보단 사용자 친화적일 것이다.

```rust
let v = vec![1, 2, 3, 4, 5];

let does_not_exist = &v[100];
let does_not_exist = v.get(100);
```

---

### 유효하지 않은 참조자

프로그램이 유효한 reference를 얻을 때, 빌림 검사기(borrow checker)가 소유권 및 빌림 규칙에 의해 이 reference와 벡터의 내용물로부터 얻은 다른 reference들이 계속 유효하게 남아있도록 확실히 보장해준다. 같은 스코프 내에서 가변 reference와 불변 reference를 같이 가질 수 없다는 규칙을 기억해보자.

```rust
let mut v = vec![1, 2, 3, 4, 5];
let first = &v[0];

v.push(6);
```

이 코드는 벡터의 첫번째 요소에 대한 불변 reference를 얻은 뒤 벡터의 끝에 요소를 추가하고자 했다. 하지만 오른쪽과 같은 에러가 발생한다. 첫번째 요소에 대한 reference가 벡터 끝에 요소를 추가하는 것에 대한 걱정은 할 필요가 없는데 말이다. 이 에러는 벡터가 동작하는 방법때문에 발생한다. 새로운 요소를 벡터의 끝에 추가 할 때 벡터가 새로운 요소를 추가하고 저장할 공간이 충분하지 않을때 발생한다. 이런 경우 새로운 메모리를 할당하여 예전 요소를 새 공간에 복사를 한다. 이 동작을 할 때 첫번째 요소에 대한 reference가 할당이 해제된 메모리를 가리키게 될 것이다. 빌림 규칙은 프로그램이 이러한 상황에 빠지지 않도록 해준다.

```
error[E0502]: cannot borrow `v` as mutable because it is also borrowed as
immutable
  |
4 | let first = &v[0];
  |              - immutable borrow occurs here
5 |
6 | v.push(6);
  | ^ mutable borrow occurs here
7 | }
  | - immutable borrow ends here
```

---

### 벡터 내의 값들에 대한 반복처리

만일 벡터 내의 각 요소들을 차례대로 접근하고 싶다면, 하나의 값에 접근하기 위해 인덱스를 사용하는 것보다, 모든 요소들에 대해 반복처리를 할 수 있다. `for` 루프를 사용해 i32 의 벡터 내에 있는 각 요소에 대한 불변 reference를 얻어서 이를 출력하는 방법이다.

```rust
let v = vec![100, 32, 57];
for i in &v {
    println!("{}", i);
}
```

만약 요소들을 변형시키길 원한다면 가변 벡터 내의 각 요소에 대한 가변 reference로 반복 작업을 할 수 있다.

```rust
let mut v = vec![100, 32, 57];
for i in &mut v {
    *i += 50;
}
```

이 때 가변 reference로 값을 바꾸기 위해서 `i` 에 역참조 연산자(`*`)를 사용해 값을 얻어야 한다.

---

### 열거형을 사용하여 여러 타입을 저장하기

이 장의 첫머리에서 벡터는 같은 유형의 값만 저장할 수 있다고 했다. 이것은 불편할 수 있다. 다른 종류의 아이템 목록을 저장해야 하는 사용 사례가 분명히 있기 때문이다. 다행히 열거형의 variant는 동일한 열거형으로 정의되므로 벡터에 다른 유형의 요소를 저장해야 할 때 열거형을 정의하여 사용할 수 있다.

예를 들어 스프레드시트 행에서 값들을 가져온다고 해보자. 값은 정수, 소수, 문자열 등등 매우 다양하다면, 이를 모두 열거형(`enum`)을 사용해 정의할 수 있다. 그러면 모든 열거형 variant가 같은 타입으로 간주되기 때문에 벡터사용이 가능하다. 코드로 작성하면 다음과 같다.

```rust
fn main() {
    enum SpreadsheetCell {
        Int(i32),
        Float(f64),
        Text(String),
    }

    let row = vec![
        SpreadsheetCell::Int(3),
        SpreadsheetCell::Text(String::from("blue")),
        SpreadsheetCell::Float(10.12),
    ];
}
```

Rust는 컴파일 시 벡터 내에 어떤 타입이 있는지 알아야 한다. 첫 번째 이유는 요소를 저장하기 위해 얼마나 많은 메모리가 필요한지 정확하게 알 수 있다. 두 번째는 이 벡터에 허용되는 유형을 명시할 수 있다는 것이다. 만약 Rust가 벡터에 어떠한 타입이든 담을 수 있도록 허용한다면, 벡터 내의 각 요소마다 수행되는 연산에 대해 하나 혹은 그 이상의 타입이 에러를 야기할 수도 있다. `enum` + `match` 식을 사용하는 것은 앞서 설명한 바와 같이 Rust가 컴파일 타입에 가능한 모든 경우에 대해 처리한다는 것을 보장해준다는 의미다. 만약 프로그램을 작성할 때 벡터에 저장하게 될 타입들을 모른다면, 열거형을 이용하는 방식은 사용할 수 없을 것이다. 대신 트레잇 객체(trait object)를 이용할 수 있는데, 이것은 이후에 다룬다. 표준 라이브러리의 `Vec` 에 정의된 수많은 유용한 메소드들이 있으니 API 문서를 찾아보아야 한다. 예를 들면 `push` 와 더불어 `pop` 메소드는 제일 마지막 요소를 반환하고 지워준다.

---

## 스트링

스트링에 대해 앞부분에서 다뤄 본 적이 있지만 조금더 자세하게 살펴본다. Rust의 스트링은 세 가지 이유로 인해 사람들이 어려워한다. 첫번째 가능한 에러를 꼭 노출하도록 하는 Rust의 특성, 예상보다 더 복잡한 구조의 스트링, 그리고 UTF-8이다. 스트링은 바이트의 집합으로 구현되기 때문에 _collection_ 에서 다룬다. 또한 바이트가 텍스트로 해석될 때 유용한 기능을 제공하는 메소드도 있다. 이 섹션에서 생성, 갱신, 값 읽기와 같은 모든 컬렉션 타입이 가지고 있는 `String` 에서의 연산에 대해 다룬다. 또한 `String` 이 다른 *collection*과 어떻게 다른지, 즉 `String`에 대한 인덱싱이 사람과 컴퓨터의 `String` 데이터 해석 방식에 따라 어떻게 복잡해지는지에 대해서도 설명한다.

우선 *String*이라는 용어가 정확히 무엇을 의미하는지 정의해보자. Rust는 코어 언어에는 딱 한가지 스트링 타입만 제공하는데, 이는 바로 스트링 슬라이스인 `str`이고, 이것의 reference형태인 `&str` 또한 많이 다뤘다. 앞서 *스트링 슬라이스*에 대해 다뤘고, 이는 다른 어딘가에 저장된 UTF-8로 인코딩된 스트링 데이터의 reference다. 예를 들어, 스트링 리터럴은 프로그램의 바이너리 출력물 내에 저장되어 있으며, 그러므로 스트링 슬라이스다.

`String` 타입은 코어 언어 기능 내에 구현된 것이 아니고 Rust의 표준 라이브러리를 통해 제공된다, 이는 커질 수 있고, 가변적이며, 소유권을 갖고 있고, UTF-8로 인코딩된 스트링 타입이다. Rust를 사용하는 사람들이 “스트링"에 대해 이야기할 떄 보통 `String`과 스트링 슬라이스 `&str` 타입 둘 모두를 이야기하는 것이지, 이들 중 하나를 뜻한 것이 아니다. 이번엔 대부분 `String`에 대해 다룰 것이지만, 두 타입 모두 Rust의 표준 라이브러리에서 매우 많이 사용되고 `String`과 스트링 슬라이스 모두 UTF-8로 인코딩 되어있다.

Rust의 표준 라이브러리는 `OsString`, `OsStr`, `CString` 그리고 `CStr` 과 같은 몇몇의 스트링 타입도 제공한다. 심지어 어떤 라이브러리 크레이트들은 스트링 데이터를 저장하기 위해 더 많은 옵션을 제공하기도 한다. `*String / *Str` 이라는 작명과 유사하게 종종 소유권이 있는 타입과 이를 빌린 변형타입을 제공하기도 하는데 이는 `String / &str` 과 비슷하다. 이러한 스트링 타입들은, 다른 종류의 인코딩이 된 텍스트를 저장하거나 다른 방식으로 메모리에 저장될 수 있다. 여기서 다른 스트링 타입은 다루지 않겠지만, 각 타입에 대해 어떤 경우에 적합한지에 대해 알고싶다면 해당 API 문서를 확인해야 한다.

### 새로운 스트링 생성하기

`Vec` 에서 쓸 수 있는 많은 연산들이 `String`에서도 마찬가지로 똑같이 쓰일 수 있는데, `new` 함수를 이용해 스트링을 생성 할 수 있다.

오른쪽 코드는 어떤 데이터를 담아둘 수 있는 `s`라는 빈 스트링을 만들어준다. 그렇지만 종종 스트링에 담아두고 시작할 초기값을 가지고 있을 수 있다. 그런 경우, `to_string` 메소드를 사용하는데 이는 `Display` 트레잇이 구현된 어떤 타입이든 사용 가능하고, 스트링 리터럴 또한 이 트레잇을 구현되어 있어 사용할 수 있다.

```rust
let data = "initial contents";

let s = data.to_string();

// the method also works on a literal directly:
let s = "initial contents".to_string();
```

```rust
let mut s = String::new();
```

또한 스트링 리터럴로부터 `String` 을 생성하기 위해 `String::from` 함수를 사용할 수도 있다.

```rust
let s = String::from("initial contents");
```

이 코드는 왼쪽 코드와 동일하다. 스트링은 너무 많은 곳에서 사용되기 때문에 스트링을 위해 다양한 제네릭 API들을 사용할 수 있고 다양한 옵션들을 제공한다. 여기서 `String::from` 과 `.to_string` 은 정확히 동일한 작업을 하고, 어떤 것을 사용할지는 본인의 코딩 스타일에 달린 문제다.

---

### 스트링 갱신하기

`String` 은 크기가 커질 수 있고 이것이 담고 있는 내용물은 `Vec` 의 내용물과 마찬가지로 더 많은 데이터를 집어넣어서 변경될 수 있다. `+` 연산자나 `format!` 매크로를 이용해 편리하게 `String` 값들을 서로 접합 (concatenation) 할 수 있다.

> `push_str`과 `push`를 이용해 스트링 추가하기

다음과 같이 스트링 슬라이스를 추가하기 위해 `push_str` 메소드를 사용할 수 있다.

```rust
let mut s = String::from("foo");
s.push_str("bar");
```

`s`는 “foobar”를 담게 될 것이다. `push_str` 메소드는 스트링 슬라이스를 파라미터로 갖는데 이는 파라미터의 소유권을 가져올 필요가 없기 때문이다. 다음은 `s1`에 `s2`를 추가하는 상황이다.

```rust
let mut s1 = String::from("foo");
let s2 = "bar";
s1.push_str(&s2);
println!("s2 is {}", s2);
```

만약 `push_str` 함수가 `s2`의 소유권을 가져갔다면 마지막 줄에 그 값을 출력할 수 없을 것이다. 하지만 이 코드는 정상적으로 작동한다.

`push` 메소드는 한 개의 글자를 파라미터로 받아서 `String` 에 추가한다.

```rust
let mut s = String::from("lo");
s.push('l');
```

위에 코드를 실행하면 `s`는 `lol`을 담고 있다.

---

만약 여러 스트링을 접합 하고자 하면 `+` 는 다루기 불편하다.

```rust
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

let s = s1 + "-" + &s2 + "-" + &s3;
```

`s` 는 `tic-tac-toe` 가 될 것이다. 모든 `+` 와 `“` 문자들을 보면 어떤 결과각 나올지 알기 어렵다. 따라서 더 복잡한 스트링 조합을 위해 `format!` 매크로를 사용할 수 있다.

> `+` 연산자나 `format!` 매크로를 이용한 접합

두 개의 스트링을 더하고 싶으면 `+` 연산자를 사용할 수 있다.

```rust
let s1 = String::from("Hello, ");
let s2 = String::from("world!");
let s3 = s1 + &s2; // s1은 여기서 이동되어 더이상 쓸 수 없음을 유의
```

위 코드 실행 결과로 `s3` 는 `Hello, world!` 를 담게 될 것이다. `s1` 이 더하기 연산 이후에 더이상 유효하지 않고, `s2`의 reference 가 사용되는 이유는 `+` 연산자를 호출되는 함수의 시그니처와 맞춰야 하기 때문이다. `+` 연산자는 `add` 메소드를 사용하는데, 이 메소드의 시그니처는 아래와 같이 생겼다.

```rust
fn add(self, s: &str) -> String {
```

이게 표준 라이브러리에 있는 정확한 시그니처는 아니다. 표준 라이브러리 내에서 `add`는 제네릭을 이용하여 정의되어 있는데 이는 나중에 다룬다.

첫번째로 `s2`는 `&`를 가지고 있는데, 이는 `add` 함수의 `s` 파라미터 때문에 첫번째 스트링에 두번째 스트링의 *reference*를 더하고 있음을 의미한다. `String`에 `&str`만 더할 수 있고 두 `String`은 더하지 못하는 것이다. 하지만 여기서 `&s2`의 타입은 `&str` 이 아니라 `&String` 이다. 그럼에도 불구하고 이 코드가 작동하는 이유는 `&String` 인자가 `&str` 으로 강제될 수 있기 때문이다. `add` 함수가 호출되면, Rust는 역참조 강제(_derf coercion_)라 불리는 것을 사용하는데, 이는 `add` 함수 내에서 사용되는 `&s` 가 `&s2[..]` 로 바뀌는 것으로 생각할 수 있도록 한다. 역참조 강제 또한 이후에 다룬다. `add`가 파라미터의 소유권을 가져가지는 않으므로, `s2`는 연산 이후에도 유효한 `String`이다.

두번째로 시그니처에서 `add`가 `self`의 소유권을 가져가는 것을 볼 수 있다. 이는 `self`가 `&`를 붙이고 있지 않기 때문이다. 즉 위 예제를 실행하고 나면 `s1`은 더 이상 유효하지 않다. 따라서 `let s3 = s1 + &s2;` 가 마치 두 스트링을 복사해서 새로운 스트링을 만든 것처럼 보이지만, 실제로 이 구문은 `s1`의 소유권을 가져가 `s2`의 복사본을 추가하고 결과물의 소유권을 반환하는 것이다. 다시 말하면 이 구문은 여러 복사본을 만드는 것처럼 보이지만 그렇지 않다. 이러한 구현은 복사보다 효율적이다.

```rust
let s1 = String::from("tic");
let s2 = String::from("tac");
let s3 = String::from("toe");

let s = format!("{}-{}-{}", s1, s2, s3);
```

이 코드 또한 `s`에 `tic-tac-toe`를 저장한다. `format!` 매크로는 `println!` 과 똑같은 방식으로 작동하지만, 스크린에 결과를 출력하는 대신 결과를 담은 `String`을 반환한다. `format!`을 이용한 방법이 훨씬읽기 쉽고, 또한 **어떠한 소유권도 가져가지 않는다.**

---

### 스트링 내부의 인덱싱

많은 프로그래밍 언어에서 인덱스를 이용한 참조를 통해 스트링 내부의 개별 문자들에 접근하는 것은 당연하고 범용적인 연산에 속한다. 그러나 Rust에서 인덱싱 문법을 이용해 `String` 의 부분에 접근하고자 하면 에러를 얻게 된다.

```rust
let s1 = String::from("hello");
let h = s1[0];
```

에러속 노트 부분에서 Rust의 스트링은 지원하지 않는다고 알려준다.

```
error: the trait bound `std::string::String: std::ops::Index<_>` is not
satisfied [--explain E0277]
  |>
  |>     let h = s1[0];
  |>             ^^^^^
note: the type `std::string::String` cannot be indexed by `_`
```

---

> 내부적 표현

String은 `Vec<u8>`을 감싼 것이다.(wrapper)

```rust
let len = String::from("Hola").len();
```

이 경우 `len` 은 4가 되는데, 이는 스트링 “Hola”를 저장하고 있는 `Vec` 이 4바이트 길이라는 뜻이다. UTF-8로 인코딩 되면 각각의 글자들이 1바이트씩 차지한다는 것이다.

```rust
let len = String::from("Здравствуйте").len();
```

하지만 이 스트링의 길이는 12가 아니라 24다. 이는 "Здравствуйте" 를 UTF-8 로 인코딩된 바이트들의 크기인데, 각각의 유니코드 스칼라 값이 저장소의 2바이트를 차지하기 때문이다. 따라서 스트링의 바이트들 안에 인덱스는 유효한 유니코드 스칼라 값과 항상 대응되지 않는다.

```rust
let hello = "Здравствуйте";
let answer = &hello[0];
```

UTF-8로 인코딩될 때 첫글자 `3`은 208, 151 두 바이트로 인코딩 된다. `answer`은 208이 반환되겠지만 사용자의 의도대로 `3` 전체를 참조하지 못한다. 기대치 않은 값을 반환하고 즉시 발견하기 힘들지도 모를 버그의 발생을 방지하기 위해 Rust는 이러한 코드를 컴파일 타임때 걸러준다.

> 바이트와 스칼라 값과 문자소 클러스터(Grapheme cluster)!

UTF-8 에 대한 또다른 주목해야할 점은 Rust가 문자열을 바라보는 방법에는 바이트, 스칼라 값, 그리고 문자소 클러스터( 우리가 _글자,letter_ 라고 부르는 것과 가장 근접한 것) 3가지가 있다는 것이다.

데바가나리 글자로 쓰여진 힌디어 "नमस्ते” 를 보면, 이것은 아래와 같이 `u8` 값들의 `Vec`로서 저장된다.

```rust
[224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164,
224, 165, 135]
```

이것은 18바이트고 컴퓨터가 이 데이터를 저장하는 방법이다. 만일 이것을 유니코드 스칼라 값, 즉 Rust의 `char` 타입인 형태로 본다면 다음과 같다.

```rust
['न', 'म', 'स', '्', 'त', 'े']
```

여섯개의 `char` 값이 있지만, 네번째와 여섯번째는 글자가 아니다. 그 자체로는 사용할 수없는 발음 기호다. 마지막으로, 이를 문자소 클러스로서 본다면 이 힌디 단어를 구성하는 네 글자를 얻는다.

```rust
["न", "म", "स्", "ते"]
```

Rust는 컴퓨터가 저장하는 가공되지 않은(raw) 스트링을 번역하는 다른 방법을 제공하고, 데이터가 담고 있는 것이 어떤 언어든 상관없이 각각의 프로그램이 필요로 하는 통역방식을 선택할 수 있다.

Rust가 `String`을 인덱스로 접근하지 못하는 마지막 이유는 연산시간이 언제나 상수시간 O(1) 에 실행될 것으로 기대받기 때문이다. 그러나 `String`을 가지고 그러한 성능을 보장하는 것은 불가능하다. 그 이유는 Rust가 스트링 내에 얼마나 많은 유효문자가 있는지 알아내기 위해 내용물의 시작 지점부터 인덱스로 지정된 곳까지 훑어야 하기 때문이다.

---

### 스트링 내에서 반복적으로 실행되는 메소드

그렇다고 스트링의 요소에 접근하는 방법이 없는 것이 아니다.

만일 개별적인 유니코드 스칼라 값에 대한 연산을 수행하고 싶다면, `chars` 메소드를 이용해 개별 요소에 접근 할 수 있다. `chars`를 “नमस्ते”에 대해 호출하면 `char` 타입의 6개의 값으로 나누어 반환하고, 각각의 요소에 접근하기 위해 반복문을 사용할 수 있다.

```rust
for c in "नमस्ते".chars() {
    println!("{}", c);
}
```

다음과 같은 결과를 얻는다.

```rust
न
म
स
्
त
े
```

`bytes` 메소드는 가공되지 않은 각각의 바이트를 반환한다.

```rust
for b in "नमस्ते".bytes() {
    println!("{}", b);
}
```

이 코드는 아래처럼 시작되는 `String`을 구성하는 18파이트를 출력한다.

```rust
224
164
168
224
// ... etc
```

하지만 유효한 유니코드 스칼라 값이 하나 이상의 바이트로 구성될 수도 있다는 것을 기억해야 한다.

스트링으로부터 문자소 클러스터를 얻는 방법은 복잡해서 표준 라이브러리를 통해 제공되지 않는다. 이 기능은 [crates.io](http://crates.io) 에서 사용 가능한 크레이트가 있다.

---

### 스트링은 그렇게 단순하지 않다

정리하면 스트링은 복잡하다. 다른 프로그래밍 언어들은 이러한 복잡함을 프로그래머에게 어떻게 보여줄지에 대해 각기 다른 선택을 한다. Rust는 `String` 데이터의 올바른 처리가 모든 Rust 프로그램에 대한 기본적인 동작이 되도록 선택했다. 이는 프로그래머들이 UTF-8 데이터를 처리하는데 있어 더많은 생각을 해야 한다는 것이다. 이러한 트레이드오프는 다른 프로그램이 언어들에 비해 더복잡한 스트링이지만 한편으로는 이후 개발자 인생에서 ASCII 가 아닌 문자를 다룰 때 에러를 처리해야할 것을 막아줄 것이다.

---

## 해쉬맵(hash map)

해쉬맵, `HashMap<K, V>` 타입은 `K` 타입의 키에 `V` 타입의 값을 매핑한 것을 저장한다. 이는 _해쉬 함수(hashing function)_ 을 통해 동작하는데, 해쉬 함수는 이 키와 값을 메모리 어디에 저장할 지 결정한다. 다른 프로그래밍 언어들도 이러한 종류의 데이터 구조를 지원하지만, 종종 해쉬, 맵, 오브젝트, 해쉬 테이블 혹은 연관 배열 등과 같은 몇몇 다른 이름으로 이용된다.

해쉬맵은 벡터를 이용하듯 인덱스를 이용하는 것이 아니라 임의의 타입으로 된 키를 이용하여 데이터를 찾고 싶을때 유용하다. 예를 들면 게임 속에 각 팀의 점수를 해쉬맵에 저장할 수 있는데, 여기서 키는 팀의 이름이고 값은 팀의 점수가 된다. 팀의 이름을 주면 그 팀의 점수를 찾을 수 있는것이다.

해쉬맵의 기본 API를 다루지만 표준 라이브러리의 `HashMap` 에 정의되어 있는 함수 중에는 좋은 기능들이 많다. 이 정보를 확인하고 싶다면 표준 라이브러리 문서를 확인하자.

---

### 새로운 해쉬맵 생성하기

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
```

앞서본 자료형들과 똑같이 `new` 를 이용해 빈 해쉬맵을 만들고, `insert` 를 이용해 요소를 추가할 수 있다. 다음은 각각 이름이 Blue 와 Yellow 인 두 팀의 점수를 저장하고 잇다.

먼저 표준라이브러리 `collections` 로부터 `HashMap`을 `use`로 가져와야한다. 해쉬맵은 앞서 본 두 가지 컬렉션보다 제일 사용빈도가 낮기 때문에 프렐루드(prelude) 내에 자동으로 가져오는 기능이 없다. 또한 해쉬맵은 표준라이브러리는의 지원이 적다. 예를 들면 해쉬맵을 생성하기 위한 built-in 매크로가 없다.

벡터와 마찬가지로, 해쉬맵도 데이터를 힙에 저장한다. 이 `HashMap`은 `String` 타입의 키와 `i32` 타입의 값을 갖는다. 벡터와 비슷하게 해쉬맵도 모든 키는 같은 타입이어야 하고, 모든 값도 같은 타입이어야 한다.

튜플의 벡터에 대해 `collect` 메소드를 사용해 해쉬맵을 생성할 수도있다. `collect` 메소드는 데이터를 모아서 `HashMap` 을 포함한 여러 컬렉션 타입으로 만들 수 있다. 만약 두 개의 분리된 벡터에 각각 팀 이름과 초기 점수를 갖고 있다면, `zip` 메소드를 이용해 “Blue”와 10이 한 쌍이 되는 방식으로 튜플의 벡터를 생성할 수 있다.

`Hash_Map<_, _>` 을 적는 이유는 이는 `collect`가 다른 많은 컬렉션 타입으로 바뀔 수 있고 이것을 무엇으로 변할지 적지 않는다면 어떤것을 원하는지 알 수 없기 때문이다. 그러나 키와 값의 타입을 적지 않은것은 벡터에 담긴 데이터의 타입에 기초하여 타입을 추론할 수 있기 때문에 밑줄을 사용할 수 있다.

```rust
use std::collections::HashMap;

let teams  = vec![String::from("Blue"), String::from("Yellow")];
let initial_scores = vec![10, 50];

let scores: HashMap<_, _> = teams.iter().zip(initial_scores.iter()).collect();
```

---

### 해쉬맵과 소유권

해쉬맵에 매핑할 때 `i32` 처럼 `Copy` 트레잇을 구현한 타입은 해쉬맵 안으로 복사된다. 그러나 `String`과 같이 소유된 값들에 대해서는 값들이 이용되어 해쉬맵이 그 값의 소유자가 된다.

`insert`를 호출해서 `field_name`과 `field_value`를 해쉬맵으로 이동시킨 후 더 이상 이 둘을 사용할 수 없다.

만일 해쉬맵에 값들에 reference를 삽입한다면 이 값들은 해쉬맵으로 이동되지 않는다. 하지만 reference가 가리키고 있는 값은 해쉬맵이 유효할 때까지 계속 유효해야 한다.

```rust
use std::collections::HashMap;

let field_name = String::from("Favorite color");
let field_value = String::from("Blue");

let mut map = HashMap::new();
map.insert(field_name, field_value);
// field_name과 field_value은 이 지점부터 유효하지 않다.
```

---

### 해쉬맵 내의 값 접근하기

`get` 메소드에 키를 넣어 해쉬맵으로부터 값을 얻을 수 있다.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

let team_name = String::from("Blue");
let score = scores.get(&team_name);
```

`score` 는 블루 팀의 점수를 가지고 있을 것이고, 결과값은 `Some(&10)` 일 것이다. 결과값이 `Some`인 이유는 `get`이 `Option<&V>`를 반환하기 때문이다. 만약 해쉬맵 내에 해당 키에 대한 값이 없다면 `get` 은 `None`을 반환한다.

벡터와 동일하게 `for` 문을 사용해서도 반복잡업을 할 수 있다.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

for (key, value) in &scores {
    println!("{}: {}", key, value);
}
```

이 코드는 각각의 쌍을 임의의 순서로 출력할 것이다.

```
Yellow: 50
Blue: 10
```

---

### 해쉬맵 갱신하기

키와 값의 개수는 증가할 수 있지만, 각각의 개별적인 키는 딱 하나의 값만을 가질 수 있다. 해쉬맵 내의 데이터를 변경하고 싶다면, 키에 이미 값이 할당되어 있는경우에 대한 처리를 어떻게 할지 결정해야 한다. 원래 있었던 값을 완전히 무시하면서 새 값으로 대신할 수도 있다. 혹은 해당키에 값이 존재하면 건너 뛰고 값이 할당되지 않았을 경우에만 새 값을 추가하는 방법도 있다. 또는 예전 값과 새로운 값을 조합할 수도있다.

> **값을 덮어쓰기**

만일 해쉬맵에 키와 값을 삽입하고, 그 후 똑같은 키에 다른 값을 삽입하면, 키에 연관지어진 값은 새 값으로 덮어진다. 다음 코드는 `insert` 를 두 번 호출함에도, 해쉬맵은 딱 하나의 키/값 쌍을 담게 된다. 두 번 모두 블루 팀의 키에 값을 삽입하고 있기 때문이다.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Blue"), 25);

println!("{:?}", scores);
```

이 코드는 `{”Blue”: 25}` 를 출력할 것이다. 원래 값 10은 덮어써졌다.

---

> **키에 할당된 값이 없을 경우에만 삽입하기**

특정 키에 대해서 만약 값이 없을경우에만 삽입하고자 한다면 `entry` 라는 특별한 API를 사용할 수 있다. 이는 값이 있는지 없는지 검사하고자 하는 키를 파라미터로 받는다. `entry` 함수의 리턴값은 열거형(enum) `Entry` 인데, 해당 키가 있는지 혹은 없는지를 나타낸다. 만약 옐로우 팀에 대한 키가 값을 가지고 있는지 검사하고 싶어한다고 해보자. 만일 없다면 값 `50`을 삽입하고, 블루팀에 대해 동일하게 적용하고 싶다. 이를 코드로 표현하면 다음과 같다.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);

scores.entry(String::from("Yellow")).or_insert(50);
scores.entry(String::from("Blue")).or_insert(50);

println!("{:?}", scores);
```

`Entry` 에 대한 `or_insert` 메소드는 해당 키가 존재할 경우 관련된 `Entry` 키에 대한 값을 반환하도록 정의되어 있다. 그렇지 않을 경우에는 파라미터로 주어진 값을 해당 키에 대한 새로운 값으로 삽입하고, 수정된 `Entry`에 대한 값을 반환한다. 이 방법은 사용자가 직접 로직을 작성하는 것보다 훨씬 깔끔하고, 빌림 검사기와 잘 어울려 동작한다.

위 코드를 실행하면 `{”Yellow”: 50, “Blue”: 10 }` 을 출력한다. 첫번째 `entry` 호출은 옐로우 팀에 대한 키의 값에 `50`을 삽입한다. 이는 옐로우 팀이 값을 가지고 있지 않기 때문이다. 두번째 `entry` 호출은 불루팀은 이미 값 `10`을 가지고 있기때문에 아무일도 일어나지 않는다.

---

> **예전 값을 기반으로 새로운 값 만들기**

해쉬맵에 흔한 사용방식인 이는 키에 대한 값을 찾아서 이 값을 기반으로 새로운 값으로 갱신하는 것이다. 예를 들어 다음은 어떤 텍스트 내에 각 단어가 몇번이나 나오는지 세는 코드다. 단어를 키로 사용하는 해쉬맵을 이용해서 해당 단어가 몇번이나 나왔는지를 저장하기 위해 값을 증가시킨다. 만일 어떤 단어를 처음본것이라면, 바로 앞에서 배운 `entry` 함수를 이용해 `0`을 삽입한다.

```rust
use std::collections::HashMap;

let text = "hello world wonderful world";

let mut map = HashMap::new();

for word in text.split_whitespace() {
    let count = map.entry(word).or_insert(0);
    *count += 1;
}

println!("{:?}", map);
```

이 코드는 `{”world”: 2, “hello”: 1, “wonderful”: 1}`를 출력한다. `or_insert` 메소드는 실제로는 해당 키의 값의 가변 reference(`&mut V`)를 반환한다. 즉 `count` 변수에 가변 reference를 저장하고, 여기에 값을 할당하기 위해 `*` 를 사용하여 `count`를 역참조한다. 가변 reference는 `for` 루프의 끝에서 스코프 밖으로 벗어나기 때문에 모든 값들의 변경은 안전하고 빌림 규칙에 위배되지 않는다.

---

### 해쉬 함수

기본적으로, `HashMap`은 `SipHash`라는 함수를 사용한다. 이 함수는 *서비스 거부 공격(Denial of Service(Dos) attack)*에 대한 방어를 제공한다. 이것이 가장 빠른 해쉬 알고리즘은 아니지만 성능을 낮추면서 더 나은 보안을 갖는 트레이드오프가 있다. 만약 우리가 스스로의 코드를 프로파일해서 기본 해쉬 함수가 너무 느린 경우 다른 해쉬어(hasher)로 다른 함수로 비끌 수 있다. 해쉬어는 `BuildHasher` 트레잇을 구현한 타입이다. 트레잇과 이를 구현하는지는 이후에 다룬다. 해쉬어를 처음부터 새로 구현할 필요 없다. [crates.io](http://crates.io) 에서는 수많은 범용적인 해쉬 알고리즘을 구현한 공유 라이브러리를 제공한다.

---

# 에러 처리

Rust의 신뢰성에 대한 보장은 에러 처리에도 적용되어 있다. 에러는 소프트웨어에서 피할 수 없는 현실이고, 따라서 Rust는 에러 처리를 위한 몇 가지 기능이 있다. 대부분의 경우 Rust는 에러가 발생할 가능성을 찾아 코드가 컴파일 되기 전에 알려준다. 이런 기능은 코드를 배포하기 전에 에러를 발견하고 적절히 조치할 것임을 보장해준다.

Rust 에서 에러는 두 가지로 구분한다. _복구 가능한(recoverable)_ 에러와 _복구 불가능한(unrecoverable)_ 에러다. 복구 가능한 에러는 사용자에게 문제를 보고하고 연산을 재시도 하는것이 보통의 경우인데, 예를 들면 파일을 찾지 못하는 경우다. 복구 불가능한 에러는 언제나 버그의 증상이 나타난다. 예를 들면 배열의 끝을 넘어선 위치의 값에 접근하려고 시도하는 경우다.

대부분의 언어들은 이 두 종류의 에러를 구분하지 않고, 예외처리 (exception)과 같은 메커니즘을 사용해 모든 에러를 처리한다. Rust는 예외 처리 기능이 없다. 대신, 복구 가능한 에러를 위한 `Result<T, E>` 값과 복구 불가능한 에러가 발생했을때 실행을 멈추는 `panic!` 매크로를 가지고 있다.

---

## panic!과 함께하는 복구 불가능한 에러

`panic!` 매크로가 실행되면 프로그램은 실패 메세지를 출력하고, 스택을 되감고 청소한다. 그 후에 종료된다. 이런 일이 발생하는 가장 흔한 상황은 버그가 발견되었고 프로그래머가 이 에러를 어떻게 처리할지 명확하지 않을때이다.

-   `panic!`에서 스택을 되감거나 그만두기
    기본적으로 패닉이 발생하면 프로그램은 *되감기(unwinding)*를 시작하는데, 이는 러스트가 스택을 다시 걸어 올라가서 마주치는 각 함수의 데이터를 정리한다는 것을 의미한다. 하지만 이는 적은 일이 아니다. 대신에 즉시 중단하고 정리하지 않고 프로그램을 종료할 수도 있따. 그런 다음 프로그램에서 사용하던 메모리를 운영 체제에서 정리해야한다. 프로젝트에서 결과 바이너리를 가능한 작게 만들어야 하는 경우 `Cargo.toml` 파일의 해당 `[profile]` 섹션에 `panic= ‘absort’` 를 적으면 되감기를 그만두기로 바꿀 수 있다. 만약 릴리즈 모드 내에서 패닉 상에서 그만두기를 쓰고 싶다면 다음과 같이 작성하면 된다.
    ```rust
    [profile.release]
    panic = 'absort'
    ```

그냥 프로그램에서 `panic!`을 호출해보자.

```rust
fn main() {
    panic!("crash and burn");
}
```

`panic!` 은 마지막 세 줄의 에러 메세지를 출력한다. 첫 번째 줄은 패닉 메세지와 함께 소스 코드에서 패닉이 발생한 지점을 보여준다. _src/main.rs:2_ 는 _src/main.rs_ 파일의 두 번째 줄을 의미한다.

지금은 해당 줄이 `panic!`을 호출한 지점이지만 다른 지점일 수도있다. 에러 메세지에 적혀있는 파일 이름과 라인 번호는 `panic!` 매크로가 호출된 다른 누군가의 코드일 것이고, `panic!`을 이끌어낸 것이 본인의 코드 라인이 아닐 수도 있다. 문제를 일으킨 코드 부분을 발견하기 위해서 `panic!` 호출이 발생된 함수에 대한 백트레이스(backtrace)를 할 수 있다.

```
$ cargo run
   Compiling panic v0.1.0 (file:///projects/panic)
    Finished dev [unoptimized + debuginfo] target(s) in 0.25 secs
     Running `target/debug/panic`
thread 'main' panicked at 'crash and burn', src/main.rs:2
note: Run with `RUST_BACKTRACE=1` for a backtrace.
error: Process didn't exit successfully: `target/debug/panic` (exit code: 101)
```

---

### panic! 백트레이스 사용하기

직접 `panic!` 매크로를 호출하지 않고 버그 때문에 `panic!` 호출이 라이브러리부터 발생될 되는 경우를 보자

```rust
fn main() {
    let v = vec![1, 2, 3];

    v[99];
}
```

벡터는 3개의 요소만 가지고 있지만 100번째 요소에 접근을 시도하고 있다. 이러한 경우 Rust는 패닉을 일으킨다. `[]`를 사용하는 것은 어떤 요소를 반환하기를 가정하지만, 유효하지 않은 인덱스를 넘기게 되면 Rust가 반환할 올바른 요소가 없다.
이러한 상황에서 C와 같은 다른 언어들은 의도치 않은 것일지라도 요청한 그대로 정확히 주려고 시도할 것이다. 벡터 내에 해당 요소와 상응하는 위치의 메모리에 들어 있는 뭔가를 얻을 것이다. 설령 그 메모리 영역이 벡터의 소유가 아닐지라도 상관없다. 이러한 것을 버퍼 _오버리드(buffer overread)_ 라고 부르며, 만일 어떤 공격자가 메모리상 벡터 뒤에 존재하는 중요한 내용을 유효하지 않은 인덱싱을 통해 접근한다면 이는 보안 취약점을 발생시킬 수 있다.

이러한 종류의 취약점으로부터 보호하기 위해서, 존재하지 않은 인덱스 상의 요소를 읽으려 시도한다면 Rust는 실행을 멈춘다.

```
$ cargo run
   Compiling panic v0.1.0 (file:///projects/panic)
    Finished dev [unoptimized + debuginfo] target(s) in 0.27 secs
     Running `target/debug/panic`
thread 'main' panicked at 'index out of bounds: the len is 3 but the index is
100', /stable-dist-rustc/build/src/libcollections/vec.rs:1362
note: Run with `RUST_BACKTRACE=1` for a backtrace.
error: Process didn't exit successfully: `target/debug/panic` (exit code: 101)
```

위 에러에서 *libcollecetions/vec.rs*를 가리키고 있다. 이는 표준 라이브러리 속 `Vec<T>`의 구현 부분이다. 벡터 `v`에 `[]`를 사용할 때 실행되는 코드는 _libcollections/vec.rs_ 안에 있고, 이곳이 바로 `panic!`이 실제 발생한 곳이다.

그 다음 줄에서 `RUST_BACKTRACE` 환경 변수를 설정하면 에러의 원인이 된 것이 무엇인지 정확하게 백트레이스 할 수 있다고 알려준다. **백트레이스(backtrace) 란 어떤 지점이 도달하기까지 호출해온 모든 함수의 리스트를 말한다.** Rust의 백트레이스는 다른 언어들과 동일하게 동작한다. 백트레이스를 읽는 요령은 위에서부터 시작해 작성한 파일이 보일 때까지 읽는 것이다. 그곳이 바로 문제를 일으킨 지점이다. 작성한 파일을 출력한 줄보다 위에 있는 줄은 작성한 코드가 호출한 코드다. 밑의 코드는 작성된 코드를 호출한 코드다. 이 줄들은 핵심(core) Rust 코드, 표준 라이브러리, 혹은 이용하고 있는 크레이트를 포함한다. 백트레이스를 얻어보자.

출력이 매우 많다. 실제 출력값은 운영 체제 및 Rust 버전에 따라 다를 수 있다. 이러한 정보들과 함께 백트레이스를 얻기 위해서는 디버그 심볼이 활성화되어 있어야 한다. 디버그 심볼은 여기서와 마찬가지로 `cargo build`나 `cargo run` 을 `--release` 플레그 없이 실행했을때 기본적으로 활성화 된다.

다음 출력값에서 백트레이스의 11번째 라인이 문제를 일으킨 프로젝트의 라인을 가리키고 있다. 바로 *src/main.rs*의 4번째 줄이다. 이 지점이 바로 패닉에 빠진 지점이므로 에러를 찾기 시작해야할 지점이다. 위 코드에서 패닉을 고칠 방법은 3개의 아이템을 가진 벡터로부터 인덱스 100의 요소를 요청하지 않는것이다. 만약 추후 코드에서 패닉에 빠졌을 때 특정한 경우에 대해 어떤 코드가 패닉을 일으키는 값을 만드는지, 그리고 그 코드가 어떻게 되어야 할지 알아낼 필요가 있다

```
$ RUST_BACKTRACE=1 cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running `target/debug/panic`
thread 'main' panicked at 'index out of bounds: the len is 3 but the index is 100', /stable-dist-rustc/build/src/libcollections/vec.rs:1392
stack backtrace:
   1:     0x560ed90ec04c - std::sys::imp::backtrace::tracing::imp::write::hf33ae72d0baa11ed
                        at /stable-dist-rustc/build/src/libstd/sys/unix/backtrace/tracing/gcc_s.rs:42
   2:     0x560ed90ee03e - std::panicking::default_hook::{{closure}}::h59672b733cc6a455
                        at /stable-dist-rustc/build/src/libstd/panicking.rs:351
------------------------------생략-----------------------------------
   9:     0x560ed9113fc8 - core::panicking::panic_bounds_check::h02a4af86d01b3e96
                        at /stable-dist-rustc/build/src/libcore/panicking.rs:56
  10:     0x560ed90e71c5 - <collections::vec::Vec<T> as core::ops::Index<usize>>::index::h98abcd4e2a74c41
                        at /stable-dist-rustc/build/src/libcollections/vec.rs:1392
  11:     0x560ed90e727a - **panic**::main::h5d6b77c20526bc35
                        at /home/you/projects/panic/src/main.rs:4
  12:     0x560ed90f5d6a - __rust_maybe_catch_panic
                        at /stable-dist-rustc/build/src/libpanic_unwind/lib.rs:98
  13:     0x560ed90ee926 - std::rt::lang_start::hd7c880a37a646e81
                        at /stable-dist-rustc/build/src/libstd/panicking.rs:436
                        at /stable-dist-rustc/build/src/libstd/panic.rs:361
                        at /stable-dist-rustc/build/src/libstd/rt.rs:57
  14:     0x560ed90e7302 - main
  15:     0x7f0d53f16400 - __libc_start_main
  16:     0x560ed90e6659 - _start
  17:                0x0 - <unknown>
```

---

---

---

---

---

---

---

---

[](Rust%206c87bc32962b444cb4dc5be72e8029d7/%EC%A0%9C%EB%AA%A9%20%EC%97%86%EC%9D%8C%20b1c6766a5c7e47848ba3fa1ba2816c3b.md)

---
