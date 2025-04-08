# Rust

# 보편적인 프로그래밍 개념

## 변수의 가변성

Rust의 기본 변수는 불변성이다. 

변수가 불변성인 경우 일단 값이 이름에 bound 되면 해당 값을 변경할 수 없다.

```rust
fn main(){
	let x = 5;
	println!("The value of x is : {}", x);
	x = 6;
	println!("The value of x is : {}", x);d                       
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

1. 상수에 대해서는 `mut` 을 사용할 수 없다.  → 상수는 기본 설정이 불변성인 것이 아니라 그 자체가 불변성이다. 
2. 상수를 사용하고자 하면 `let` 키워드 대신 `const` 키워드를 사용해야 하고, 값의 유형을 선언해야 한다. → [데이터 타입들](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21)
3. 상수는 전체 영역을 포함하여 어떤 영역에서도 선언될 수 있다. 이는 코드의 많은 부분에서 사용될 필요가 있는 값을 다루는데 유용하다.
4. 상수는 오직 상수 표현식만 설정될 수 있다. 함수 호출의 결과값이나 그 외에 실행 시간에 결정되는 값이 설정될 수 없다.

```rust
const MAX_POINTS: u32 = 100_100;
```

상수는 자신이 선언되어 있는 영역 내에서 프로그램이 실행되는 시간 동안 항상 유효하다. 따라서 프로그램의 다양한 곳에서 사용되는 값을 상수로 하면 유용하다. 예를 들면 사용자가 한 게임에서 획득할 수 있는 최대 점수, 빛의 속도 같은 값 등등...

---

### Shadowing

이전에 선언한 변수와 같은 이름의 새 변수를 선언할 수 있고, 새 변수는 이전 변수를 *shadows* 하게 된다. 또는 이전 변수가 두 번째 변수에 의해 *shadowed* 됐다고 표현한다. `let` 키워드를 사용해서 반복해서 같은 변수명으로 변수를 shadow 할 수 있다.

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

- 스칼라
- 컴파운드

둘로 나눌 수 있다.

Rust는 `statically typed language`다. 즉 정적타입 언어로 **컴파일 시 변수의 타입이 결정되어 있어야 한다.** 보통 컴파일러는 값과 사용방법에 따라 어떤 유형을 사용할지 추론할 수 있다. 타입의 선택 폭이 넓은 경우는 반드시 타입 명시해야 한다.

### 스칼라 ( Scalar )

스칼라는 하나의 값으로 표현되는 타입이다. Rust는 정수형, 부동소수점 숫자, boolean, 그리고 문자까지 총 네 가지 스칼라 타입이 있다. 

> **정수형 ( Integer )**
> 

정수형은 소수점이 없는 숫자다. `u32` 타입은 부호없는 32비트 변수임을 나타낸다. 부호 있는 타입은 `u` 대신 `i` 로 시작한다. 각각의 타입은 부호 혹은 미부호이며 명시된 크기를 갖는다. 부호와 미부호가 의미하는 바는 숫자가 양수 와 음수를 다룰 수 있는지를 나타낸다. 숫자가 부호를 둘 다 가져야 하는 경우는 부호가 필요하지만(Signed) 양수만 다룰 것이라면 부호가 필요없다(Unsigned).

각 Signed변수는 $-(2^{n-1})$부터 $2^{n-1}-1$까지의 값을 나타낼 수 있다. 여기서 n은 타입의 비트 수다. 즉 `i8`은 $-(2^7)$에서 $2^7-1$까지의 값을 저장할 수 있다. 반대로 `u8`은 0에서 $2^8-1$ 즉 0에서부터 255까지 값을 저장할 수 있다.

추가적으로 `isized`, `usize`타입은 프로그램이 동작하는 컴퓨터 환경이 64-bits인지 아닌지에 따라 결정된다. 64-bit 아키텍쳐이면 64bit를, 32-bit 아키텍쳐이면 32bit를 갖게 된다.

정수형은 Number literals를 사용해 표현할 수 있다. byte literal을 제외하고 모든 Number literals은 `57u8` 과 같은 타입 접미사와 `1_000`과 같이 시각적인 구분을 위한 `_` 사용을 허용한다.

확실하게 정해진 경우가 아니라면 Rust의 기본 값인 `i32` 가 일반적으로 좋은 선택이다. 이유는 보통 가장 빠르기 때문이다. 심지어 64-bit 시스템에서도.

| Length | Signed | Unsigned |
| --- | --- | --- |
| 8-bit | `i8` | `u8` |
| 16-bit | `i16` | `u16` |
| 32-bit | `i32` | `u32` |
| 64-bit | `i64` | `u64` |
| arch | `isize` | `usize` |

| Number literals | Example |
| --- | --- |
| Decimal | `98_222` |
| Hex | `0xff` |
| Octal | `0o77` |
| Binary | `0b1111_0000` |
| Byte(`u8` only) | `b’A’` |

---

> **수학적 연산 ( Numeric Operations )**
> 

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
> 

대부분의 다른 언어처럼 boolean 타입은 Rust에서 둘 중 하나의 값만 가질 수 있다. `true`와 `false`.

rust내에서는 `bool`로 표현한다.

```rust
fn main() {
    let t = true;

    let f: bool = false; // with explicit type annotation
}
```

> **부동 소수점 타입 ( Floating-Point )**
> 

Rust에는 부동소수점 숫자를 위한 두 가지 기본 타입이 있다. `f32`와 `f64`로 각각 32bit와 64bit의 크기를 갖는다. 기본타입은 `f64`인데, 그 이유는 최신 CPU 상에서는 `f32`와 `f64`의 속도가 대략 비슷하기 때문에 더 정밀한 표현이 가능한 `f64`가 기본 타입이다. 

예제 :

```rust
fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}
```

> **문자 타입 ( Character )**
> 

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

- 튜플
- 배열

> **튜플 ( Tuple )**
> 

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
> 

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
> 

배열은 `스택`에 단일 메모리 뭉치로 할당된다. 인덱싱을 통해 배열의 요소에 접근할 수 있다.

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```

> **유효하지 않은 배열 요소에 대한 접근**
> 

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

Rust코드는 snake case를 변수나 함수 이름의 형식규칙으로 사용한다. snake case에서 모든 문자는 소문자를 사용하고, 단어구분은 밑줄표시(_)로 한다.

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
> 

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
> 

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

`if` 블록이 정수형을 산출하고 `else`블록은 문자열을 산출한다. 이것이 [에러를 갖는 이유](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21)는 변수가 가질 수 있는 타입은 오직 하나이기 때문이다. Rust는 컴파일 시에 `number`변수의 타입이 뭔지 **확실히** 정의해야 한다. Rust는 number의 타입을 실행 시에 정하도록 하지 않는다.

---

### 반복문과 반복

Rust는 몇 가지 반복문을 제공한다.

- `loop`
- `while`
- `for`

> **loop**
> 

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
> 

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
> 

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

1번은 우리가 직접 수행한다.  `String::from`을 호출하면, 구현부분에서 필요한 만큼의 메모리를 요청한다. 

하지만 2번은 다르다. Garbage Collector(GC)를 갖고 있는 언어들은, GC가 더이상 사용하지 않는 메모리 조각들을 계속해서 찾고 지워주며, 우리는 이러한 생각을 할 필요가 없다. GC가 없을 경우, 할당받은 메모리가 더이상 필요없는 시점을 알아서 명시적으로 이를 반납하는 코드를 호출하는 것은 프로그래머의 책임이다. 프로그래머가 이를 잊어버리면 메모리를 낭비하는 셈이다. 너무 빨리 반납해버리면 유효하지 않은 변수를 갖게 될 것이다. 반납을 두 번 한다면 이것 또한 버그다. 프로그래머는 딱 한번의 `allocate` 와 한번의 `free` 쌍을 사용해야 한다.

Rust는 다른 방식으로 사용하지 않는 메모리를 처리한다. 메모리는 변수가 소속되어 있는 스코프 밖으로 벗어나는 순간 자동으로 반납된다.

```rust
{    
		let s = String::from("hello"); // s는 여기서부터 유효하다.

    // s를 가지고 뭔가 할 수 있다.
}                                  // 이 스코프는 끝났고, s는 더 이상 
                                   // 유효하지 않다.
```

`String`이 요구한 메모리를 운영체제에게 반납하는 지점이 있다. 바로 `s` 가 스코프 밖으로 벗어날 때다. 변수가 스코프 밖으로 벗어나면 Rust는  `drop`함수를 호출한다. Rust는 `}` 괄호가 닫힐때 자동적으로 `drop`을 호출한다.

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

1. 문자열의 내용물을 담고 있는 메모리의 포인터
2. 내용물의 길이값
3. 내용물의 용량값

이 데이터는 `스택`에 저장된다. 하지만 내용물을 담은 오른쪽은 `힙` 메모리에 있다.

내용물의 길이값은 바이트 단위로 `String`의 내용물이 얼마나 메모리를 현재 사용하고 있는지 나타낸다.

내용물의 용량값은 운영체제로부터 얼마나 많은 양의 메모리를 할당 받았는지를 말한다. 이 길이와 용량의 차이는 중요하다. 

![trpl04-01.svg](Rust%200d7ead296adb4593aa99b2b8cb2e5307/trpl04-01.svg)

---

`s2`에 `s1`을 대입하면 `String` 데이터가 복사되는데, 이는 `스택`에 있는 포인터, 길이값, 그리고 용량값이 복사된다는 의미다. 포인터가 가리키고 있는 `힙` 메모리 상의 데이터는 복사되지 않는다. 다시 말하면 메모리 내의 데이터 구조는 다음과 같다.

![trpl04-02.svg](Rust%200d7ead296adb4593aa99b2b8cb2e5307/trpl04-02.svg)

이 그림은 만약 Rust가 `힙` 메모리 상의 데이터까지도 복사한다면 벌어질 일이다. 

만일 Rust가 이렇게 동작한다면, 힙 안의 데이터가 클 경우 `s2 = s1` 연산은 매우 느려질 가능성이 있다.

![trpl04-03.svg](Rust%200d7ead296adb4593aa99b2b8cb2e5307/trpl04-03.svg)

---

앞서 변수가 스코프 밖에서 벗어날 때, Rust는 자동적으로 `drop` 함수를 호출하여 해당 변수가 사용하는 `힙` 메모리를  제거한다고 했다. 하지만 복사후 두 데이터 포인터가 모두 같은 곳을 가르키고 있다. 이것은 문제다. `s2`와 `s1`이 이 스코프 밖으로 벗어나게 되면, 둘 다 같은 메모리를 해체하려 할 것이다. 이는 두번 해체(double free) 오류라고 알려져 있으며 앞서 언급했던 메모리 안정성 버그 중 하나다. 메모리를 두 번 해체하는 것은 메모리 손상(memory corruption) 의 원인이 되는데 이는 보안 취약성 문제를 일으킬 가능성이 있다.

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

![trpl04-04.svg](Rust%200d7ead296adb4593aa99b2b8cb2e5307/trpl04-04.svg)

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

이 코드는  잘 작동한다. 앞서 배운 내용과 충돌되는 것처럼 보인다. `clone`을 호출하지 않았지만 `x`도 유효하며 `y`로 이동하지도 않았다.

그 이유는 정수형과 같이 컴파일 할때 이미 결정되어 있는 크기의 타입은 모두 `스택`에 저장되기 때문에 실제 값의 복사본이 빠르게 만들어질 수 있다. 이는 변수 `y`가 생성된 후에 `x`가 더 이상 유효하지 않도록 해야할 이유가 없다는 것이다. 다시 말하면 여기서는 깊은 복사와 얕은 복사간의 차이가 없다는 것으로 `clone`을 호출하는 것이 보통의 얕은 복사와 아무런 차이가 없다는 것이다.

Rust는 정수형과 같이 스택에 저장할 수 있는 타입에 대해 달수 있는 `Copy` 트레잇이라고 불리우는 특별한 어노테이션(annotation)을 가지고 있다. 만일 어떤 타입이 `Copy` 트레잇을 갖고 있다면, 대입 과정 후에도 예전 변수를 계속 사용할 수 있다. Rust는 만일 그 타입 혹은 그 타입이 가지고 있는 부분 중에서 `Drop` 트레잇을 구현한 것이 있다면 `Copy` 트레잇을 어노테이션 없게끔 한다. 만일 어떤 타입이 스코프 밖으로 벗어날 때 어떤 특수한 동작을 필요로 하고 우리가 그 타입에 대해 `Copy` 어노테이션을 추가한다면, 컴파일 타임 오류를 보게 된다. 

`Copy`가 되는 타입은 일반적인 규칙으로서 단순한 스칼라 값들의 묶음은 `Copy`가 가능하고, 할당이 필요하거나 어떤 자원의 형태인 경우 `Copy`를 사용할 수 없다. `Copy`가 가능한 몇가지 타입을 나열해보면

- `u32` 와 같은 모든 정수형 타입들
- `true` 와 `false` 값을 갖는 boolean 타입 `bool`
- `f64` 와 같은 모든 부동 소수점 타입들
- `Copy` 가 가능한 타입들로만 구성된 튜플들. `(i32, i32)` 는 `Copy`가 되지만, `(i32, String)` 은 안된다.

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

![trpl04-05.svg](Rust%200d7ead296adb4593aa99b2b8cb2e5307/trpl04-05.svg)

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

그렇다면 함수의 시그니처에 대해 생각해봤을때 무슨 타입으로 반환 해야 할까?   >  `fn first_word(s: &String) → ?`

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

이후 바이트 리터럴 문법을 이용해 공백문자를 찾는다.  공백 문자를 찾았다면, 이 위치를 반환한다. 그렇지 않으면 `s.len()` 을 통해 스트링의 길이값을 반환한다.

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

왼쪽 프로그램은 아무런 오류 없이 컴파일되고, `s.clear()` 를 호출한 뒤 `word`를 사용한다 해도 컴파일이 될 것이다. `word` 와 `s` 는 전혀 연결되어 있지 않은 변수이므로, `word` 는 여전히 `5` 를 담고 있다. 첫번째 단어를 추출하기 위해 `s` 와 `5` 를 사용할 수 있지만 이후에 `s`의 내용물이 변경 되었기 때문에 이러한 사용은 버그가 될 것이다.

이러한 오류는 매우 자주 발생할 수 있는 오류다. 만약 두번째 단어를 추출하는 `second_word` 함수를 작성했을때 더더욱 다루기 어려워진다. 이 함수의 시그니처는 아마 다음과 같을 것이다.

`fn second_word(s: &String) → (usize, usize) {`

첫 단어와 다르게 두번째 단어는 시작지점과 끝지점의 인덱스를 추출할 것이다. 매개변수로 들어온 데이터로부터   계산 되었지만 그 상태와 전혀 묶여 있지 않은걸 하나 더 갖게 되었다. 데이터와 그 데이터로부터 추출한 특정 데이터의 동기화를 유지할 필요가 있다는 것이다.

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

대괄호 내에 `[starting_inx..ending_index]` 를 이용해 특정한 범위의 슬라이스를 만들 수 있다. 내부적으로 슬라이스 데이터 구조는 시작 위치와 슬라이스의 길이를 저장한다. 이 길이의 값은 `ending_index` 에서 `starting_index` 를 뺀 값이다. 따라서 `let world = &[6..11];` 의 경우 `world` 는  `s` 의 6번째 바이트를 가리키고 있는 포인터와 길이값 5를 갖고 있는 슬라이스가 될 것이다.

Rust의 .. 문법을 사용할 때 첫번째 인덱스 즉 0번 인덱스에서부터 시작하길 원한다면, 두 개의 마침표 전의 값을 생략할 수 있다. 다음 두 줄은 동일한 표현이다.

- `let slice = &s[0..2];`
- `let slice = &s[..2];`

비슷한 이치로 `String` 의 마지막 바이트까지 포함한다면 끝 숫자를 생각할 수 있다.

- `let slice = &s[3..s.len()];`
- `let slice = &s[3..];`

둘 다 응용하면 전체 스트링의 슬라이스를 만들기 위해 양쪽 값을 모두 생략할 수 있다.

- `let slice = &s[0..s.len()];`
- `let slice = &s[..];`

“스트링 슬라이스” 를 나타내는 타입은 `&str` 으로 쓴다.

`String` 의 일부를 참조하는 스트링 슬라이스

![trpl04-06.svg](Rust%200d7ead296adb4593aa99b2b8cb2e5307/trpl04-06.svg)

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

자 이제 엉망이 되기 힘든 훨씬 직관적인 API를 갖게 되었다. 이제 컴파일러가 `String` 에 대한 레퍼런스들이 유효한 상태로 남아있게끔 보장해줄 것이다. [이 코드](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21)는 논리적으로 맞지 않지만 이상없이 컴파일되고 실행된다. 이후에 변경된 스트링에 대한 첫번째 단어의 인덱스에 접근하고자 할 경우에나 오류가 발생할 것이다. 스트링 슬라이스는 이러한 버그를 불가능하게 만들고 코드 내에서 발생할 수 있는 문제를 훨씬 일찍 알게 해준다.

---

[위 코드](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21)와 다르게 정상적으로 오류를 발생시킨다.

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

구조체는 [튜플](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21)과 비슷하다. 튜플처럼 구조체의 구성요소들을 각자 다른 타입을 지닐 수 있다. 그러나 튜플과는 다르게 각 구성요소를 명명할 수 있다. 즉 값이 의미하는 바를 명확하게 인지할 수 있다. 바로 이 점이 튜플보다 유연하게 다룰 수 있는 차이점이다. 구조체 내의 특정 인스턴스 값을 지정하거나 데이터의 순서에 의존할 필요가 없다.

```rust
struct User {
    username: String,
    email: String,
    *sign_in_count: u64,*
    active: bool,
}
```

구조체를 정의할 때는 `struct` 키워드를 먼저 입력하고 명명할 구조체의 이름을 입력하면 된다. 구조체의 이름은 함께 묶이게 되는 구성요소들의 의미를 내포할 수 있도록 짓는 것이 좋다. 이름 뒤에 중괄호 안에서는 필드(*field*)라 불리는 각 구성요소들의 타입과 접근할 수 있는 이름을 정의한다.

```rust
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
```

정의한 구조체를 사용하려면, 각 필드의 값을 적은 인스턴스(*instance*)를 생성해야 한다. 구조체의 이름을 명시해서 사용할 수 있고, 필드를 식별할 수 있는 이름인 키와 그 키에 저장하고자 하는 값의 쌍(`key:value`)을 이어지는 중괄호 안에 추가해 생성할 수 있다.

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

변수명과 구조체의 필드명이 같다면, 필드 초기화 축약법(*field init  shorthand*) 을 이용할 수 있다. 이를 활용하면 구조체를 생성하는 함수를 더 간단히 작성할 수 있다. `build_user` 함수에는 `email` 과 `username` 이라는 매개변수가 있다. 함수는 `User` 구조체가 구현된 인스턴스를 반환한다.

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

하지만 구조체 갱신법은 입력으로 주어진 인스턴스와 변화하지 않은 필드들을 명시적으로 할당하지 않기 위해 .. 구문을 사용한다.  아래 코드는 `user1` 인스턴스와 `active`, `sign_in_count` 필드의 값은 같고, `email` 과 `username` 필드들의 값은 다른 `user2` 인스턴스를 생성할 때 구조체 갱신법을 사용한 것을 보여준다.

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

어떠한 필드도 없는 구조체 역시 정의할 수 있다. 이는 유닛 타입인 () 와 비슷하게 동작하고, 그 때문에 유사 유닛 구조체(*unit-like struct*)라 불린다. 유사 유닛 구조체는 특정한 타입의 트레잇(trait)을 구현해야하지만 타입 자체에 데이터를 저장하지 않는 경우에 유용하다. 이 내용들은 이후에 다룬다.

---

### 구조체 데이터의 소유권

이 코드에서의 `User` 구조체 정의에서는 `&str` 문자 슬라이스 타입 대신 `String` 타입을 사용했다. 이는 의도적인 선택으로, 구조체 전체가 유효한 동안 구조체가 그 데이터를 소유하게 하고자 함이다.

구조체가 소유권이 없는 데이터의 reference 를 저장할 수는 있지만 나중에 다룰 라이프타임(*lifetime*)을 전제로 한다. 라이프 타임은 구조체가 존재하는 동안 참조하는 데이터를 계속 존재할 수 있도록 한다. 라이프타임을 사용하지 않고 reference를 저장하고자 하면 에러가 발생한다.

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

`println!` 매크로는 다양한 종류의 포멧을 출력할 수 있으며, 기본적으로 {} 는 `println!` 에게 Display 라고 알려진 포멧팅을 이용하라고 전달해준다. 여태것 봐온 기본 타입들은 Display 가 기본적으로 구현되어 있다. 하지만 구조체를 사용하는 경우, `println!` 가 출력을 형식화하는 방법이 명확하지 않다. 사용자가 쉼표를 이용하길 원할수도 있고, 중괄호로 구분하는것을 원할수도 있기때문에 Rust는 따로 추론을 해서 출력해주지 않는다.

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

메소드(*method*) 는 함수와 유사하다. 똑같이 `fn` 키워드와 이름으로 선언되고, 파라미터와 반환값을 갖고 있다. 또한 어딘가로부터 호출 되었을때 실행될 코드를 담고 있다. 하지만 메소드는 함수와는 달리 구조체, 열거형이나 트레잇 객체안에 정의되고 첫번째 파라미터가 언제나 `self` 인데, 이는 메소드가 호출되고 있는 구조체의 인스턴스를 나타낸다.

### 메소드 정의하기

[이전](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21)에서 `Rectangle` 인스턴스를 파라미터로 가지고 있는 `area` 함수를 바꿔서 `Rectangle` 구조체 위에서 정의된 `area` 메소드를 만들 수 있다.

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

`Rectangle` 의 내용 안에 함수를 정의하기 위해서, `impl` (*구현: implementation*) 블록을 만든다. 그 다음 `area` 함수를 `impl` 중괄호 안으로 옮기고 시그니처 및 본체 내의 모든 곳에 있는 첫번째 파라미터를 `self` 로 변경한다. `area` 함수를 호출하고 여기에 `rect1` 을 인자를 넘기는 대신에, `Rectangle` 인스턴스 상의 `area` 메소드를 호출하기 위해 메소드 문법 (method syntax) 를 이용할 수 있다. 메소드 문법은 점을 추가하고 그 뒤를 이어 메소드 이름, 괄호, 인자들을 적으면 된다.

`area` 의 시그니처 에는 이전에는 `rectangle: &Rectangle`  이 사용되었지만 지금은 `&self` 가 사용되었다. 이는 메소드가 `impl` `Rectangle` 내용물 안에 위치하고 있어서 Rust가 `self` 의 타입이 `Rectangle` 이라는 사실을 알고 있기 때문이다. 하지만 이전에 `&Rectangle` 이라고 썼던 것 처럼, `self` 앞에도 `&` 를 사용할 필요가 있음을 주목해야 한다. 메소드는 `self` 의 소유권을 가질수도, 이것처럼 `self` 를 불변하게 빌릴수도, 혹은 다른 파라미터와 비슷하게 변경이 가능하도록 빌려올 수도 있다.

이 함수에서는 `&Rectangle` 을 이용한 것과 같은 이유로 `&self` 를 선택했다. 소유권을 가져오는걸 원하지 않고, 구조체 내의 데이터를 읽기만 하고, 쓰고 싶지는 않다. 만약 메소드가 동작하는 과정에서 메소드 호출에 사용된 인스턴스가 변경되길 원한다면 첫번째 파라미터로 `&mut self` 를 써야한다. 그냥 `self` 를 첫번째 파라미터로 사용하여 인스턴스의 소유권을 가져오는 메소드를 작성하는 일은 드물다. 이러한 일은 보통 해당 메소드가 `self` 를 다른 무언가로 변형시키고 이 변형 이후에 호출하는 측에서 원본 인스턴스를 사용하는 것을 막고 싶을 때 종종 쓰인다.

함수 대신 메소드를 사용하며 얻을 수 있는 이점은 모든 메소드를 사용할때 `self` 를 타이핑 하지 않아도 된다는 점과 조직화에 관한 점이다. 이 코드를 향후 사용할 사람들이 `Rectangle` 함수를 사용할 수 있는 지점을 찾는 것보다 하나의 `impl` 블록 내에 해당 타입의 인스턴스로 할 수 있는 것을 보는게 더 편리하다.

<aside>
💡 “->“ 연산자는요?

</aside>

C++ 같은 언어에서는, 메소드 호출을 위해서 두 개의 연산자가 사용된다. 만일 어떤 객체의 메소드를 직접 호출하는 중이라면 `.` 를 이용하고 어떤 객체의 포인터에서의 메소드를 호출하는 중이고 이 포인터를 역참조할 필요가 있다면 `->` 를 사용한다. 만일 `object` 가 포인터라면 `object->something()` 은 `(*object).something()` 과 비슷하다. 

하지만 Rust 는 -> 연산자와 같은 연산자를 가지고 있지 않다. 대신 Rust는 자동 참조 및 역참조(*automatic referencing and dereferencing*) 이라는 기능을 가지고 있다. 

예를 들자면 `object.something()` 이라고 메소드를 호출했을 때, Rust는 자동적으로 `&`나 `&mut`, 혹은 `*` 를 붙여서 `object` 가 해당 메소드의 시그니처와 맞도록 한다. 다음은 동일한 표현이다.

```rust
p1.distance(&p2);
(&p1).distance(&p2);
```

첫번째 표현이 훨씬 깔끔해보인다. 이러한 자동 참조 동작은 메소드가 명확한 수신자-즉 `self` 의 타입을 가지고 있기 때문에 동작한다. 수신자와 메소드의 이름이 주어질 때, Rust는 해당 메소드가 읽는지(`&self`) 혹은 변형 시키는지 (`&mut self`), 아니면 소비하는지(`self`)를 결정론적으로 알아낼 수 있다. Rust가 메소드 수신자를 암묵적으로 빌리도록 하는 사실은 실사용 환경에서 소유권을 인간공학적으로 만드는 중요한 부분이다.

---

### 더 많은 파라미터를 가진 메소드

이번에는 `Rectangle` 의 인스턴스가 다른 `Rectangle` 인스턴스를 가져와서 이 두번째 `Rectangle` 의 필드들이  `self` 의 필드들보다 전부 작다면 `True` 를 반환하고, 그렇지 않으면 `false`를 반환하고 싶다. 만약 `can_hold` 메소드를 정의했다면 다음처럼 사용할 수 있다.

```rust
fn main() {
    let rect1 = Rectangle { length: 50, width: 30 };
    let rect2 = Rectangle { length: 40, width: 10 };
    let rect3 = Rectangle { length: 45, width: 60 };

    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
    println!("Can rect1 hold rect3? {}", rect1.can_hold(&rect3));
}
```

예상 결과값은 `true` 와 `false`다.  `rect2` 필드의 모든 값은 `rect1` 필드의 모든 값보다 작기 때문에 `true`를 반환하고 `rect3`은 `rect1`에 비해 가로로 더 넓기 때문에 `false`다.

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

`impl` 블록의 또다른 유용한 기능은 `self` 파라미터를 갖지 않는 함수도 `impl` 내의 정의하는 것이 가능하다. 이를 연관 함수 (*associated functions*) 라고 부르는데, 그 이유는 이 함수가 해당 구조체와 연관되어 있기 때문이다. 이들은 메소드가 아니라 함수인데, 이는 함께 동작할 구조체의 인스턴스를 가지고 있지 않기 때문이다. 우리는 이미 `String::from` 연관 함수를 사용해본 적이 있다.

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

열거(*enumerations*)은 열겨형(*enums*)이라고도 한다. 열거형은 하나의 타입이 가질 수 있는 값들을 열거 함으로써 타입으로 정의할 수 있도록 한다. `Option` 이라는 독특한 열거형은 어떤 값을 가질 수도 있고, 갖지 않을 수도 있다. `match` 를 이용해 열거형의 값에 따라 쉽게 다른 코드를 실행하기 위한 패턴 매칭을 사용할 수 있다. 또한 `if let` 은 코드에서 열거형을 간결하게 다르기 위한 구문이다.

---

## 열거형 정의하기

열거형이 구조체보다 유용하고 적절하게 사용되는 상황이 있다. IP 주소를 다뤄야 하는 경우가 해당된다. 현재 IP 주소에는 두 개의 주요한 표준이 있다. (버전4, 버전6) 프로그램에서 다룰 IP 주소의 경우는 이 두 가지가 전부이다. 이때 모든 가능한 값들을 나열(*enumerate*) 할 수 있으며, 이 경우를 열거라고 한다.

IP 주소는 버전 4나 버전 6중 하나고, 동시에 두 버전이 될 수는 없다. IP 주소의 속성에 따라 열거형 자료 구조가 적절하다. 왜냐하면 열거형의 값은 variants 중 하나만 될 수 있기 때문이다. 버전 4나 버전 6은 근본적으로 IP 주소이기 때문에, 이 둘은 코드에서 모든 종류의 IP 주소에 적용되는 상황을 다룰 때 동일한 타입으로 취급하는 것이 좋다.

`IpAddrKind` 라는 열거형을 정의하면서 포함할 수 있는 IP 주소인 v4 와 v6 를 나열해서 이 개념을 코드에 표현할 수 있다. 이것을 열거형의 *variants* 라고 한다.

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

- `Quit` 은 연관된 데이터가 없다
- `Move` 는 구조체 처럼 name fields 를 가지고 있다.
- `Write` 는 하나의 싱글 `String` 을 포함한다.
- `ChangeColor` 는 세 개의 i32 를 포함한다.

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

프로그래밍 언어 디자인은 어떤 특성들이 포함되어있는지의 관점에서 생각할 수도 있지만, 포함되지 않는 특성들도 역시 중요하다. Rust는 다른 언어들에서 흔하게 볼 수 있는 **`null` 특성이 없다.** `null` 은 값이 없다는 것을 표현하는 하나의 값이다. `null` 을 허용하는 언어에서, 변수는 항상 `null` 상태이거나, `null` 이 아닌 상태가 된다. `null` 값으로 발생하는 문제는, `null` 값을 `null` 이 아닌 값처럼 사용하려고 할 때 여러 종류의 오류가 발생할 수 있다는 점이다. `null` 이나 `null` 이 아닌 속성은 어디에나 있을 수 있고, 너무나도 쉽게 오류를 발생시킬 수 있다.

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

이 에러 메세지는 Rust가 `Option<i8>` 와 `i8` 를 어떻게 더해야 하는지 모른다는 것을 의미한다. **둘은 다른  타입이기 때문이다.** Rust에서 `i8` 과 같은 타입의 값을 가질 때, 컴파일러는 항상 유효한 값을 갖고 있다는 것으로 보장한다. 때문에 값을 사용하기 전에 `null` 인지 확인할 필요도 없이 값을 사용할 수 있다.

하지만 `Option<T>` 을 사용할 경우엔 값이 있을지 없을지에 대한 생각을 할 필요가 있으며 컴파일러는 값을 사용하기 전에 이런 케이스가 처리되었는지 확인 해 줄것이다.

따라서 다음을 계산하려면 `T`에 대한 연산을 수행하기 전에 `Option<T>` 를 `T`로 변환해야 한다. 이렇게 하면 `null` 과 관련된 가장 흔한 이슈를 발견하는데 도움을 준다. `null` 일때 `null` 이 아니라고 가정할 경우처럼 말이다.

`null` 일 수 있는 값을 사용하기 위해서, 명시적으로 값의 타입을 `Option<T>` 로 만들어 줘야 한다. 그 다음에 값을 사용할 때 명시적으로 `null` 인 경우를 처리해야 한다. 이것으로 인해 값의 타입이 `Option<T>` 가 아닌 모든 값이 `null` 이 아니라고 안전하게 가정할 수 있다. 이것은 `null` 을 너무 많이 사용하는 문제를 제한하고, Rust 코드의 안정성을 높이기 위한 의도된 디자인 결정사항이다.

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

`Option<T>`을 사용할 때 `Some` 케이스로부터 내부의 T 값을 얻을 필요가 있었다. `Coin` 열거형을 가지고 했던 것처럼 `match` 를 이용해여 `Option<T>` 를 다룰 수 있다. 동전을 비교하는 대신, `Option<T>`의 variant를 비교할 것이지만 match 표현식이 동작하는 방법은 동일하다.

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

Rust는 우리가 다루지 않는 모든 가능한 경우를 알고 있고, 심지어 우리가 어떤 패턴을 잊어먹었는지도 알고 있다. Rust에서 매치는 하나도 빠뜨리지 않는다(exhaustive) : 코드가 유효해지기 위해서는  모든 마지막 가능성까지 샅샅이 다루어야 한다. 특히 `Option<T>`의 경우, Rust가 우리에게 `None` 케이스를 다루는 것을 까먹는것을 알려줄때 `Null` 일지도 모를 값을 가지고 있음을 가정해 실수하는 일을 방지해준다.

---

### _ 변경자(placeholder)

Rust는 모든 가능한 값을 나열하고 싶지 않을 경우에 사용할 수 있는 패턴을 갖고 있다. 예를 들어, `u8`은 0에서부터 255까지 유효한 값을 가질 수 있따. 마일 우리가 1, 3, 5, 그리고 7 값에 대해서만 신경 쓰고자 한다면, 나머지 0, 2, 4, 6, 8 그리고 9부터 255까지를 모두 나열하고 싶지는 않을 것이다. 이럴때 _ 패턴을 이용할 수 있다.

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

하지만 `match` 표현식은 우리가 단 한가지 경우에 대해 고려하는 상황에서는 장황할 수 있다. 이러한 상황에서는 `if let` 을 사용할 수 있다.

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

- `mod` 키워드는 새로운 모듈을 선언한다. 모듈 내의 코드는 이 선언 바로 뒤에 중광호로 묶여서 따라오거나 다른 파일에 놓일 수 있다.
- 기본적으로, 함수, 타입, 상수, 그리고 모듈은 `private` 다. `pub` 키워드가 어떤 아이템을 `public` 하게 만들어줘서 이것의 네임스페이스 바깥쪽에서 볼 수 있도록 한다.
- `use` 키워드는 모듈이나 모듈 내의 정의들을 스코프 안으로 가져와서 이들을 더 쉽게 참조할 수 있도록 한다.

---

## mod와 파일 시스템

항상 cargo를 이용해 새로운 프로젝트를 만들었지만, 바이너리 크레이드(crate)를 만드는 대신에 라이브러리 크레이트를 만들 것이다. 라이브러리 크레이트란 다른 사람들의 프로젝트에 디펜던시(dependency)로 추가할 수 있는 프로젝트다.

지금부터 몇가지 일반적인 네트워크 기능을 제공하는 라이브러리의 뼈대를 만들 것이다. 여기서는 모듈들과 함수들의 조직화에 집중하고, 함수의 본체에 어떤 코드가 들어가야 하는지는 신경쓰지 않는다. 이 라이브러리를 `commuicator` 라고 부를 것이다. 라이브러리를 만들기 위해서는 `--bin` 대신 `--lib` 옵션을 사용한다

```rust
$ cargo new communicator --lib
$ cd communicator
```

---

cargo 가 *src/main.rs* 대신 *scr/lib.rs* 를 생성했다.

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

*src/main.rs* 파일이 없기 때문에, `cargo run` 커맨드로 cargo가 실행할 것이 없다. 따라, 여기서는 라이브러리 크레이트의 코드를 컴파일하기 위해 `cargo build`를 사용할 것이다.

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

또한 *src/lib.rs* 파일 내에 여러 개의 모듈을 나란히 정의할 수도 있다. 예를 들어 `connect` 라는 이름의 함수를 갖고 있는 `client` 모듈을 정의할 수 있다.

이제 `network::connect` 함수와 `client::connect` 함수를 갖게 되었다. 둘은 완전히 다른 기능을 갖을 수 있고, 서로 다른 모듈에 정의되어 있기 때문에 함수 이름이 서로 부딪힐 일이 없다.

지금은 라이브러리를 만드는 중이기 때문에, 처음 만들어진 파일은 *src/lib.rs* 다. 하지만 모듈을 만드는 것에 관하여 *src/lib.rs* 는 특별할 것이 없다. 라이브러리 크레이트의 *src/lib.rs* 내에 모듈을 만드는 것과 똑같은 방식으로 바이너리 크레이트의 *src/main.rs* 내에도 모듈을 만들 수 있다. 

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

모듈 안에 다른 모듈을 집어넣는 것도 가능한데, 이는 우리의 모듈이 커짐에 따라 관련된 기능이 잘 조직화 되도록 하고 각각의 기능을 잘 나누도록 하는데 유용하다. 코드를 어떻게 조직화 할 것인가에 대한 선택은 코드의 각 부분간의 관계에 대해 어떻게 생각하고 있는지에 따라 달라진다.  예를 들면 왼쪽 코드처럼 `client` 모듈과 `connect` 함수가 `network` 네임스페이스 내에 있다면 라이브러리 사용자가 더 쉽게 이해할 수 있다.

이제 `network::connect` 와 `network::client::connect` 함수를 갖게 되었다. 다시 말하면 `connect` 라는 이름의 두 함수는 서로 다른 네임스페이스에 있으므로 부딪힐 일이 없다.

---

```rust
communicator
 ├── network
 └── client
```

[이 예제](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21) 에서의 조직화 계층 구조.

```rust
communicator
 └── network
     └── client
```

바로 위 예제 에서의 조직화 계층 구조.

더 복잡한 프로젝트는 많은 수의 모듈을 갖고 있을 수 있고, 이들은 지속적인 트래킹을 위해 논리적으로 잘 조직화 해야한다. 프로젝트 내에서 “논리적으로"가 의미하는 것은 우리에게 달려 있는 것이며, 우리와 우리의 라이브러리 사용자들이 프로젝트 도메인에 대해 어떻게 생각하는지에 따라 달라진다.

---

### 모듈을 다른 파일로 옮기기

모듈은 계층적인 구조를 형성하는데, 이는 파일 시스템과 매우 닮아있다. Rust 에서는 프로젝트를 잘게 나누기 위해 여러 개의 파일 상에서 모듈 시스템을 사용할 수 있어, 모든 것들이 *src/lib.rs* 나 *src/main.rs* 안에 존재할 필요는 없다.

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

세 개의 모듈 `client`, `network`, `network::server` 가 모두 *src/lib.rs* 에 정의되어 있다.

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

이제 모듈과 같은 이름의 외부 파일을 만들어야 한다. `client.rs` 파일을  *src/* 디렉토리에 생성한다. 그런뒤 앞에서 제거했던 `client` 모듈 내의 `connect` 함수를 입력한다.

```rust
fn connect() {
}
```

이미 *src/lib.rs* 안에 `client` 모듈을 `mod` 를 이용하여 선언했기 때문에, 이 파일 안에는 `mod` 선언이 필요 없다. 이 파일은 단지 `client` 모듈의 내용물만 제공한다. 만일 `mod client` 를 여기에 적는다면, 이는 `client` 모듈 내에 서브모듈 `client` 를 만들게 된다.

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

Rust는 기본적으로 *src/lib.rs* 만 찾아본다. 만약에 더 많은 파일을 프로젝트에 추가하고 싶다면, *src/lib.rs* 내에서 다른 파일을 찾아보라고 Rust에게 알려줘야 한다. 

이것이 왜 `mod client` 라는 코드가 *src/lib.rs* 내에 정의되야 하고, 그리고 *src/client.rs* 내에는 정의될 수 없는지에 대한 이유다.

---

같은 방법으로 `network` 모듈 또한 새로운 *src/network.rs* 파일로 나눌 수 있다.

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

*src/server.rs* 파일을 만들고 추출해둔 `server` 모듈의 내용물을 입력한다.

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
2. *src/network.rs* 파일을 이 새로운 *network* 디렉토리 안으로 옮기고, 파일 이름을 *src/network/mod.rs* 로 고친다.
3. 서브모듈 파일 *src/server.rs*를 *network* 디렉토리 안으로 옮긴다.

이후 `cargo build` 를 다시 실행하면 이상없이 컴파일 된다. 

모듈 레이아웃은 여전히 아래와 같다. 이는 [이 코드](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21)에서 만든 것과 정확하게 동일하다.

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

[위 과정](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21)에서 에러가 생기는 이유는 *src* 디렉토리 안에 *server.rs* 파일이 있으면, Rust는 `server` 가 `network` 의 서브모듈이라고 인식할 수 없기 때문이다.

예를 들자면 다음과 같은 구조를 보자.

```rust
communicator
 ├── client
 └── network
     └── client
```

이 예제에서는 `client`, `network`, `network::client` 라는 세 개의 모듈이 있다. 모듈을 파일로 추출하기 위해서 앞서 배웠던 단계를 취하면 `client` 모듈을 위한 *src/client.rs* 를 만들게 될 것이다. `network` 모듈을 위해서는 *src/network.rs* 파일을 만들 것이다. 하지만 `network::client` 모듈을 *src/client.rs*로 추출하는 것은 불가능하다. 그 이유는 최상위 층에 `client` 모듈이 이미 있기 때문이. 만일 `client` 와 `network::client` 모듈 둘다 *src/client.rs*파일에 집어 넣는다면 Rust는 이 코드가 `client` 를 위한 것인지, 아니면 `network::client` 를 위한 것인지 알아낼 방법이 없다.

따라서 `network` 모듈의 `network::client` 서브 모듈을 위한 파일을 추출하기 위해서는 *src/network.rs* 파일 대신 `network` 모듈을 위한 디렉토리를 만들 필요가 있다. 그후 `network` 모듈의 내용을 *src/network/mod.rs* 파일로 옮기고, 서브모듈 `network::client`는 *src/network/client.rs* 파일을 갖게 할 수 있다. 이렇게 하면 *src/client.rs* 는 명확하게 `client` 모듈이 소유한 코드고, *src/network/client.rs* 는 `network::client`가 소유한 코드다.

---

### 모듈 파일 시스템의 규칙

파일에 관한 모듈의 규칙을 정리해보자.

- 만일 `foo` 라는 이름의 모듈이 서브모듈을 가지고 있지 않다면, *foo.rs* 라는 이름의 파일 내에 `foo` 에 대한 선언을 집어넣어야 한다.
- 만일 `foo`가 서브모듈을 가지고 있다면, *foo/mod.rs* 라는 이름의 파일에 `foo` 에 대한 선언을 집어넣어야 한다.

이 규칙들은 모두 제귀적으로 적용된다. 만약 `foo` 라는 이름의 모듈이 `bar` 라는 이름의 서브 모듈을 갖고 있고 `bar` 는 서브 모듈이 없다면, *src* 디렉토리 안에는 아래와 같은 파일들이 있어야 한다.

```rust
├── foo
│   ├── bar.rs (contains the declarations in `foo::bar`)
│   └── mod.rs (contains the declarations in `foo`, including `mod bar`)
```

이 모듈들은 부모 모듈의 파일에 `mod` 키워드를 사용하여 선언되어 있어야 한다.

---

## pub으로 가시성(visibility) 제어하기

앞에서 `network` 와 `network::server` 코드를 각각 *src/network/mod.rs* 와 *src/network/server.rs* 파일 안으로 이동시켰다. 그리고 `cargo build` 로 프로젝트를 빌드할 수 있긴 했지만, 사용하지 않고 있는 `client::connect`, `network::connect`, `network::server::connect` 함수에 대한 경고 메세지를 보게 된다.

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

그렇다면 `connect` 라이브러리를 다른 프로젝트에서 사용해보자. 다음 코드를 담은 *src/main.rs* 파일을 만들어서 같은 디렉토리에 라이브러리 크레이트와 나란히 바이너리 크레이트를 만든다.

```rust
extern crate communicator;

fn main() {
    communicator::client::connect();
}
```

`communicator` 라이브러리 크레이트를 가져오기 위해 `extern crate` 명령어를 사용한다. 이제 패키지는 두 개의 크레이트를 담고 있다. cargo는 *src/main.rs* 를 바이너리 크레이트의 루트 파일로 취급하는데, 이 바이너리 크레이트는 *src/lib.rs* 가 루트 파일인 라이브러리 크레이트와는 별개다. 이러한 경우는 꽤 흔한 일이다. 대부분의 기능은 라이브러리 크레이트 안에 구현되어 있고, 바이너리 크레이트는 이 라이브러리 크레이트를 이용한다. 결과적으로, 다른 프로그램 또한 그 라이브러리 크레이트를 이용할 수 있다. 

`communicator` 라이브러리 밖의 크레이트가 안을 참조하는 시점에서,여태 만들어왔던 모든 모듈들은 `communicator` 라는 이름을 갖는 모듈 내에 있다. 크레이트의 최상위 모듈을 루트 모듈 (*root modul*) 이라 부른다.

만약 프로젝트의 서브모듈 내에서 외부 크레이트를 이용하고 있을지라도, `extern carte` 이 루트 모듈에 와 있어야 한다. (*src/main.rs* 혹은 *src/lib.rs*)  그러면 서브모듈 안에서 마치 최상위 모듈의 아이템을 참조하듯 외부 크레이트로부터 아이템들을 참조할 수 있다.

현 시점에서 바이너리 크레이트는 `client` 모듈로부터 `connect` 함수를 호출한다. 하지만 `cargo build` 를 실행하면 경고들 이후에 에러를 표시한다.

```
error: module `client` is private
 --> src/main.rs:4:5
  |
4 |     communicator::client::connect();
  |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

이 에러는 `client` 모듈이 비공개(*private*) 임을 알려주고 있다. Rust의 모든 코드의 기본 상태는 비공개(*private*)다. 즉, 다른 사람은 이 코드를 사용할 수 없다. 만약 프로그램 내에서 *private* 함수를 이용하지 않는다면, 그 프로그램이 그 함수를 이용할 수 있는 유일한 곳이기 때문에, Rust는 그 함수가 사용된 적이 없다며 경고한다.

`client::connect` 함수를 *public* 으로 지정한 뒤에는 바이너리 크레이트 상에서 이 함수를 호출하는 것이 가능해지고, 그 함수가 사용된 적이 없다는 경고 또한 사라질 것이다. 함수를 *public* 으로 표시하는 것은 Rust에게 그 함수가 우리의 프로그램 외부의 코드에 코드에 의해 사용될 것이라고 알려준다. Rust는 이제부터 이론적으로 외부의 사용이 가능해진 이 함수를 “사용되었다" 라고 간주한다. 따라서 어떤 것이 *public* 으로 표시한다면, Rust는 그것이 우리의 프로그램 내에서 미사용한 것에 대한 경고를 멈춘다.

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

하지만 지금은, 다른 함수들이 크레이트의 공개 API의 일부분이 되길 원하고 있고, 이들에게 `pub` 을 표시해서 남은 경고를 제거할 수 있다. *src/network/mod.rs* 를 다음처럼 수정해보자.

```rust
pub fn connect() {
}

mod server;
```

그리고 컴파일 하면, 여전히 에러가 발생한다. `network::connect` 를 `pub` 으로 설정해 줌으로써 함수가 모듈 내에서는 *public*이지만, 이 함수를 포함하는 `network` 모듈이 *private* 이기 때문이다. 따라서 *src/lib.rs* 를 수정해서 `network` 를 *public* 으로만들어야 한다.

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

1. 만일 어떤 아이템이 *public* 라면, 이는 부모 모듈 어디에서든 접근 가능하다.
2. 만일 어떤 아이템이 *private* 라면, 같은 파일 내에 있는 **부모 모듈** 및 **이 부모의 자식** 모듈에서만 접근이 가능하다.

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

### *  를 이용한 모두(glob) 가져오기

네임스페이스 내의 모든 아이템을 가져오기 위해서 `*` 문법을 사용할 수 있다.

`*`  는 *글롭(glob)* 이라고 부르며, 이는 네임스페이스 내에 공개된(*public*) 모든 아이템을 가져온다. 하지만 글롭을 사용할 때는 신중해야 한다.글롭은 매우 편리하지만, 예상한 것보다 더 많은 아이템을 끌어와서 이름간의 충돌의 원인이 될 수도 있다.

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

`cargo new [이름] --lib` 명령어로 라이브러리 크레이트를 만들때, cargo는 기본적으로 `tests` 모듈을 만들어준다.  `communicator` 프로젝트 내에 다음과 같이 있다고 해보자.

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

코드를 조직화하기 위한 몇가지 새로운 기술을 알게 되었다. 관련된 기능들을 함께 묶여주는 이 기술들을 사용해 파일이 너무 길어지지 않게 하고, 해당 라이브러리 사용자들에게  깔끔한 공개 API를 제공할 수 있다.

---

# 일반적인 컬렉션

Rust의 표준라이브러리에는 *Collection*이라 불리는 여러개의 유용한 데이터 구조들이 존재한다. 대부분의 데이터 타입들은 하나의 특정한 값을 나타내지만, *Collection*은 다수의 값을 담을 수 있다. 내장된 배열 (build-in array)와 튜플 타입과는 달리, 이 *Collection*들이 가리키고 있는 데이터들은 힙에 저장된다. 이는 곧 데이터량이 컴파일 타임에 결정되지 않아도 되며 프로그램이 실행될 때 늘어나거나 줄어들 수 있다는 의미다. 각각의 *Collection*은 서로 다른 용량과 비용을 가지고 있고, 현재 상황에 따라 적절한 *Collection*을 선택하는 능력은 필수다. Rust에서 자주 사용되는 세 가지 컬렉션에 대해 다루겠다.

- *벡터(Vector)*는 여러 개의 값을 서로 붙어 있게 저장할 수 있도록 해준다.
- *스트링(String)*은 문자(charactor)의 모음이다. `String` 타입은 이전에 다루었지만 이번에 더 깊이 다룬다.
- *해쉬맵(hash map)*은 어떤 값을 특정한 키와 연관지어 준다. 이는 *맵(map)*이라 불리는 데이터 구조의 특정한 구현 형태다.

---

## 벡터

벡터, `Vec<T>` 를 사용하면 모든 값을 메모리에 나란히 배치하는 단일 데이터 구조에 하나 이상의 값을 저장할 수 있다. 벡터는 같은 타입의 값만을 저장한다.

### 새 벡터 만들기

비어있는 새 백터를 만들기 위해서는, 아래와 같이 `Vec::new` 함수를 호출해 만들 수 있다.

```rust
let v: Vec<i32> = Vec::new();
```

위 코드에서 벡터의 타입을 적었다. 벡터에 어떠한 값도 넣지 않았기 때문에, Rust는 어떤 요소를 저장할지 모른다. 이것이 중요한 점이다. 벡터는 제네릭(*generic*)을 이용해 구현되어있다. 제네릭을 이용하여 사용자만의 타입을 만드는 방법은 이후에 다룬다. 현 시점에서는 `Vec`은 어떠한 종류의 값이라도 저장할 수 있고, 특정한 벡터는 특정한 타입의 값을 저장할 때 꺽쇠 괄호(`<>`)안에 적어야 한다.

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

스트링에 대해 앞부분에서 다뤄 본 적이 있지만 조금더 자세하게 살펴본다. Rust의 스트링은 세 가지 이유로 인해 사람들이 어려워한다. 첫번째 가능한 에러를 꼭 노출하도록 하는 Rust의 특성, 예상보다 더 복잡한 구조의 스트링, 그리고 UTF-8이다. 스트링은 바이트의 집합으로 구현되기 때문에 *collection* 에서 다룬다. 또한 바이트가 텍스트로 해석될 때 유용한 기능을 제공하는 메소드도 있다. 이 섹션에서 생성, 갱신, 값 읽기와 같은 모든 컬렉션 타입이 가지고 있는 `String` 에서의 연산에 대해 다룬다. 또한 `String` 이 다른 *collection*과 어떻게 다른지, 즉 `String`에 대한 인덱싱이 사람과 컴퓨터의 `String` 데이터 해석 방식에 따라 어떻게 복잡해지는지에 대해서도 설명한다.

우선 *String*이라는 용어가 정확히 무엇을 의미하는지 정의해보자. Rust는 코어 언어에는 딱 한가지 스트링 타입만 제공하는데, 이는 바로 스트링 슬라이스인 `str`이고, 이것의 reference형태인 `&str` 또한 많이 다뤘다. 앞서 *스트링 슬라이스*에 대해 다뤘고, 이는 다른 어딘가에 저장된 UTF-8로 인코딩된 스트링 데이터의 reference다. 예를 들어, 스트링 리터럴은 프로그램의 바이너리 출력물 내에 저장되어 있으며, 그러므로 스트링 슬라이스다.

`String` 타입은 코어 언어 기능 내에 구현된 것이 아니고 Rust의 표준 라이브러리를 통해 제공된다, 이는 커질 수 있고, 가변적이며, 소유권을 갖고 있고, UTF-8로 인코딩된 스트링 타입이다. Rust를 사용하는 사람들이 “스트링"에 대해 이야기할 떄 보통 `String`과 스트링 슬라이스 `&str` 타입 둘 모두를 이야기하는 것이지, 이들 중 하나를 뜻한 것이 아니다. 이번엔 대부분 `String`에 대해 다룰 것이지만, 두 타입 모두 Rust의 표준 라이브러리에서 매우 많이 사용되고 `String`과 스트링 슬라이스 모두 UTF-8로 인코딩 되어있다.

Rust의 표준 라이브러리는 `OsString`, `OsStr`, `CString` 그리고 `CStr` 과 같은 몇몇의 스트링 타입도 제공한다. 심지어 어떤 라이브러리 크레이트들은 스트링 데이터를 저장하기 위해 더 많은 옵션을 제공하기도 한다. `*String / *Str` 이라는 작명과 유사하게 종종 소유권이 있는 타입과 이를 빌린 변형타입을 제공하기도 하는데 이는 `String / &str` 과 비슷하다. 이러한 스트링 타입들은, 다른 종류의 인코딩이 된 텍스트를 저장하거나 다른 방식으로 메모리에 저장될 수 있다. 여기서  다른 스트링 타입은 다루지 않겠지만, 각 타입에 대해 어떤 경우에 적합한지에 대해 알고싶다면 해당 API 문서를 확인해야 한다.

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
> 

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

> `+`  연산자나 `format!` 매크로를 이용한 접합
> 

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

첫번째로 `s2`는 `&`를 가지고 있는데, 이는 `add` 함수의 `s` 파라미터 때문에 첫번째 스트링에 두번째 스트링의 *reference*를 더하고 있음을 의미한다. `String`에 `&str`만 더할 수 있고 두 `String`은 더하지 못하는 것이다. 하지만 여기서 `&s2`의 타입은 `&str` 이 아니라 `&String` 이다. 그럼에도 불구하고 이 코드가 작동하는 이유는 `&String` 인자가 `&str` 으로 강제될 수 있기 때문이다. `add` 함수가 호출되면, Rust는 역참조 강제(*derf coercion*)라 불리는 것을 사용하는데, 이는 `add` 함수 내에서 사용되는 `&s` 가 `&s2[..]` 로 바뀌는 것으로 생각할 수 있도록 한다. 역참조 강제 또한 이후에 다룬다. `add`가 파라미터의 소유권을 가져가지는 않으므로, `s2`는 연산 이후에도 유효한 `String`이다. 

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
> 

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
> 

UTF-8 에 대한 또다른 주목해야할 점은 Rust가 문자열을 바라보는 방법에는 바이트, 스칼라 값, 그리고 문자소 클러스터( 우리가 *글자,letter*  라고 부르는 것과 가장 근접한 것) 3가지가 있다는 것이다.

데바가나리 글자로 쓰여진 힌디어 "नमस्ते” 를 보면, 이것은 아래와 같이 `u8` 값들의 `Vec`로서 저장된다.

```rust
[224, 164, 168, 224, 164, 174, 224, 164, 184, 224, 165, 141, 224, 164, 164,
224, 165, 135]
```

이것은  18바이트고 컴퓨터가 이 데이터를 저장하는 방법이다. 만일 이것을 유니코드 스칼라 값, 즉 Rust의 `char` 타입인 형태로 본다면 다음과 같다.

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

해쉬맵, `HashMap<K, V>`  타입은 `K` 타입의 키에 `V` 타입의 값을 매핑한 것을 저장한다. 이는 *해쉬 함수(hashing function)* 을 통해 동작하는데, 해쉬 함수는 이 키와 값을 메모리 어디에 저장할 지 결정한다. 다른 프로그래밍 언어들도 이러한 종류의 데이터 구조를 지원하지만, 종종 해쉬, 맵, 오브젝트, 해쉬 테이블 혹은 연관 배열 등과 같은 몇몇 다른 이름으로 이용된다.

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

`Hash_Map<_, _>` 을 적는 이유는 이는 `collect`가 다른 많은 컬렉션 타입으로 바뀔 수 있고 이것을 무엇으로 변할지 적지 않는다면 어떤것을 원하는지 알  수 없기 때문이다. 그러나 키와 값의 타입을 적지 않은것은 벡터에 담긴 데이터의 타입에 기초하여 타입을 추론할 수 있기 때문에 밑줄을 사용할 수 있다.

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
> 

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
> 

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

위 코드를 실행하면 `{”Yellow”: 50, “Blue”: 10 }` 을 출력한다. 첫번째 `entry` 호출은 옐로우 팀에 대한 키의 값에 `50`을  삽입한다. 이는 옐로우 팀이 값을 가지고 있지 않기 때문이다. 두번째 `entry` 호출은 불루팀은 이미 값 `10`을 가지고 있기때문에 아무일도 일어나지 않는다.

---

> **예전 값을 기반으로 새로운 값 만들기**
> 

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

Rust 에서 에러는 두 가지로 구분한다. *복구 가능한(recoverable)* 에러와 *복구 불가능한(unrecoverable)* 에러다. 복구 가능한 에러는 사용자에게 문제를 보고하고 연산을 재시도 하는것이 보통의 경우인데, 예를 들면 파일을 찾지 못하는 경우다. 복구 불가능한 에러는 언제나 버그의 증상이 나타난다. 예를 들면 배열의 끝을 넘어선 위치의 값에 접근하려고 시도하는 경우다.

대부분의 언어들은 이 두 종류의 에러를 구분하지 않고, 예외처리 (exception)과 같은 메커니즘을 사용해 모든 에러를 처리한다. Rust는 예외 처리 기능이 없다. 대신, 복구 가능한 에러를 위한 `Result<T, E>` 값과 복구 불가능한 에러가 발생했을때 실행을 멈추는 `panic!` 매크로를 가지고 있다.

---

## panic!과 함께하는 복구 불가능한 에러

`panic!` 매크로가 실행되면 프로그램은 실패 메세지를 출력하고, 스택을 되감고 청소한다. 그 후에 종료된다. 이런 일이 발생하는 가장 흔한 상황은 버그가 발견되었고 프로그래머가 이 에러를 어떻게 처리할지 명확하지 않을때이다.

- `panic!`에서 스택을 되감거나 그만두기
    
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

`panic!` 은 마지막 세 줄의 에러 메세지를 출력한다. 첫 번째 줄은 패닉 메세지와 함께 소스 코드에서 패닉이 발생한 지점을 보여준다. *src/main.rs:2* 는 *src/main.rs* 파일의 두 번째 줄을 의미한다.

지금은 해당 줄이 `panic!`을 호출한 지점이지만 다른 지점일 수도있다. 에러 메세지에 적혀있는  파일 이름과 라인 번호는 `panic!` 매크로가 호출된 다른 누군가의 코드일 것이고, `panic!`을 이끌어낸 것이 본인의 코드 라인이 아닐 수도 있다. 문제를 일으킨 코드 부분을 발견하기 위해서 `panic!` 호출이 발생된 함수에 대한 백트레이스(backtrace)를 할 수 있다. 

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
 이러한 상황에서 C와 같은 다른 언어들은 의도치 않은 것일지라도 요청한 그대로 정확히 주려고 시도할 것이다. 벡터 내에 해당 요소와 상응하는 위치의 메모리에 들어 있는 뭔가를 얻을 것이다. 설령 그 메모리 영역이 벡터의 소유가 아닐지라도 상관없다. 이러한 것을 버퍼 *오버리드(buffer overread)* 라고 부르며, 만일 어떤 공격자가 메모리상 벡터 뒤에 존재하는 중요한 내용을 유효하지 않은 인덱싱을 통해 접근한다면 이는 보안 취약점을 발생시킬 수 있다. 

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

위 에러에서 `*libcollecetions/vec.rs*`를 가리키고 있다. 이는 표준 라이브러리 속 `Vec<T>`의 구현 부분이다. 벡터 `v`에 `[]`를 사용할 때 실행되는 코드는 `*libcollections/vec.rs*` 안에 있고, 이곳이 바로 `panic!`이 실제 발생한 곳이다.

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

## Result와 함께하는 복구 가능한 에러

대부분의 에러는 프로그램이 전부 멈출 정도로 심각하지 않다. 우리는 종종 어떤 함수가 실패할 때, 그 결과를 쉽게 해석하고 대응할 수 있다. 예를 들면, 만일 우리가 어떤 파일을 열고자 하는데 파일이 존재하지 않아서 실패했다면, 프로세스를 멈추는 대신 파일을 새로 만들 수도 있는것처럼.

`Result` 열거형은 다음과 같이 `Ok`와 `Err`라는 두 개의 값을 갖도록 정의되어 있음을 생각하자. 

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

`T`와 `E`는 제네릭 타입 파라미터다. `T`는 성공한 경우에 `Ok` 내에 반환될 값의 타입을 나타내고, `E`는 실패한 경우에 `Err` 내에 반환될 에러의 타입을 나타낸다. `Result`가 이러한 제네릭 타입 파라미터를 갖기 때문에, 반환하고자 하는 성공적인 값과 에러 값이 다를 수 있는 다양한 상황 내에서 표준 라이브러리에 정의된 `Result` 타입과 함수들을 사용할 수 있다.

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");
}
```

`File::open`이 `Result`를 반환하는지 아는 방법은 표준 라이브러리API문서를 찾아보거나, 컴파일러에게 물어볼 수 있다. 만일 `f`에게 그 함수의 반환 타입이 명확하게 아닌 어떤 타입에 대한 타입 명시를 주고 컴파일을 시도한다면, 컴파일러는 우리에게 타입이 맞지 않는다고 알려준다. 그 후 에러 메세지는 `f`의 타입이 무엇인지 알려준다. File::open의 반환 타입이 명확하게 u32는 아닐 것을 알고 있으니, 다음 문장으로 바꾸어 보자.

`let f: u32 = File::open(”hello.txt”);`

컴파일 시도하면 다음 메세지가 나타난다.

---

```
error[E0308]: mismatched types
 --> src/main.rs:4:18
  |
4 |     let f: u32 = File::open("hello.txt");
  |                  ^^^^^^^^^^^^^^^^^^^^^^^ expected u32, found enum
`std::result::Result`
  |
  = note: expected type `u32`
  = note:    found type `std::result::Result<std::fs::File, std::io::Error>`
```

여기서 `File::open` 함수의 반환 타입이 `Result<T, E>` 라는 것을 알려준다. 여기서 제네릭 파라미터 `T`는 성공값의 타입인 `std::fs:File`로 채워져 있는데, 이것은 파일 핸들러다. 에러에 사용되는  `E`타입은 `std::io::Error`다.

예를 들면 파일이 존재하지 않거나, 파일에 접근할 권한이 없을지도 모른다. 그렇기 때문에 해당 함수가 성공하거나 실패했을때 둘 중 하나의 정보를 `Result` 열거형으로 전달하게 되는 것이다.  

만약 함수가 성공했을 경우, 변수 `f`는 파일 핸들을 담고 있는 `Ok` 인스턴스가 될 것이다. 반대로 실패할 경우, `f`의 값은 발생한 에러의 종류의 정보를 가지고 있는 `Err`의 인스턴스가 될 것이다. 그렇기 때문에 `File::open`이 반환하는 값에 따라 다른 행동을 취하는 코드를 추가해야 한다. 대표적으로 `match` 표현식을 이용해 `Result`를 처리하는 방법이 있다.

여기서 Rust에게 결과가 `Ok`일 때는 `Ok`로부터 내부의 `file`을 변수 `f`에 대입한다. 반대로 `Err`를 얻는 경우에 이 예제에서는 `panic!` 매크로를 호출하는 방법을 택했다. 

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => {
            panic!("There was a problem opening the file: {:?}", error)
        },
    };
}
```

---

### 서로 다른 에러에 대해 매칭하기

위 예제는 `File::open`이 실패한 이유가 무엇이든 간에 `panic!`을 일으킬 것이다. 이번에는 실패한 이유에 따라 다른 행동을 취하고 싶다고 가정하자. 만약 파일이 없어 실패한 것이라면, 새로운 파일을 만들어서 파일 핸들을 반환하고, 그 밖의 이유로 실패한 것이라면, 이전과 같이 `panic!`을 일으키고 싶다고 가정하자. 

`Err` 내에 있는 `File::open`이 반환하는 값의 타입은 `io::Error`인데, 이는 표준 라이브러리에서 제공하는 구조체다. 이 구조체는 kind 메서드를 제공하는데, 이 메서드를 사용해서 `io::ErrorKind`값을 얻을 수 있다. `io::ErrorKind`는 `io` 연산으로부터 발생할 수 있는 여러 종류의 에러를 표현할 수 있는 열거형이다. 이중에서 `ErrorKind::NotFound` 가 우리가 원하던 파일이 존재하지 않았을때 반환하는 값이다. 이를 조건문에 이용해서 우리가 원하는 바를 이룰 수 있다.

조건문 `if error.kine() == ErrorKind::NotFound` 는 매치 가드*(match guard)* 라고 한다. `match` 줄기 상에서 패턴을 더 정제해주는 추가 조건문이다. 그 줄기의 코드가 실행되기 위해서는 이 조건문이 참이어야 한다. 이때 패턴에는 `ref` 가 필요하고, 이를 적어줌으로서 가드 조건문은  `error`변수의 소유권을 갖지 않고 그거 참조만 하게 된다. 이때 `&`가 아니라 `ref`를 사용하는 이유는 이후에 다른다. 짧게 설명하면, `&`는 참조자를 매치하고, 그 값을 제공하지만 `ref`는 값을 매치하고 그 참조자를 제공한다.

매치 가드 내에서 확인하고 하는 조건은, `error.kine()` 에 의해 반환된 값이 `ErrorKind` 열거형의 `NotFound` 인지 확인하는 것이다. `File::open` 함수가 실패하는 수 있는 수많은 이유중에 파일이 존재하지 않아서 실패한 것인지 확인한다. 만약 그렇다면 `File::create`로 파일을 생성을 시도한다. 이때 `File:create` 또한 실패할 수 있기 때문에 안쪽에 `match` 구문을 바깥쪽과 마찬가지로 적을 필요가 있다. 

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(ref error) if error.kind() == ErrorKind::NotFound => {
            match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => {
                    panic!(
                        "Tried to create file but there was a problem: {:?}",
                        e
                    )
                },
            }
        },
        Err(error) => {
            panic!(
                "There was a problem opening the file: {:?}",
                error
            )
        },
    };
}
```

---

### 에러가 났을 때 패닉을 위한 숏컷: unwrap과 expect

`match`문은 잘 동작하지만, 살짝 장황하기도 하고 의도를 항상 잘 전달하는 것 또한 아니다. `Result<T, E>` 타입은 다양한 작업을 하기 위해 정의된 수많은 헬퍼 메소드를 가지고 있다. 그 중 하나인 `unwrap` 메서드는 `match`구문과 비슷한 구현을 한 숏컷 메소드다. 만일 `Result`값이 `Ok`라면, `unwrap`은 `Ok` 내의 값을 반환한다. 만약 `Err`라면 `unwrap`은 `panic!` 메크로를 호출할 것이다. 

`*hello.txt*` 파일이 없는 상태에서 이 코드를 실행시키면, `unwrap` 메소드에 의한 `panic!` 호출로부터 에러 메세지를 볼 수 있다.

```
thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: Error {
repr: Os { code: 2, message: "No such file or directory" } }',
/stable-dist-rustc/build/src/libcore/result.rs:868
```

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").unwrap();
}
```

---

또 다른 메소드인 `expect`는 `unwrap`과 유사한데, 프로그래머에게 `panic!` 에러 메세지를 선택할 수 있게 한다. `unwrap` 대신 `expect`를 이용하고 좋은 에러 메세지를 제공하는 것은 프로그래머의 의도를 전달해주고 패닉의 근원을 추적하는 걸 쉽게 해 줄 수 있다. 

`expect`는 `unwrap`과 동일하게 `Ok`내의 값을 리턴하거나, `panic!` 매크로를 호출한다. `expect`가 `panic!`을 호출할 때 사용하는 에러 메세지는 `expect`에 넘기는 파라미터로 설정될 것이다.

에러 메세지는 파라미터로 넘긴 문장 `“Failed to open hello.txt`”로 시작하기 때문에, 이 에러 메세지가 어디서 부터 왔는지 찾기 쉬워진다. 여러군데 `unwrap`을 사용하면 정확하게 어떤 `unwrap`이 패닉을 일으켰는지 찾기 어려운 것과 반대된다.

```rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").expect("Failed to open hello.txt");
}
```

```
thread 'main' panicked at '**Failed to open hello.txt**: Error { repr: Os { code:
2, message: "No such file or directory" } }',
/stable-dist-rustc/build/src/libcore/result.rs:868
```

---

### 에러 전파하기

실패할지도 모르는 무언가를 호출하는 함수를 작성할 때, 함수 내에서 에러를 처리하는 대신, 해당 함수를 호출한 코드 쪽으로 에러를 반환해서 그쪽에서 처리하도록 할 수 있다. 이는 에러가 어떻게 처리해야 좋을지 좌우해야 할 상황에서, 코드 내용 내에서 이용 가능한 것들보다 더 많은 정보와 로직을 가지고 있을 수도 있는 호출하는 코드 쪽에 더 많은 제어권을 준다.

다음 코드는 파일로부터 사용자 이름을 읽는 함수를 작성한 것이다. 만일 파일이 존재하지 않거나, 읽을 수 없다면 함수는 호출하는 코드 쪽으로 해당 에러를 반환할 것이다.

해당 함수의 반환타입은 `Result<String, io::Error>`다. 이는 함수가 `Result<T, E>` 타입의 값을 반환하고, 이때  `T`는 구체적 타입인 `String`으로 체워져 있고, `E`는 구체적 타입인 `io::Error`로 채워져 있다. 만약 이 함수가 어떤 문제 없이 성공하면, 함수를 호출한 쪽은 `Ok(String)`값을 받을 것이다. 

하지만 실패한다면, 함수를 호출한 코드는 `io::Error`의 인스턴스를 담은 `Err` 값을 받을 것이다. 이때 함수내에서 실패 가능한 연산인 두 가지 메소드 `File::open`, `read_to_string` 모두 `io::Error` 타입의 에러 값을 반환하기 때문에 전체 함수의 반환하는 에러 타입이 `io::Error`다.

이 코드를 호출하는 코드는 사용자의 이름을 담은 `Ok`값 혹은 `io::Error`를 담은 `Err`값을 얻는 처리를 하게 된다. 호출하는 코드가 이 값을 가지고 어떤 일을 할 것인지 알 필요가 없다. 예를 들면 똑같이 `panic`을 발생해 프로그램을 종료 시킬수도 있고, 기본 사용자이름을 만들어서 프로그램을 이어갈 수도 있다. 

호출하는 코드가 정확히 어떤 것을 시도하려 하는지에 대한 충분한 정보가 없기 때문에 모든 성공 혹은 에러 정보를 위로 전파하여 호출하는 코드가 적절하게 처리하도록 하자. 

```rust
use std::io;
use std::io::Read;
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
    let f = File::open("hello.txt");

    let mut f = match f {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut s = String::new();

    match f.read_to_string(&mut s) {
        Ok(_) => Ok(s),
        Err(e) => Err(e),
    }
}
```

---

### 에러를 전파하기 위한 숏컷: `?`

다음은 [위 예제](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21)와 동일한 기능을 가진 `read_username_from_file`의 구현을 보여준다. 

`Result` 값 뒤의 `?`는 위에서 다른 `match` 표현식과 거의 같은 방식으로 동작하게끔 정의되어 있다. 만일 `Result`의 값이 `Ok`라면 `Ok`내의 값이 얻어지고 Err라면 `return` 키워드를 사용하여 호출하는 코드에게 에러값을 전파하는 것처럼 `Err`값이 반환된다.

`match` 표현식과 `?` 연산자의 한가지 차이점은 `?` 연산자를 사용할 때 에러 값들이 표준 라이브러리 내에 있는 `From` 트레잇에 정의된 `from` 함수를 통과하며, 한 유형의 값을 다른 유형으로 변환하는 데 사용된다. 많은 에러 타입들이 어떤 타입의 에러를 다른 타입의 에러로 변환하기 위해 `from`함수를 구현했다. `?` 연산자가 사용되면, `from` 함수의 호출이 `?` 연산자가 얻게 되는 에러 타입을 현재 함수의 반환 타입에 정의된 에러 타입으로 변환한다. 

```rust
use std::io;
use std::io::Read;
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut f = File::open("hello.txt")?;
    let mut s = String::new();
    f.read_to_string(&mut s)?;
    Ok(s)
}
```

---

위 예제에서는 발생할 수 있는 연산 두 개 모두의 에러타입이 동일했기 때문에 해당 함수의 에러 반환값의 타입을 `io::Error`라고 지정할 수 있었지만, 만약 함수 내에서 발생할 수 있는 에러 타입이 여러개라면 `?` 연산을 통해 현재 함수의 반환 타입에 정의된 에러 타입 변환할 때 유용하다. 각각의 에러 타입이 그 자신을 반환되는 에러 타입으로 변경할 방법을 정의하기 위해 `from` 함수를 정의하기만 한다면, `?` 연산자는 자동적으로 사용한다. 

`?` 연산자는 많은 수의 보일러플레이트(*boilerplate*)를 제거해주고 함수의 구현을 더 단순하게 만들어 준다. 심지어는 `?`뒤에 바로 메소드 호출을 연결하는 식으로 코드를 더 줄일 수 있다.

```rust
use std::io;
use std::io::Read;
use std::fs::File;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut s = String::new();

    File::open("hello.txt")?.read_to_string(&mut s)?;

    Ok(s)
}
```

---

### ? 연산자는 Result를 반환하는 함수에서만 사용될 수 있다.

`?` 연산자 `Result<T, E>` 에서 `Ok(T)`라면 내부의 값 `T`의 표현식으로 종료되지만 만약 `Err(E)`라면 `Err(E)`를 **리턴하고 함수를 종료** 하기 때문에 함수의 반환 타입은 반드시 `Err(E)`와 호환 가능한 `Result`가 되어야 한다. 

`main`의 반환 타입이 `()`라는 것을 기억하고, 만약 `main` 함수 내에서 ?를 사용하면 어떤일이 생길지 생각해보자.

```rust
use std::fs::File;

fn main() {
	let f = File::open("hello.txt")?;
}
```

컴파일하면 당연히 에러가 나온다.

```
error[E0277]: the `?` operator can only be used in a function that returns
`Result` (or another type that implements `std::ops::Try`)
 --> src/main.rs:4:13
  |
4 |     let f = File::open("hello.txt")?;
  |             ------------------------
  |             |
  |             cannot use the `?` operator in a function that returns `()`
  |             in this macro invocation
  = help: the trait `std::ops::Try` is not implemented for `()`
  = note: required by `std::ops::Try::from_error`
```

 에러 메세지는 오직 `Result`를 반환하는 함수에서만 `?` 연산자를 사용할 수 있다고 알려준다. `Result`를 반환하지 않는 함수 내에서, `Result`를 반환하는 다른 함수를 호출했을 때는 에러를 전파하는 대신 위에서 배운 `match`, `unwrap`, `expect`등 Rust와 `Result`에서 제공하는 메소드들 사용해 함수 내에서 에러를 처리할 필요가 있다.

---

## panic!이냐, panic!이 아니냐, 그것이 문제로다!

그렇다면 언제 `panic!`을 써야 하고 언제 `Result`를 반환해야할까? 코드가 패닉을 일으킬 때는 복구할 방법이 없다. 복구 가능한 방법이 있든 아니든 에러 상황에 대해 `panic!`을 호출할 수 있지만, `panic!`을 호출한다는 것은 현 상황은 복구 불가능한 것이라고 결정을 내리는 것이다. 만약 `Result` 값을 반환한다면, 호출하는 코드에게 결단을 내려주기보다는 옵션을 제공하는 것이다. 그들이 그들의 상황에 적합한 방식으로 복구를 시도할 수도 있고, 혹은 현재 상황의 Err은 복구 불가능하다고 판단해 `panic!`을 호출해서 프로그래머가 만든 복구 가능한 에러를 복구 불가능한 것으로 바꿔놓을 수도 있다. 그러므로, 프로그래머는 실패할지도 모르는 함수를 정의할 때는 `Result`를 반환하는 것이 기본적으로 좋은 선택이다.

다만 몇 가지 상황에서는 `Result`를 반환하는 대신 패닉을 일으키는 코드를 작성하는 것이 더 적합할수도 있지만, 그런 상황은 드물다. 그런 드문 경우를 소개하자면 예제, 프로토타입 코드 및 테스트의 경우를 살펴보자. 그 다음 사람으로서 실패할 리 없는 메소드라는 것을 알 수 있지만 컴파일러는 이유를 파악할 수 없는 경우도 살펴보자. 마지막으로, 라이브러리 코드에 `panic!`을 추가해야 할지 말지를 어떻게 결정할까에 대한 일반적인 가이드라인을 내려보자.

---

### 예제, 프로토타입 코드, 그리고 테스트는 패닉을 일으켜도 완전 괜찮은 곳이다.

프로그래머가 어떤 개념을 그려내기 위한 예제를 작성 중이라고 가정하자. 이 예제에 강건한 에러 처리 코드를 예제 안에 넣는 것은 예제를 명확하게 못하게 한다. 예제 코드 내에서 는 `panic!`을 일으킬 수 있는 `unwrap` 같은 메소드를 호출하는 것이 어플리케이션이 에러를 처리하고자 하는 방법에 대한 플레이스홀더로서 의미를 갖는다. 이는 프로그래머의 코드의 나머지 부분이 어떤 것을 하는지 에 따라 달라질 수 있다.

비슷한 상황에서, 프로그래머가 에러를 어떻게 처리할지 결정할 준비가 되기 전에는, `unwrap`과 `expect` 메소드가 프로토타이핑을 할 때 매우 편리하다. 이 함수들은 코드를 더 강건하게 만들 준비가 되었을 때를 위한 명확한 표시다.

만일 테스트 내에서 메소드 호출이 실패한다면, 해당 메소드가 테스트 중인 기능이 아니더라도 전체 테스트가 실패하는게 좋다. `panic!`이 테스트를 실패시키는 방법이기 때문에, `unwrap` 이나 `expect`를 호출하는 것이 좋다.

---

### 컴파일러보다 프로그래머가 더 많은 정보를 가지고 있을 때

프로그래머는 `Result`가 Ok값을 가지고 있을 거라 확신할 생각을 가지고 있지만, 그 논리가 컴파일러는 이해할 수 있는 것이 아니라면, `unwrap`을 호출하는 것이 적절할 수 있다. 오른쪽 예제를 보자.

예제에서는 하드코딩된 `127.0.0.1`를 파싱해 `IpAddr` 인스턴스를 만든다. 이때 우리는 `127.0.0.1`이 유효한 IP주소임을 알 수 있고, 이는 `Ok`값을 가질것이라고 확신할 수 있다. 따라서 여기서 `unwrap`을 사용하는 것은 적절하다. 그러나 이는 어디까지나 코드를 작성하는 프로그래머가 확신할 수 있다는 것이며, 컴파일러는 이를 확신할 수 없기 때문에 `parse` 메소드의 반환 타입은 당연히 `Result`타입을 반환한다.

만약 `IP` 주소 스트링이 예제처럼 하드코딩 된 것이아니라, 사용자에게 입력되었다면, 즉 실패할 가능성이 생겼다면  프로그래머는 `Result`를 처리할 필요가 생긴다.

```rust
use std::net::IpAddr;

let home = "127.0.0.1".parse::<IpAddr>().unwrap();
```

---

### 에러 처리를 위한 가이드라인

만약 코드가 결국 나쁜 상태에 처하게 될 가능성이 있다면 코드에 panic!을 넣는 것이 바람직하다. 여기서 **나쁜 상태**란 **가정, 보장, 계약 혹은 불변성이 깨질 때**를 의미한다. 이를테면 유효하지 않는 값이나 모순되는 값, 혹은 찾을 수 없는 값이 코드를 통과할 경우를 말한다.  - 다음 상황 중 하나 또는 그 이상의 경우

- 사용자가 잘못된 형식으로 데이터를 입력하는 것과 같이 가끔 발생할 가능성이 있는 것과는 반대로 예상치 못한 것이다.
- 그 시점 이후의 코드는 이 **나쁜 상태**에 있지 않아야 한다.
- 프로그래머가 사용하고 있는 타입 내에 이 정보를 집어 넣을만한 뾰족한 수가 없다.

만약 어떤 사람이 우리의 코드를 호출하고 타당하지 않은 값을 집어 넣었다면, `panic!`을 써서 우리의 라이브러리를 사용하고 있는 사람에게 개발하는 동안 이를 고칠 수 있도록 하는것이 최선책일 수도 있다. 비슷한 식으로, 우리가 제어권을 벗어난 외부의 코드를 사용하고 있고, 우리가 이것을 고칠 방법이 없는 유효하지 않은 상태를 반환한다면 `panic!`이 종종 적합하다.

**나쁜 상태**에 도달했지만, 일어날 것으로 예상되는 일이라면, `panic!`을 호출하는 것보다 `Result`를 반환하는 것이 더 적합하다. 예시로 기형적인 데이터가 주어지는 parser나 HTTP 요청 중 속도 제한에 달했음을 나타내는 상태가  포함될 수 있다. 이러한 경우는 해당 함수의 호출자가 그 문제를 어떻게 처리할지를 결정할 수 있도록 하기 위해 `Result`를 반환하는 방식으로 실패할 수도 있다는 사실을 알려줘야 한다.

---

만약 우리의 코드가 어떤 값에 대해 연산을 수행할 때, 코드는 해당 값이 유효한지를 먼저 검사하고, 만일 그렇지 않다면 `panic!`을 호출해야 한다. 유효하지 않은 데이터 상에서 어떤 연산을 시도하는 것은 취약점에 노출시킬 수 있다. → 이는 현재의 데이터 구조가 잘못된 메모리에 접근 시도하는 것은 흔한 보안 문제다.

함수는 종종 ***계약(contracts)***을 갖고 있다. 예를 들면 입력이 특정 요구사항을 만족시킬 경우에만 함수가 작동함을 보장한다. 이 계약을 위반했을 때 `panic`에 빠지는 것은 타당하다. 그 이유는

1.  계약 위반이 언제나 이 함수를 호출한 호출자 쪽의 버그임을 나타낸다.

2.  이 위반 사항은 호출하는 쪽이 처리할 수 있는 종류의 버그가 아니다.

따라서 해당 함수를 호출한 프로그래머는 코드를 고칠 필요가 있다. 그렇기 때문에 함수에 대한 계약은, 특히 계약 위반이 `panic`의 원인이 될 때는, 그 함수에 대한 API문서에 설명되어야 한다.

---

### 유효성을 위한 커스텀 타입 생성하기

Rust의 타입 시스템을 이용해 값을 보장하는 아이디어에서 한 발 더 나가서, 유효성을 위한 커스텀 타입을 생성해보자.

만약 코드에서 1부터 100 사이의 숫자를 입력 받는다고 가정하자. 이는 변수에 음수나, 1부터 100사이가 아닌 다른 숫자를 저장하지 않겠다고 보장하는 것이다.

다음 예제를 보자.
if 표현식은 변수 값이 1부터 100사이인지 검사하며 그렇지 않다면 사용자에게 문제점을 말해주고 다시 입력받는다. 만약 유효한 값이라면  `break`를 통해 이후의 코드를 진행한다. 

하지만 예제는 이상적인 해결책이 아니다. 만약 프로그램이 오직 1 부터 100 사이의 값에서 동작하고 많은 함수가 이러한 요구사항을 가지고 있다면, 모든 함수에 내에서 검사하는것은 잠재적으로 성능에 영향을 줄 것이다.

```rust
loop {
    // snip

    let guess: i32 = match guess.trim().parse() {
        Ok(num) => num,
        Err(_) => continue,
    };

    if guess < 1 || guess > 100 {
        println!("The secret number will be between 1 and 100.");
        continue;
    }
		break;
}

match guess.cmp(&secret_number) {
// snip
```

---

```rust
pub struct Guess {
    value: u32,
}

impl Guess {
    pub fn new(value: u32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Guess value must be between 1 and 100, got {}.", value);
        }

        Guess {
            value
        }
    }

    pub fn value(&self) -> u32 {
        self.value
    }
}
```

대신, 새로운 타입을 만들어서 대처할 수 있다. 새로운 타입을 만들 때, 그 타입의 인스턴스를 생성하는 함수(new) 내에서 프로그램에 필요한 유효성 확인을 할 수 있다. 

---

# 제네릭 타입, 트레잇, 그리고 라이프 타입

제네릭은 구체화된 타입이나 다른 속성들에 대한 추상화된 대리인이다. 이미 `Option<T>`, `Vec<T>`, `Result<T, E>`에서 이미 제네릭을 사용해 보았다.

## 제네릭 데이터 타입

함수 시그니처나 구조체에서와 동일하게, 일반적으로 타입을 쓰는 곳에 제네릭을 이용하는 것은 여러 다른 종류의 구체적인 데이터 타입에 대해서도 동일하게 사용할 수 있도록 해준다. 제네릭을 이용해서 함수, 구조체, 열거형 그리고 메소드를 정의하는 방법을 살펴본다.

---

### 함수 정의 내에서 제네릭 데이터 타입을 이용하기

우리는 함수의 시그니처 내에서 파라미터의 데이터 타입과 반환 값이 올 자리에 제네릭을 사용하는 함수를 정의할 수 있다. 이러한 방식으로 작성된 코드는 더 유연하고 함수를 호출하는 쪽에서 더 많은 기능을 제공할 수 있다. 

다음 두 `largest` 함수는 슬라이스 내에서 가장 큰 값을 찾는 동일한 기능을 한다.

```rust
fn largest_i32(list: &[i32]) -> i32 {
    let mut largest = list[0];

    for &item in list.iter() {
        if item > largest {
            largest = item;
        }
    }

    largest
}
```

```rust
fn largest_char(list: &[char]) -> char {
    let mut largest = list[0];

    for &item in list.iter() {
        if item > largest {
            largest = item;
        }
    }

    largest
}
```

여기서 두 함수는 정확하게 동일한 내용을 가지고 있고, 코드 중복을 제네릭을 이용해 제거하면 좋을 것이다. 우리가 정의하고자 하는 함수의 시그니처 내에 있는 타입들을 파라미터화 하기 위해서, 타입 파라미터를 위한 이름을 만들 필요가 있다. 어떤 식별자든지 파라미터의 이름으로 사용될 수 있지만, Rust가 Camel-case를 사용하기 때문에 `T`를 사용하려 한다. 제네릭 타입 파라미터의 이름은 관례상 짧은 경향이 있는데, 보통은 그냥 한 글자로 되어있다. 

함수에 파라미터를 사용할 때는, 시그니처 내에 그 파라미터를 선언해 파라미터가 코드 내에서 무엇을 의미하는지 알 수 있도록 한다. 이와 동일하게 함수 시그니처 내에서 타입 파라미터 이름을 사용할 때, 사용전에 그 타입 파라미터 이름을을 선언해야 한다. 타입 선언은 함수의 이름과 파라미터 리스트 사이에 꺾쇠괄호(<>)를 쓰고 그 안에 넣는다.

`fn largest<T>(list: &[T]) → T {`

위 함수 시그니처는 어떤 타입 `T`를 이용한 제네릭이다. `list`라는 이름을 가진 하나의 파라미터를 가지고 있고, 이 `list`의 타입은 `T` 타입 값들의 슬라이스다. 또한 위 함수는 타입 `T`값을 반환할 것이다. 이를 통해 위 두 함수를 하나의 함수로 압축하여 사용할 수 있다. 

```rust
fn largest<T>(list: &[T]) -> T {
    let mut largest = list[0];

    for &item in list.iter() {
        if item > largest {
            largest = item;
        }
    }

    largest
}

fn main() {
    let numbers = vec![34, 50, 25, 100, 65];

    let result = largest(&numbers);
    println!("The largest number is {}", result);

    let chars = vec!['y', 'm', 'a', 'q'];

    let result = largest(&chars);
    println!("The largest char is {}", result);
}
```

하지만 왼쪽 코드는 아직 컴파일 되지 않는다. 

```
error[E0369]: binary operation `>` cannot be applied to type `T`
  |
5 |         if item > largest {
  |            ^^^^
  |
note: an implementation of `std::cmp::PartialOrd` might be missing for `T`
```

위 에러는 `std::cmp::PartialOrd`를 언급하는데, 이는 *트레잇(trait)*이다. 트레잇에 대해서 간략하게 설명하자면, 이 에러가 말하고 있는 것은 `T`가 될 수 있는 모든 가능한 타입에 대해서 동작하지 않으리라는 것이다. 

함수 본체 내에서 `T`타입의 값을 비교하고자 하기 때문에 이 비교 연산이 가능한 타입만 사용할 수 있다는 것이다. 표준 라이브러리는 어떤 타입에 대해 비교 연산이 가능하도록 구현할 수 있는 트레잇인 `std::cmp::PartialOrd`를 정의해 두었다. 이후에 제네릭 타입이 특정 트레잇을 갖도록 명시하는 방법을 배울테니 그 전까지 이 예제를 잠깐 치워두자.

---

### 구조체 정의 내에서 제네릭 데이터 타입 사용하기

구조체 내에서도 제네릭 타입 파라미터를 사용하여 구조체를 정의할 수있다

```rust
struct Point<T> {
    x: T,
    y: T,
}

fn main() {
    let integer = Point { x: 5, y: 10 };
    let float = Point { x: 1.0, y: 4.0 };
}
```

문법은 함수 정의 내에서 제네릭을 사용하는 것과 유사하다. 구조체 이름 바로 뒤에 꺾쇠괄호를 쓰고 그 안에 타입 파라미터의 이름을 선언한다. 이후 구조체 정의부 내에서 구체적인 데이터 타입을 명시하는 곳에 제네릭 타입을 사용할 수 있다. 

예시 `Point`의 정의 내에서 단 하나의 제네릭 타입을 사용하기 때문에, `x`와 `y`가 동일한 타입을 가지고 있다. 만약 다음과 같이 `Point` 인스턴스를 만들고자 한다면 컴파일 되지 않을 것이다.

```rust
fn main() {
    let wont_work = Point { x: 5, y: 4.0 };
}
```

---

```
error[E0308]: mismatched types
 -->
  |
7 |     let wont_work = Point { x: 5, y: 4.0 };
  |                                      ^^^ expected integral variable, found
  floating-point variable
  |
  = note: expected type `{integer}`
  = note:    found type `{float}`
```

`x`에 정수 `5`를 대입할 때, 컴파일러는 이 `Point`의 인스턴스에 대해 제네릭 타입 `T`가 정수일 것이라고 알게 된다. 그 다음 `y`에 `4.0`을 지정 했는데, 이 `y`는 `x`와 동일한 타입을 갖도록 정의 되었으므로, 타입 불일치 에러를 받게 되는 것이다. 만약 `x`와 `y`가 서로 다른 타입을 가지는 제네릭 `Point` 구조체를 정의하고 싶다면, 여러 개의 제네릭 타입 파라미터를 사용 하면된다.

다음은 Point의 정의를 T와 U를 이용한 제네릭이 되도록 변경했다. 필드 x의 타입은 T이고, y의 타입은 U다.

```rust
struct Point<T, U> {
    x: T,
    y: U,
}

fn main() {
    let both_integer = Point { x: 5, y: 10 };
    let both_float = Point { x: 1.0, y: 4.0 };
    let integer_and_float = Point { x: 5, y: 4.0 };
}
```

정의 부분에 원하는 만큼 많은 수의 제네릭 타입 파라미터를 사용할 수 있지만, 몇몇 개보다 더 많이 사용하는 것은 코드를 읽고 이해하기 어렵게 만든다. 많은 수의 제네릭 타입을 필요로 하는 지점에 다다랐다면, 이는 코드가 좀 더 작은 조각들로 나뉘는 리펙토링이 필요하다는 징조다.

---

### 열거형 정의 내에서 제네릭 데이터 타입 사용하기

구조체와 유사하게, 열거형도 그 variant 내에서 제네릭 데이터 타입을 갖도록 정의할 수 있다. 표준 라이브러리가 제공하는 `Option<T>` 열거형에서 이를 사용했었다.

```rust
enum Option<T> {
    Some(T),
    None,
}
```

`Option<T>`를 다시 보면 `T`타입에 제네릭인 열거형임을 알 수 있다. 타입 `T`값 하나를 들고 있는 `Some`, 또는 어떠한 값도 들고 있지 않는 `None`이다. `Option<T>` 표준 라이브러리는 구체적인 타입을 가진 이 열거형에 대한 값의 생성을 하기 위한 딱 한가지 정의만 가지고 있으면 된다. 

열거형 또한 여러 개의 제네릭 타입을 이용할 수 있다. `Result`열거형의 정의가 그 예시다.

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

`Result` 열거형은 `T`와 `E`, 두 개의 타입을 이용한 제네릭이다. `Result`는 두 개의 variant를 가지고 있다. 타입 `T`의 값을 들고 있는 `Ok`, 그리고 타입 `E`를 가지고 있는 `Err`이다. 이러한 구조는 성공해서 `Ok`내의 값을 가져가거나, 또는 실패하는 연산 어디에도 편리하게 사용할 수 있도록 해준다. 

---

### 메소드 정의 내에서 제네릭 데이터 타입 사용하기

정의부에 제네릭 타입을 갖는 구조체와 열거형 상의 메소드를 구현할 수 있다.  다음은 앞서 정의한 `Point<T>` 구조체이며 필드 `x`의 값에 대한 참조자를 반환하는 `x`라는 이름의 메소드를 정의했다.

```rust
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> {
    fn x(&self) -> &T {
        &self.x
    }
}

fn main() {
    let p = Point { x: 5, y: 10 };

    println!("p.x = {}", p.x());
}
```

`impl` 바로 뒤에 `T`를 정의해야한다.

**구조체 정의** 내에서의 제네릭 타입 파라미터와 **구조체의 메소드 정의** 내에서의 제네릭 타입 파라미터는 항상 같지 않다. 다음 예제에서의 `mixup` 메서드는 또다른 `Point`를 파라미터를 갖는데, 이는 `self`의 `Point`와 다른 타입을 가지고 있을 수 있다.

```rust
struct Point<T, U> {
    x: T,
    y: U,
}

impl<T, U> Point<T, U> {
    fn mixup<V, W>(self, other: Point<V, W>) -> Point<T, W> {
        Point {
            x: self.x,
            y: other.y,
        }
    }
}

fn main() {
    let p1 = Point { x: 5, y: 10.4 };
    let p2 = Point { x: "Hello", y: 'c'};

    let p3 = p1.mixup(p2);

    println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
}
```

---

### 제네릭을 이용한 코드의 성능

Rust에서 제네릭을 파라미터를 이용하는 것이 구체적인 타입을 명시했을 때와 비교해 전혀 느리지 않다. Rust는 컴파일 타입에 제네릭을 사용하는 코드에 대해 *단형성화(monomorophization)* 을 한다. 단형성화란 제네릭 코드를 실제로 채워질 구체적인 타입으로 된 코드로 바꾸는 것이다. 

컴파일러는 제네릭 코드가 호출되는 모든 곳을 살펴보고 제네릭 코드가 호출될 때 사요된 구체적인 타입에 대한 코드를 생성한다.

표준 라이브러리의 `Option` 열거형을 보자.

```rust
let integer = Some(5);
let float = Some(5.0);
```

러스트가 이 코드를 컴파일할 때 단형성화를 할 것이다. 컴파일러는 `Option`에 넘겨진 값들을 읽고 두 종류: `i32`와 `f64`라는 두 가지의 제네릭 `Option<T>`를 가지고 있다는 것을 알게 된다. 그렇게 컴파일러는 제네릭 정의를 명시된 타입들로 교체함으로써 `Option<T>`에 대한 제네릭 정의를 `Option_i32`와 `Option_f64`로 확장시킬 것이다.  

컴파일러가 생성한 버전의 코드는 다음과 같다.

```rust
enum Option_i32 {
    Some(i32),
    None,
}

enum Option_f64 {
    Some(f64),
    None,
}

fn main() {
    let integer = Option_i32::Some(5);
    let float = Option_f64::Some(5.0);
}
```

---

## 트레잇: 공유 동작을 정의하기

트레잇은 타입들이 공통적으로 갖는 동작에 대해서 추상화하도록 해준다. 트레잇(trait) 이란 Rust 컴파일러에게 특정한 타입이 갖고 다른 타입들과 함께 공유할 수도 있는 기능에 대해서 말해준다. 제네릭 타입 파라미터를 사용하는 상황에서, 컴파일 타임에 해당 제네릭 타입이 어떤 트레잇을 구현한 타입이어야 함을 명시하여, 해당 상황에서 우리가 사용하길 원하는 동작을 갖도록 하기 위해 트레잇 바운드를 사용할 수 있다.

> 트레잇은 다른 언어들에서 ‘인터페이스’라고 부르는 기능과 유사하지만, 몇 가지 다른 점이 있다.
> 

### 트레잇 정의하기

어떤 타입의 동작은 해당 타입이 호출할 수 있는 메소드들로 구성되어 있다. 만약 서로 다른 타입에 대해 모두 동일한 메소드를 호출할 수 있다면, 이 타입들은 동일한 동작을 공유하는 셈이다. 트레잇의 정의는 어떠한 목적을 달성하기 위해 필요한 동작들의 집합을 정의하기 위해 메소드 시그니처들을 함게 묶는 방법이다.

예를 들면 다양한 종류와 양의 텍스트를 갖는 여러 구조체가 있다고 가정하자. `NewsArticle` 구조체는 세계 특정한 곳에서 줄지어 들어오는 뉴스 이야기를 들고 있고, `Tweet`은 최대 140자의 콘텐츠와 해당 트윗이 리트윗인지 아니면 답변인지와 같은 메타데이터를 가지고 있다.

우리는 `NesArticle` 또는 `Tweet` 인스턴스의 종합 정리를 보여줄 수 있는 미디어 종합기 라이브러리를 만들고 싶어한다고 해봅시다. 각각의 구조체들에게 `summary` 메소드를 정의해야하며 , 각 인스턴스 상에서 `summary` 메소드를 호출함으로써 해당 정리를 얻을 수 있어야 한다. 다음은 이런 개념을 표현한 `Summarizable` 트레잇의 정의를 나타낸 것이다.

```rust
pub trait Summarizable {
	fn summary(&self) -> String;
}
```

---

`trait` 키워드 다음에 트레잇의 이름, 예시에 경우 `Summarizable`을 써서 트레잇을 선언 했다. 중괄호 내에서는 이 트레잇을 구현하는 타입들이 가질야 하는 메소드 시그니처들을 정의했는데, 위의 경우 `fn summary(&self) → String` 이다. 메소드 시그니처 두에, 중괄호로 해당 메소드를 정의하는 것이 아니라, 세미콜론을 집어넣었다. 그러면 이 트레잇을 구현할 각 타입은 이 메소드에 대한 해당 타입 고유의 정의부를 제공해야 하는데, 컴파일러가 `Summarizable` 트레잇을 갖는 어떤 타입이든 그에 대한 메소드 `summary`를 정확히 동일한 시그니처로 정의하도록 강제할 것이다.

트레잇은 한 줄 당 하나의 시그니처와 각 줄의 끝에 세미콜론을 갖도록 함으로써, 본채 내에 여러 개의 메소드를 가질 수 있다.

---

### 특정 타입에 대한 트레잇 구현하기

`Summarizable` 트레잇을 정의하였으니, 이제 우리의 라이브러리 내에서 이 동작을 갖길 원했던 타입들 상에 이 트레잇을 구현할 수 있다.

다음은 `summary`의 반환 값을 만들기 위해 헤드라인, 저자, 위치를 사용하는 `NewsArticle` 구조체 상의 `Summarizable` 트레잇 구현을 보여준다. `Tweet` 구조체에 대해서는, 트윗 내용이 이미 140자로 제한되어 있으니, `summary`를 정의할 때 사용자의 이름과 해당 트윗의 전체 텍스트를 가지고 오는 선택을 했다.

---

`impl` 키워드 뒤에 구현하고 하는 트레잇의 이름을 넣고, 그다음 `for`와 해당 트레잇을 구현하고자 하는 타입의 이름을 써서 구현한다. 해당 블록 내에 트레잇의 정의부에서 정의했던 메소드 시그니처를 집어넣지만, 이번에는 선언이 아니라 구현부까지 작성함으로서 메소드의 본채를 채워야 한다.

---

이렇게 트레잇을 한번 구현했다면, `NewArticle`과 `Tweet`의 인스턴스 상에서 평범하게 메소드를 호출하는 것과 동일한 방식으로 트레잇 상의 메소드를 호출할 수 있다.

```rust
let tweet = Tweet {
    username: String::from("horse_ebooks"),
    content: String::from("of course, as you probably already know, people"),
    reply: false,
    retweet: false,
};

println!("1 new tweet: {}", tweet.summary());
```

```rust
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summarizable for NewsArticle {
    fn summary(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}

pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}

impl Summarizable for Tweet {
    fn summary(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}
```

---

위에서 `Summarizable` 트레잇과 `NewsArticle` 및 `Tweet` 타입을 동일한 `lib.rs` 내에 정의했기 때문에, 이들이 모두 동일한 스코프에 존재한다. 만약 이 `lib.rs`가 `aggregator` 라는 그레이트에 대한 것이고 누군가가 우리의 크레이트 기능에 더해 `WeatherForecast`라는 구조체에 대해서 `Summarizable`을 구현하고 싶다면, 코드에 구현하기 앞서 `Summarizable` 트레잇을 스코프로 가져올 필요가 있다.

코드는 `Summarizble`이 공개 트레잇임을 가정하는데 이는 트레잇을 선언할 때  `pub` 키워드를 집어 넣었기 때문이다.

---

트레잇을 구현할 때 주의해야할 점이 있다. 트레잇과 타입 둘 중 하나가 우리의 크레이트 내의 것일 경우에만 해당 타입에서의 트레잇을 정의할 수 있다. 다시 말하면 둘다 우리의 크레이트 내의 것이 아니라면 정의할 수 없다. 예를 들면 `Vec`에 대한 Display 트레잇은 구현이 불가능하다. `Vec`와 Display 모두 표준 라이브러이 내에 정의되어 있기 때문이다. 반대로 우리가 정의한 `aggregator` 일부인 `Tweet`과 같은 커스텀 타입에 대한 표준라이브러리의 Display 트레잇을 구현하는 것은 허용된다. 또 `aggregator` 크레이트 내에서 `Vec`에 대한 `Summarizable`을 구현하는 것도 가능하다. `Summarizable`이 크레이트 내에서 정의되어 있기 때문이다.

이를 *고아 규칙(orphan rule)* 이라고 하는데, 러스트가 이를 강제하기 때문에, 다른 사람이 우리들의 코드를 망가 뜨리지 못하고 반대의 경우도 마찬가지다.

```rust
extern crate aggregator;

use aggregator::Summarizable;

struct WeatherForecast {
    high_temp: f64,
    low_temp: f64,
    chance_of_precipitation: f64,
}

impl Summarizable for WeatherForecast {
    fn summary(&self) -> String {
        format!("The high will be {}, and the low will be {}. The chance of
        precipitation is {}%.", self.high_temp, self.low_temp,
        self.chance_of_precipitation)
    }
}
```

---

### 기본 구현

트레잇에서 정의한 메소드가 꼭 모든 타입이 각자 타입에 맞는 커스텀 동작을 정의할 필요는 없다. 각 메소드의 기본 동작을 유지하거나 오버라이드 하도록 선택할 수 있다.

다음은 메소드 시그니처를 정의만 하는 선택 대신 `Summarizable` 트레잇의 `summary` 메소드가 기본 스트링을  반환 하는 방법이다.

만약 우리가 NewsArticle 인스턴스가 위 트레잇을 구현하고 summary 메소드를 사용하고 싶다면 빈 impl 블록을 명시하면 된다.

```rust
impl summarizable for NewArticle {}
```

```rust
pub trait Summarizable {
    fn summary(&self) -> String {
        String::from("(Read more...)")
    }
}
```

---

비록  `NewsArticle` 에 대한 `summary` 메소드를 직접 정의하는 선택을 더 이상 하지 않았더라도, `summary` 메소드가 기본 구현을 갖고 있고, `NewsArticle`이 `Summarizable` 트레잇을 구현하도록 명시했기 때문에, `NewArticle`의 인스턴스는 `summary` 메소드를 호출 할 수 있다.

다음 코드는 `New artilve available! (Read more…)`를 출력한다.

이렇게 `Summarizable` 트레잇 에서 `summary` 메서드가 기본 구현으로 이루어져 있더라도, 이 메서드를 오버라이드 할 수 있다. `Summarizable` 내에 정의한 `summary` 메서드 시그니처만 정확하게 따른다면, 해당 메서드를 오버라이드 할 수 있다.  추가적으로 기본 구현은 동일한 트레잇 내의 다른 메소드들을 호출하는 것이 허용되어 있는데, 심지어 그 다른 메소드들이 기본 구현을 갖고 있지 않아도 된다. 이러한 방법으로  트레잇은 수많은 유용한 기능을 제공하면서도 다른 구현자들이 해당 트레잇의 작은 일부분만 구현하도록 요구할 수 있다.

```rust
let article = NewsArticle {
    headline: String::from("Penguins win the Stanley Cup Championship!"),
    location: String::from("Pittsburgh, PA, USA"),
    author: String::from("Iceburgh"),
    content: String::from("The Pittsburgh Penguins once again are the best
    hockey team in the NHL."),
};

println!("New article available! {}", article.summary());
```

---

[다음은](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21) `Summarizable` 트레잇이 구현이 필요한 `author_summary` 메소드와 구현이 필요 없는 기본 구현 메소드 `summary`가 `author_summary` 메소드를 호출하는 형태다.

이 버전의 `Summarizble`을 사용하기 위해서는, 이 트레잇을 구현할 때 `author_summary`만 정의하면 된다. 

```rust
impl Summarizable for Tweet {
    fn author_summary(&self) -> String {
        format!("@{}", self.username)
    }
}
```

일단 `author_summary`를 정의하면, `Tweet` 구조체의 인스턴스 상에서 `summary`를 호출할 수 있으며, `summary`의 기본 구현이 `author_summary`의 정의부를 호출할 것이다.

[다음 코드](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21)는 `1 new tweet: (Read more from @horse_ebooks…)` 를 출력할 것이다.

```rust
pub trait Summarizable {
    fn author_summary(&self) -> String;

    fn summary(&self) -> String {
        format!("(Read more from {}...)", self.author_summary())
    }
}
```

```rust
let tweet = Tweet {
    username: String::from("horse_ebooks"),
    content: String::from("of course, as you probably already know, people"),
    reply: false,
    retweet: false,
};

println!("1 new tweet: {}", tweet.summary());
```

---

## 트레잇 바운드

제네릭 타입 파라미터를 이용하는 트레잇을 사용할 수 있다. 제네릭 타입에 제약을 두어서 제네릭 타입이 어떤 타입이 되는 것보다, 제네릭 타입이 특정한 트레잇을 구현하도록 해서 이 타입들이 가지고 있을 필요있는 메소드를 구현하도록 제한하는 방법으로 제네릭 타입을 컴파일러가 확신할 수 있도록 한다. 

예를 들면, 우리는 이전에 `NewsArticle`과 `Tweet` 타입에 대해서 `Summarizable` 트레잇을 구현했다. 만약 파라미터 `item` 상에서 `summary` 메소드를 호출하는 함수 `notify`를 정의한다고 했을 때, 이 `item`은 제네릭 타입 `T`라고 가정하자. 에러없이 `item` 상에서 `summary`를 호출하기 위해서는, `T`에 대한 트레잇 바운드를 사용해서 `item`이 `Summarizable` 트레잇을 반드시 구현한 타입이어야 함을 특정할 수 있다.

```rust
pub fn notify<T: Summarizable>(item: T) {
    println!("Breaking news! {}", item.summary());
}
```

---

트레잇 바운드는 제네릭 타입 파라미터의 선언부와 함께, 꺾쇠 괄호(<>) 내에 콜론 뒤에 온다. T상에서의 트레잇 바운드 이므로, `notify`를 호출할 때 `NewsArticle`이나 `Tweet`의 어떠한 인스턴스라도 넘길 수 있다. `String`이나 `i32`같은 다른 타입을 가지고 `notify` 함수를 호출하는 코드는 `Summarizable`를 구현하지 않았기 때문에 컴파일 되지 않을 것이다.

`+` 를 이용하면  하나의 제네릭 타입에 대해 여러 개의 트레잇 바운드를 특정할 수 있다. 만일 함수 내에서 타입 `T`에 대해 `summary` 메소드 뿐만 아니라 형식화된 출력을 사용하길 원한다면, 트레잇 바운드 `T: Summarizable + Display` 를 사용할 수 있다. 이는 `T`가 `Summarizable`과 `Display` 둘 다 구현한 타입이어야 함을 나타낸다.

여러 개의 제네릭 타입을 가진 함수에서, 각 제네릭은 고유의 트레잇 바운드를 가진다. 함수이름 옆 꺾쇠 괄호 내에 많은 수의 트레잇 바운드를 특정하는 것은 가독성이 떨어질 수 있다. 따라서 함수 시그니처 뒤에 `where` 절을 사용함으로서 가독성을 높일 수 있다.

```rust
fn some_function<T: Display + Clone, U: Clone + Debug>(t: T, u: U) -> i32 {
```

```rust
fn some_function<T, U>(t: T, u: U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{
```

---

## 라이프타임을 이용한 참조자 유효화

Rust에서 모든 참조자는 *라이프타임(lifetime)*을 갖는데, 이는 해당 참조자가 유요한 범위다. 대부분의 경우에서 타입들이 추론되는 것처럼 대부분의 경우에서 라이프타임도 암묵적이며 추론된다. 여러 가지 타입이 가능할때 타입을 명시해야하는 것처럼 참조자의 라이프타임이 몇몇 다른 방식으로 연관될 수 있으므로 라이프타임 파라미터를 이용해서 이 관계를 명시할 수 있다. 이런 개념은 흔치 않은데, 다른 프로그래밍 언어에서는 찾을수 없는 기능입니다.

---

### 라이프타임은 댕글링 참조자를 방지합니다.

라이프타임의 주목적은 댕글링 참조자(dangling reference)를 방지하는 것이다. 이는 프로그램이 우리가 참조하기로 의도한 데이터가 아닌 다른 데이터를 참조하는 원인이 된다. 다음과 같이 외부 스코프와 내부 스코프를 가진 프로그램을 생각해보자. 외부 스코프는 `r`이라는 이름의 변수를 초기값 없이 선언했고, 내부 스코프는 `x`라는 이름의 변수를 초기값 `5`와 함께 선언했다. 내부 스코프 내에서, `x`의 참조자를 `r`에 대입하고 그 후 내부스코프가 닫히고 `r`이 출력하도록 해보자.

---

```
error: `x` does not live long enough
   |
6  |         r = &x;
   |              - borrow occurs here
7  |     }
   |     ^ `x` dropped here while still borrowed
...
10 | }
   | - borrowed value needs to live until here
```

```rust
{
    let r;

    {
        let x = 5;
        r = &x;
    }

    println!("r: {}", r);
}
```

이 코드를 컴파일 하면, 왼쪽과 같은 에러가 발생한다.

변수 `x`는 “충분히 오해 살지 못한다(does not live long enough)”고 한다. `x`는 7번 라인의 닫는 괄호 기호에 도달했을 때 스코프 밖으로 벗어날 것이다. 그러나 `r`는 스코프 밖에서도 유효하다.  우리는 이것을 “더 오래 산다” 라고 말한다. 만약 이 코드가 작동한다면, `r`은 `x`가 스코프 밖으로 벗어났을 때 할당이 해제되는 메모리를 참조하게 될 것이고, `r`을 가지고 시도하려 했던 어떤 동작이든지 정확하게 동작하지 않게 될 것이다. 그렇다면 러스트는 이 코드가 허용되어서는 안 된다는 것을 어떻게 결정할까?

---

### 빌림 검사기(Borrow checker)

*빌림 검사기(borrow checker)* 라고 불리는 컴파일러의 일부가 모든 빌림이 유효한지를 결정하기 위해 스코프를 비교한다. 다음은 변수들의 라이프 타임을 보여주는 주석과 함께 이전과 동일한 예제다.

`r`의 라이프타임을 `‘a`라고 명명하고, `x`의 라이프타임을 `‘b`라고 명명했다. 보다시피, 내부의 `‘b` 블록은 외부의 `‘a` 라이프타임 블록에 비해 훨씬 작다. 컴파일 타임에서, Rust는 두 라이프타임의 크기를 비교하고 `r`이 `‘a` 라이프타임을 가지고 있지만, `‘b` 라이프타임을 가지고 있는 어떤 오브젝트를 참조하고 있음을 보게 된다. `‘b` 라이프타임이 `‘a` 라이프타임에 비해 작기 때문에 컴파일러는 이 프로그램을 거부한다. 참조 당하는 쪽이 참조자만큼 오래 살지 못하고 있기 때문이다.

```rust
{
    let r;         // -------+-- 'a
                   //        |
    {              //        |
        let x = 5; // -+-----+-- 'b
        r = &x;    //  |     |
    }              // -+     |
                   //        |
    println!("r: {}", r); // |
                   //        |
                   // -------+
}
```

---

다음은 반대로 댕글링 참조자를 만드는 시도가 없고 에러 없이 컴파일 되는 코드다.

여기서 `x`는 라이프타임 `‘b`를 갖고 있는데, `‘a`에 비해 더 크다. 이는 `r`이 `x`를 참고할 수 있음을 의미한다. 컴파일러는 `r`의 참조자가 `x`가 유효한 동안 언제나 유효할 것이라는것을 확신할 수 있다.

```rust
{
    let x = 5;            // -----+-- 'b
                          //      |
    let r = &x;           // --+--+-- 'a
                          //   |  |
    println!("r: {}", r); //   |  |
                          // --+  |
}                         // -----+
```

---

### 함수에서의 제네릭 라이프타임

두 스트링 슬라이스 중 긴 쪽을 반환하는 함수를 반환해보자. 두 개의 스트링 슬라이스를 넘겨서 호출할 수 있고, 스트링 슬라이스를 반환해야 한다. `longest` 함수를 구현하면 `The longest string is abcd` 를 출력해야 한다.

`longest` 함수가 인자의 소유권을 얻는 것을 원하지는 않기 때문에 스트링 슬라이스를 인자로 갖는것이다. (스트링 슬라이스는 모두 참조자다) 또한 스트링 슬라이스는 물론 스트링 리터럴 또한 받아들인다.

```rust
fn main() {
    let string1 = String::from("abcd");
    let string2 = "xyz";

    let result = longest(string1.as_str(), string2);
    println!("The longest string is {}", result);
}
```

---

만약 다음과 같이 구현한다면 이는 컴파일 되지 않을것이다.

```rust
fn longest(x: &str, y: &str) -> &str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

```
error[E0106]: missing lifetime specifier
   |
1  | fn longest(x: &str, y: &str) -> &str {
   |                                 ^ expected lifetime parameter
   |
   = help: this function's return type contains a borrowed value, but the
   signature does not say whether it is borrowed from `x` or `y`
```

대신 라이프타임에 대한 에러를 얻는데, 이는 반환 타입에 대하여 제네릭 라이프타입 파라미터가 필요하다고 말해주고 있다. 왜냐하면 반환될 값이 `x`를 참조할지 `y`를 참조할지 알 수 없기 때문이다.  또한 함수에 넘겨지게 될 참조자의 구체적인 라이프타임도 알지 못한다. 어떤 것이 반환될 것인지 알 수도 없기 때문에 `x`와 `y`의 라이프타임이 반환될 값의 라이프타임과 어떻게 연관되어 있는지 알지 못한다. 따라서 참조자들 간의 관계를 정의하는 제네릭 라이프타임 파라미터를 추가해서 빌림 검사기가 분석을 수행할 수 있도록 한다.

---

### 라이프타임 명시 문법

라이프타임을 명시하는 것은 연관된 참조자가 얼마나 오랫동안 살게 되는지를 바꾸지는 않는다. 함수의 시그니처가 제네릭 타입 파라미터를 특정할 때 어떤 타입이든 허용할 수 있는 것과 같은 방식으로, 함수의 시그니처가 제네릭 라이프타임 파라미터를 특정할 때면 이 함수는 어떤 라이프타임을 가진 참조자라도 허용할 수 있다. 라이프 타임 명시가 하는 일은 그저 여러 개의 참조자에 대한 라이프타임을 서로 연관 짓도록 하는 것이다.

라이프타임 파라미터의 이름은 어퍼스트로피`’`로 시작해야 한다. 라이프타임 파라미터의 이름은 보통 모두 소문자이고, 제네릭 타입과 비슷하게 이름은 보통 매우 짧다.

`‘a`는 대부분의 사람들이 기본적으로 사용하는 이름이다. 라이프타임 파라미터 명시는 참조자의 `&` 뒤에 오며, 공백 문자가 라이프타임 명시와 참조자의 타입을 구분해준다.

```rust
&i32        // a reference
&'a i32     // a reference with an explicit lifetime
&'a mut i32 // a mutable reference with an explicit lifetime
```

1. 라이프타임 파라미터가 없는 `i32`에 대한 참조자.
2. `‘a`라고 명명된 라이프타임 파라미터를 가지고 있는 i32에 대한 참조자.
3. 라이프타임 `‘a`를 갖고 있는 i32에 대한 가변 참조자.

스스로에 대한 하나의 라이프타임 명시는 큰 의미를 지니고 있지 않다. 라이프타임 명시는 Rust에게 여러 개의 참조자에 대한 제네릭 라이프타임 파라미터가 서로 어떻게 연관되는지를 말해준다. `‘a`를 가지고 있는  `i32`에 대한 두 참조자를 파라미터로 갖는 함수가 있다면 이 라이프타임 명시는 두 팜조자가 동일한 제네릭 라이프타임만큼 살아야 한다는 것을 의미한다.

---

### 함수 시그니처 내의 라이프타임 명시

그렇다면 longest함수에 라이프타임을 명시해보자. 제네릭 타입 파라미터처럼 제네릭 라이프타임 파라미터도 함수 이름과 파라미터 리스트 사이에 꺾쇠괄호(<>)를 쓰고 그 안에 정의한다.

파라미터들과 반환 값에서의 참조자들에 대해 컴파일러에게 말해주고 싶은 것은 그들이 모두 동일한 라이프타임을 갖고 있어야 한다는 것이다. 다음 코드처럼  `‘a`라고 명명하여 각각의 참조자에 추가할 수 있다.

이는 컴파일 되고, 정상적으로 실행될 것이다.

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

이 함수 시그니처는 어떤 라이프타임 `‘a`에 대하여, 두개의 파라미터를 갖게 될 것인데, 두 파라미터 모두 적어도 라이프타임 `‘a` 만큼 살아있는 스트링 슬라이스임을 말해준다. 또한 이 함수의 반환값도 적어도 라이프타임 `‘a`만큼 살아있는 스트링 슬라이스를 반환할 것이다.

이 함수 시그니처 내에 라이프타임 파라미터를 특정함으로써, 함수에 넘겨지거나 반환되는 값들의 라이프타임도 바꾸지 않지만, 여기에 부합하지 않는 어떠한 값들도 빌림 검사기에 의해 거부되어야 함을 말해준다. 이 함수는 `x`와 `y`가 정확히 얼마나 오래 살게 될지 알지 못하지만, 다만 이 시그니처를 만족시킬 `‘a`에 대입될 수 있는 어떤 스코프가 있음을 알아야 할 필요가 있을 뿐이다.

구체적인 참조자들이 `longest`로 넘겨질 때, `‘a`에 대입되게 되는 구체적인 라이프타임은 `y`의 스코프와 겹치는 `x`의 스코프의 부분이다. 스코프는 언제나 중첩되기 때문에, 다시말하면 제네릭 라이프타임 `‘a`은 `x`와 `y`이 라이프타임 중에서 더 작은 쪽과 동일한  라이프타임이다. 반환되는 참조자에 대해서도 같은 라이프타임 파라미터인 `‘a`를 명시했으므로, 반환되는 참조자도 `x`와 `y`의 라이프타임 중 짧은 쪽만큼 유효함을 보장할 것이다.

---

서로 다른 라이프타임을 가진 참조자들을 넘기는 예시를 보자. `string1`은 외부 스코프가 끝날 때까지 유효하고, `string2`는 내부 스코프가 끝날 때까지 유효하며, `result`는 내부 스코프가 끝날 때까지 유효한 무언가를 참조한다. 이는 빌림검사기를 통과하고 컴파일된다. 

실행되었을 때 `The longest string is long string is long`을 출력한다.

```rust
fn main() {
    let string1 = String::from("long string is long");

    {
        let string2 = String::from("xyz");
        let result = longest(string1.as_str(), string2.as_str());
        println!("The longest string is {}", result);
    }
}
```

---

다음으로, result의 참조자의 라이프타임이 두 인자들의 라이프타임보다 작아야 함을 보여주는 예제다. result의 선언부를 외부 스코프로 옮기고, result의 할당을 string2가 있는 스코프에 남겨둔다. 다음으로 result를 이용하는 println! 구문을 내부 스코프 바깥에, 내부스코프가 끝나는 시점으로 옮긴다. 다음은 컴파일 되지 않을 것이다.

---

```
error: `string2` does not live long enough
   |
6  |         result = longest(string1.as_str(), string2.as_str());
   |                                            ------- borrow occurs here
7  |     }
   |     ^ `string2` dropped here while still borrowed
8  |     println!("The longest string is {}", result);
9  | }
   | - borrowed value needs to live until here
```

```rust
fn main() {
    let string1 = String::from("long string is long");
    let result;
    {
        let string2 = String::from("xyz");
        result = longest(string1.as_str(), string2.as_str());
    }
    println!("The longest string is {}", result);
}
```

이 에러는 result가 println! 에서 유효하기 위해서는, string2가 외부 스코프의 끝까지 유효할 필요가 있다고 말한다. Rust는 이를 알고 있는데, 그 이유는 우리가 함수의 파라미터들과 반환 값에 대해 동일한 라이프타임 파라미터 ‘a를 적었기 때문이다.

우리는 인간으로 string1이 더 길기 때문에 result에 string1의 참조자를 담게 될 것이라는 점을 알 수 있다. string1이 스코프 밖으로 나가기 전에 println! 구문에서 유효할 것이다. 하지만  Rust에게 라이프타임 파라미터를 가지고 말해준 것은 longest 함수에 의해 반환되는 참조자의 라이프타임이 인자로 넘겨준 라이프타임들 중 작은 쪽이기 때문이다. 따라서 빌림 검사기는 잠재적으로 유효하지 않은 참조자를 가질 수 있는 문제로 인해 코드를 허락하지 않는다.

---

### 라이프타임의 측면에서 생각하기

라이프타임 파라미터를 특정하는 정확한 방법은 함수가 어떤 일을 하고 있는가에 따라 달린 문제다. 예를 들면, `longest` 함수의 구현을 가장 긴 스트링 슬라이스 대신 항상 첫 번째 인자를 반환한다고 해보자. 그렇다면 `y`파라미터에 대한 라이프타임을 특정할 필요는 없을 것이다. 다음 코드는 컴파일 된다. 

이 예제에서, 파라미터 `x`와 반환 값에 대한 라이프타임 파라미터 `‘a` 는 특정했지만, 파라미터 `y`는 특정하지 않았다. 그 이유는 `y`의 라이프타임이 `x` 혹은 반환될 값의  라이프타임과 어떠한 관련도 없기 때문이다.

---

함수로부터 참조자를 반환할 때, 반환 타입에 대한 라이프타임 파라미터는 인자 중 하나의 라이프타임 파라미터와 일치할 필요가 있다. 만일 반환되는 참조가 인자들 중 하나를 참조하지 **않는다면**, 다른 유일한 가능성은 이 함수 내에서 생성된 값을 참조하는 경우인데, 이 값은 함수가 끝나는 시점에서 스코프 밖으로 벗어나기 때문에 댕글링 참조자가 될 것이다. `longest` 함수에 대한 다음 구현은 컴파일 되지 않는다 

반환 타입에 대해 라이프타임 파라미터 `‘a`를 특정했을지라도, 이러한 구현은 컴파일에 실패하는데, 이는 반환되는 값의 라이프타임 파라미터의 라이프타임과 아무런 관련이 없기 때문이다.

문제는 `result`가 `longest` 함수가 끝나는 지점에서 스코프 밖으로 벗어나게 되어 메모리 해제가 일어나게 되는데, 이 함수로부터 `result`의 참조자를 반환하려는 시도를 한다는 점이다. 이 댕글링 참조자를 변경시킬 라이프타임 파라미터를 특정할 방법은 없고, Rust는 댕글링 참조자를 만들게끔 놔두지 않는다. 이 경우, 가장 좋은 수정 방법은 참조자보다는 차라리 값을 소유한 데이터 타입을 리턴하도록 하여 호출하는 함수가 값을 할당 해제하도록 하는 것이다.

```rust
fn longest<'a>(x: &'a str, y: &str) -> &'a str {
    x
}
```

---

```rust
fn longest<'a>(x: &str, y: &str) -> &'a str {
    let result = String::from("really long string");
    result.as_str()
}
```

```
error: `result` does not live long enough
  |
3 |     result.as_str()
  |     ^^^^^^ does not live long enough
4 | }
  | - borrowed value only lives until here
  |
note: borrowed value must be valid for the lifetime 'a as defined on the block
at 1:44...
  |
1 | fn longest<'a>(x: &str, y: &str) -> &'a str {
  |                                             ^
```

---

### 구조체 정의 상에서의 라이프타임 명시

구조체가 참조자를 들고 있도록 할 수 있지만, 구조체 정의 내의 모든 참조자들에 대하여 라이프타임을 표시할 필요가 있다. 다음은 스트링 슬라이스를 들고 있는 `ImpoprtantExcerpt` 라고 명명된 구조체가 있다.

이 구조체는 스트링 슬라이스를 담을 수 있는 `part`라는 하나의 필드를 갖고 있는데, 이것은 참조자다. 제네릭 데이터 타입과 마찬가지로, 제네릭 라이프타임 파라미터의 이름을 구조체의 이름 뒤편에 꺾쇠괄호 안에다 선언하여 구조체 정의의 본체 내에서 이 라이프타임 파라미터를 이용할 수 있도록 해야 한다.

여기 이 `main` 함수는 변수 `novel` 이 소유하고 있는 `String`의 첫 문장에 대한 참조자를 들고 있는 `ImportantExcerpt` 구조체의 인스턴스를 생성한다.

```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.')
        .next()
        .expect("Could not find a '.'");
    let i = ImportantExcerpt { part: first_sentence };
}
```

---

### 라이프타임 생략

여태것 모든 참조자가 라이프타임을 가지고 있으며, 참조자를 사용하는 함수나 구조체에 대해 라이프타임 파라미터를 특정할 필요가 있다고 했다. 하지만 이전에 사용한 코드에서 보듯이 라이프타임 명시 없이도 컴파일 된 적이 있다.

이 함수가 라이프타임 없이 컴파일되는 이유는 역사가 있다. 실제로 1.0 이전 Rust에서는 이 코드가 컴파일 되지 않았다. 모든 참조자들은 명시적인 라이프타임이 필요했었다. 그 시절, 함수 시그니처는 다음과 같이 작성되었다.

```rust
fn first_word<'a>(s: &'a str) -> &'a str {
```

수 많은 Rust 코드를 작성하고 난후, Rust 팀은 Rust 프로그래머들이 특정한 상황에서 똑같은 라이프타임 명시를 계속하고 타이핑 하고 있다는 사실을 발견했다. 이 상황들은 사실 예측 가능하며 몇 가지 결정론적인 패턴을 따르고 있었다. 그리하여 Rust 팀은 컴파일러 코드 내에 이 패턴들을 프로그래밍하여 이러한 상황 내에서는 프로그래머가 명시적으로 라이프타임 명시를 추가하도록 강제하지 않고, 빌림 검사기가 라이프타임을 추론할 수 있도록 하였다.

```rust
fn first_word(s: &str) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

---

참조자에 대한 Rust의 분석 기능 내에 프로그램이 된 패턴들을 일컬어 *라이프타임 생략 규칙* 이라고 한다. 이들은 프로그래머가 따라하는 규칙이 아니라, 컴파일러가 고려할 특정한 경우의 집합이고 만약 우리의 코드가 여기에 부합하면 명시적으로 라이프타임을 작성할 필요가 없어지는 것이다.

생략 규칙들은 모든 추론을 제공하지는 않는다. 만일 Rust가 결정론적으로 이 규칙을 적용했지만 여전히 참조자들이 어떤 라이프타임을 가지고 있는지에 대해 모호하다면, 해당하는 남은 참조자들의 라이프타임이 어덯게 되어야 하는지에 대해 추측하지 않을 것이다. 이러한 경우, 컴파일러는 프로그래머에게 이 참조자들이 서로 어떻게 연관되는지에 대해서 프로그래머의 의도에 맞게끔 라이프타임을 추가함으로써 해결 가능한 에러를 표시할 것이다.

먼저 몇 가지 정의들을 보자. 함수나 메소드의 파라미터에 대한 라이프타임을 *입력 라이프타입(input lifetime)*이라고 하며, 반환 값에 대한 라이프타임을 *출력 라이프타임(output lifetime*) 이라고 한다. 

이제 명시적인 라이프타임이 없을 때 참조자가 어떤 라이프타임을 가져야 하는지 알아내기 위해서 컴파일러가 사용하는 규칙들을 보자. 첫 번째 규칙은 입력 라이프타임에 적용되고, 다음 두 규칙은 출력 라이프타임에 적용된다. 만일 컴파일러가 이 세 가지 규칙의 끝에 도달하고 여전히 라이프타임을 알아낼 수 없는 참조자가 있다면, 컴파일러는 에러와 함께 멈출 것이다.

1. 참조자인 각각의 파라미터는 고유한 라이프타임 파라미터를 갖는다. 바꿔 말하면, 하나의 파라미터를 갖는 함수는 하나의 라이프타임 파라미터를 갖고:`fn foo<’a>(x: &’a i32)`, 두 개의 파라미터를 갖는 함수는 두 개의 라이프타임 파라미터를 따로 갖고: `fn foo<’a, ‘b>(x: &’a i32, y: &’b i32)`, 이와 같은 식이다.
2. 만일 정확히 딱 하나의 라이프타임 파라미터만 있다면, 그 라이프타임이 모든 출력 라이프타임 파라미터들에 대입된다. `fn foo<’a>(x: &’a i32) → &’a i32`.
3. 만일 여러 개의 입력 라이프타임 파라미터가 있는데, 메소드라서 그중 하나가 `&self` 혹은 `&mut self` 라고 한다면, `self`의 라이프타임이 모든 출력 라이프타임 파라미터에 대입된다. 

---

직접 컴파일러가 되어 규칙들을 적용시켜서 바로 직전 예시 코드에서 `first_word`함수의 시그니처에 있는  참조자들의 라이프타임이 무엇인지 알아내 보자. 함수 시그니처는 참조자들과 관련된 아무런 라이프타임도 없이 시작한다.

- `fn first_word(s: &str) → &str {`

그러면 컴파일러는 첫 번째 규칙을 적용하는데, 이는 각각의 파라미터가 고유한 라이프타임을 갖는다고 말한다. 우리는 이를 평범하게 ‘a라고 명명할 것이며, 따라서 이제 시그니처는 다음과 같다.

- `fn first_word<’a>(s: &’a str) → &str {`

두 번째 규칙 상에 놓이게 되는데, 이는 정확히 단 하나의 입력 라이프타임만 존재하기 때문에 적용된다. 이제 시그니처는 다음과 같다

- `fn first_word<’a> (s: &’a str) → &’a str {`

이제 이 함수는 시그니처의 모든 참조자들이 라이프타임을 갖게 되었고, 컴파일러는 프로그래머에게 이 함수 시그니처 내의 라이프타임을 명시하도록 요구하지 않는다.

또 다른 예제를 해보자.

- `fn logest(x: &str, y: &str) → &str {`

다시 한번 컴파일러의 입장에서 첫 번째 규칙을 적용해보자.

- `fn longest<’a, ‘b>(x: &’a str, y: &’b str) → &str {`

두 번째 규칙을 살펴보았을때, 하나 이상의 입력 라이프타임이 있으므로 적용되지 않는다. 세 번째 규칙을 살펴봤을 때, 이 또한 적용되지 않는데 이는 메소드가 아니라 함수고, 따라서 어떠한 파라미터도 self가 아니다. 따라서 더 이상 규칙이 남아있지 않기때문에, 아직도 반환 타입의 라이프타임이 무엇인지 알아내지 못했다. 

이것이 바로 [이 코드](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21)가 에러가 발생한 이유다. 컴파일러는 자신이 알고있는 규칙들을 모두 적용해 보았지만, 여전히 이 시그니처의 참조자들에 대한 모든 라이프타임을 알아내지 못했기 때문이다. 

---

### 메소드 정의 내에서의 라이프타임 명시

라이프타임을 가진 구조체에 대한 메소드를 구현할 때, 문법은 또다시 제네릭 타입 파라미터와 같다. 라이프타임 파라미터가 선언되고 사용되는 곳은 라이프타임 파라미터가 구조체의 필드들 혹은 메소드 인자와 반환 값과 연관이 있는지 없는지에 따라 달린 문제다. 구조체 필드를 위한 라이프타임 이름은 언제나 `impl` 키워드 뒤에 선언되어야 하며, 그리고 나서 구조체의 이름 뒤에 사용되어야 한다.

`impl` 블록 안에 있는 메소드 시그니처에서, 참조자들이 구조체 필드에 있는 참조자들의 라이프타임과 묶일 수도 있고, 혹은 서로 독립적일 수도 있다. 여기에 더해, 라이프타임 생략 규칙이 종종 적용되어 메소드 시그니처 내에 라이프타임 명시를 할 필요가 없다. 

먼저 `level` 이라는 이름의 메소드가 있다. 파라미터는 오직 `self`에 대한 참조자이며, 반환 값은 무언가에 대한 참조자가 아닌 그냥 `i32`이다.

첫 번째 생략 규칙때문에 `self`로의 참조자의 라이프타임은 명시할 필요가 없다.

```rust
impl<'a> ImportantExcerpt<'a> {
    fn level(&self) -> i32 {
        3
    }
}
```

---

다음은 세번째 라이프타임 생략 규칙이 적용되는 예제다.

두 개의 입력 라이프타임이 있으므로, Rust는 첫 번째 라이프타임 생략 규칙을 적용하여 `&self`와 `annoucement` 에게 각각 라이프타임을 부여한다. 그 다음, 파라미터 중 하나가 `&self`이므로, 반환 타입은 `&self`의 라이프타임을 얻고 모든 라이프타임들이 추론된다.

```rust
impl<'a> ImportantExcerpt<'a> {
    fn announce_and_return_part(&self, announcement: &str) -> &str {
        println!("Attention please: {}", announcement);
        self.part
    }
}
```

---

### 정적 라이프타임(Static lifetime)

우리가 논의할 필요가 있는 특별한 라이프타임이 딱 하나 있다. 바로 `‘static`이다. `‘static` 라이프타임은 프로그램의 전체 생애주기를 가리킨다. 모든 스트링 리터럴은 `‘static` 라이프타임을 가지고 있는데, 다음과 같이 명시할 수도 있다.

```rust
let s: &'static str = "I have a static lifetime.";
```

이 스트링의 텍스트는 프로그램의 바이너리 내에 직접 저장되며 프로그램의 바이너리는 항상 이용이 가능하다. 따라서 모든 스트링 리터럴의 라이프타임은 `‘static`이다.

에러 메시지에서 `‘static` 라이프타임을 이용하라는 제안을 봤을지도 모른다. 하지만 참조자의 라이프타임으로써 `‘static`으로 특정하기 전에, 실제로 가지고 있는 참조자가 프로그램의 전체 라이프타임 동안 사는 것인지 대해 생각해본다. 대부분의 경우, 코드 내의 문제는 댕글링 참조자를 만드는 시도 혹은 사용 가능한 라이프타임들의 불일치이며, 해결책은 이 문제들을 해결하는 것이지 `‘static` 라이프타임으로 특정하는 것이 아니다.

---

### 제네릭 타입 파라미터, 트레잇 바운드, 라이프타임을 함께 써보기

```rust
use std::fmt::Display;

fn longest_with_an_announcement<'a, T>(x: &'a str, y: &'a str, ann: T) -> &'a str
    where T: Display
{
    println!("Announcement! {}", ann);
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

두 스트링 슬라이스 중 긴 쪽을 반환하는 `longest` 함수지만, `ann` 이름의 추가 인자를 가지고 있다. `ann` 의 타입은 제네릭 타입 `T`인데, `where` 절을 가지고 특정한 바와 같이 `Display` 트레잇을 구현한 어떤 타입으로도 채워질 수 있다. 

---

# 테스팅

예를 들어 어떤 숫자든 입력되면 2를 더하는 add_two 라는 함수를 작성한다고 하자. 이 함수의 시그니처는 정수를 파라미터로 받아들여서 정수를 결과로 반환한다. 이 함수를 구현하여 컴파일할 때, Rust는 이제껏 봐온 모든 종류의 타입 검사 및 빌림 검사를 할 것이다. 이러한 검사는, 이를테면 `String` 값이나 유효하지 않은 참조자를 함수에 넘기지 않도록 보장할 것이다. 그러나 Rust는 우리가 정확히 의도한 것을 함수가 수행하는 지를 검사할 수 없는데, 다시말하면 파라미터 더하기 10 혹은 파라미터 빼기 50이 아니라, 더하기 2여야 한다. 이러한 지점이 바로 테스트가 필요해지는 부분이다.

예를 들면 우리가 3을 add_two 함수에 넘겼을 때, 반환값은 5임을 단언하는(assert) 테스트를 작성할 수 있다. 우리는 어떤 종류의 코드 변경이 생겨도 기존의 정확히 동작하던 부분에 어떤 변화도 없음을 확신할 수 있도록 이 테스트들을 실행할 수 있다.

## 테스트 작성하는 방법

테스트는 테스트 아닌 코드가 프로그램 내에서 기대했던 대로 기능을 하는지 검증하는 함수다. 테스트 함수의 본체는 통상적으로 다음 세 가지 동작을 수행한다.

1. 필요한 데이터 혹은 상태를 설정하기
2. 테스트하고 싶은 코드를 실행하기
3. 그 결과가 우리 예상대로인지 단언하기(assert)

이러한 동작을 하는 테스트 작성을 위해 Rust가 특별히 제공하는 기능들을 살펴보자. `test` 속성, 몇 가지 매크로, 그리고 `should_panic` 속성들이 존재한다.

### 테스트 함수의 해부

간단하게 말하면, Rust 내의 테스트란 `test` 속성(attribute)이 주석으로 달려진 함수다. 속성은 Rust 코드 조각에 대한 메타데이터다. 한 가지 예로 앞서 구조체와 함께 사용했던 `derive` 속성이 있다. 함수를 테스트 함수로 변경하기 위해서는 `fn` 전라인에 `#[test]`를 축한다. `cargo test` 커맨드를 사용해 테스트를 실행시키면, 러스트는 `test` 속성이 달려있는 함수를 실행하고 각 테스트 함수가 성공 혹은 실패했는지를 보고하는 테스트 실행용 바이너리를 빌드할 것이다.

앞서 카고를 통해 새로운 라이브러리 프로젝트를 만들 때, 테스트 함수를 갖고 있는 테스트 모듈이 자동으로 생성되었다. 이 모듈은 테스트를 작성하기 시작하도록 도움을 주는데, 즉 우리가 새로운 프로젝트를 시작할 때마다 매번 테스트 함수를 위한 추가적인 구조 및 문법을 찾아보지 않아도 되게 해 준다. 우리는 원하는 만큼 추가적인 테스트 함수들과 테스트 모듈들을 추가할 수 있다.

실제 코드를 테스팅하지는 않으면서 자동으로 만들어진 템플릿 테스트를 가지고 실험하는 식으로 테스트가 어떻게 동작하는지 몇 가지 관점에서 탐구할 것이다. 그리고 나서 우리가 작성한 몇몇 코드를 호출하고 동작이 정확한지를 확고히 하는 실제의 테스트를 작성해보자.

`adder` 라고 하는 새로운 라이브러리 프로젝트를 만들자.

```
$ cargo new adder --lib
     Created library `adder` project
$ cd adder
```

`fn` 라인 전의 `#[test]` 어노테이션에 주목하자. 이 속성이 이것이 테스트 함수임을 나타내므로, 테스트 실행기는 이 함수를 테스트로 다루어야 한다는 것을 알게 된다. 또한 `tests` 모듈 내에 일반적인 시나리오를 셋업 하거나 일반적인 연산을 수행하는 것을 돕기 위한 테스트 아닌 함수를 넣을 수 있으므로, 어떤 함수가 테스트 함수인지 `#[test]`를 이용해 나타낼 필요가 있다.

함수 내에서는 2 + 2가 4와 같음을 단언하기 위해 `assert_eq!` 매크로를 사용한다. `cargo test` 커맨드는 이 프로젝트에 있는 모든 테스트를 실행한다.

```rust
pub fn add(left: usize, right: usize) -> usize {
    left + right
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn it_works() {
        let result = add(2, 2);
        assert_eq!(result, 4);
    }
}
```

---

```
$ cargo test
   Compiling adder v0.1.0 (file:///projects/adder)
    Finished dev [unoptimized + debuginfo] target(s) in 0.22 secs
     Running target/debug/deps/adder-ce99bcc2479f4607

running 1 test
test tests::it_works ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests adder

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

카고는 테스트를 컴파일하고 실행했다. `Compiling`, `Finished`, 그리고 `Running` 라인 이후에는 `running 1 test` 라인이 있다. 그다음 라인에는 생성된 테스트 함수의 이름인 `it_works`가 나타나고, 테스트의 실행 결과 `ok`가 나타난다. 그리고 나서 테스트 실행의 전체 요약이 나타난다. `test result: ok.` 는 모든 테스트가 통과했다는 뜻이다. `1 passed; 0 failed`는 과하거나 실패한 테스트의 개수를 추가적으로 보여준다.

따로 무시하라고 표시한 테스트가 없기 때문에, 요약문에 `0 ignored` 라고 표시된다. 

`Doc-tests adder` 로 시작하는 테스트 출력의 다음 부분은 문서 테스트의 결과를 보여주기 위한 것이다. 아직 어떠한 문서 테스트도 없긴 하지만, Rust는 API 문서 내에 나타난 어떠한 코드 예제라도 컴파일할 수 있다. 이 기능은 우리의 문서와 코드가 동기화를 유지하도록 돕는다. 지금은 잠깐 `Doc-tests` 부분은 넘어가자.

---

이번엔 테스트의 이름을 변경하고 테스트 출력이 어떻게 변하는지 보자. `it_works` 대신 `exploration`으로 변경하자.

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn exploration() {
        assert_eq!(2 + 2, 4);
    }
}
```

그리고 나서 `cargo test`를 다시 실행하자. 이제 출력 부분에서 `it_works` 대신 `exploration`을 볼 수 있을 것이다.

```rust
running 1 test
test tests::exploration ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

---

다른 테스트를 추가해보자. 이번에는 실패하는 테스트를 만들 것이다. 테스트 함수내의 무언가가 패닉을 일으키면 테스트를 실패한다. 각 테스트는 새로운 스레드 내에서 실행되며, 테스트 스레드가 죽은 것을 메인 스레드가 알게 되면, 테스트는 실패한 것으로 표시된다. `panic!` 매스크로를 호출하는 것으로 패닉을 유발할 수 있다. 

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn exploration() {
        assert_eq!(2 + 2, 4);
    }

    #[test]
    fn another() {
        panic!("Make this test fail");
    }
}
```

```
running 2 tests
test tests::exploration ... ok
test tests::another ... FAILED

failures:

---- tests::another stdout ----
    thread 'tests::another' panicked at 'Make this test fail', src/lib.rs:10:8
note: Run with `RUST_BACKTRACE=1` for a backtrace.

failures:
    tests::another

test result: FAILED. 1 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out

error: test failed
```

`cargo test`를 이용해 테스트를 실행시키면, `exploration` 테스트는 통과하고 `another`은 실패했다.

`test tests::another` 라인은 `ok` 대신 `FAILED` 를 보여준다. 개별 결과 부분과 요약 부분 사이에 새로운 두 개의 섹션이 나타난다. 첫번째 섹션은 테스트 실패에 대한 구체적인 이유를 표시한다. 이 경우, `another`은 `panicked at ‘Make this test fail’` 때문에 실패했다. 다음 섹션은 실패한 모든 테스트의 이름만 목록화한 것인데, 이는 테스트들이 많이 있고 구체적인 테스트 실패 출력이 많을 때 유용하다. 실패하는 테스트의 이름은 이를 더 쉽게 디버깅하기 위해서 해당 테스트만을 실행시키는데 사용될 수 있다. 

요약 라인이 가장 마지막에 표시된다. 전체적으로, 테스트 결과는 `FAILED`다. 하나의 테스트를 통과했고, 하나의 테스트에 실패했기 때문이다.

---

### assert! 매크로를 이용하여 결과 확인하기

표준 라이브러리에서 제공하는 `assert!` 매크로는 테스트가 어떤 조건이 `true` 임을 보장하기를 원하는 경우 유용하다. `assert!` 매크로에는 `bool` 타입으로 계산되는 인자가 제공된다. 만일 값이 `true` 라면 `assert!`는 아무일도 하지 않고 테스트는 통과한다. 만일 값이 `false`라면, `assert!`는 `panic!` 매크로를 호출하는데, 이것은 테스트를 실패하게 한다. 이는 코드가 의도대로 기능하고 있는지를 체크하는 것을 도와주는 매크로 중 하나다.

---

`can_hold` 메소드는 `bool` 값을 반환하는데, 이는 `assert!` 매크로를 위한 사용 사례다. 길이 `8` 너비 `7`인 `Rectangle` 인스턴스를 만들고, 이것이 길이 `5`에 너비 `1`인 다른 `Rectangle` 인스턴스를 포함할 수 있는지 단언(assert) 해보는 것으로 테스트를 작성한다.

`tests` 모듈 내에 새로운 라인이 추가된 것을 주목하자. `use super::*;`

`tests` 모듈은 보통의 가시성 규칙을 따르는 일반적인 모듈이다. 우리가 내부 모듈 내에 있기 때문에, 외부 모듈에 있는 코드를 내부 모듈의 스코프로 가져올 필요가 있다. 여기서는 글롭(`*`)을 사용하기로 선택했고 따라서 외부 모듈에 정의한 어떠한 것이든 이 `tests` 모듈에서 사용 가능하다.

이 테스트는 `larger_can_hold_smaller`로 명명되었고, 요구된 바와 같이 `Rectangle` 인스턴스를 두 개 생성했다. 그 뒤 `assert!` 매크로를 호출하고 `larger.can_hold(&smaller)` 호출의 결과값을 인자로서 넘겼다. 이 표현식은 `true`를 반환할 예정이고, 이 테스트는 통과될 것이다.

```rust
#[derive(Debug)]
pub struct Rectangle {
    length: u32,
    width: u32,
}

impl Rectangle {
    pub fn can_hold(&self, other: &Rectangle) -> bool {
        self.length > other.length && self.width > other.width
    }
}
```

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn larger_can_hold_smaller() {
        let larger = Rectangle { length: 8, width: 7 };
        let smaller = Rectangle { length: 5, width: 1 };

        assert!(larger.can_hold(&smaller));
    }
}
```

---

### assert_eq!와 assert_ne!를 이용한 동치(equality) 테스트

기능성을 테스트하는 일반적인 방법은 테스트 내의 코드의 결과값과 기대하는 값을 비교하여 둘이 서로 같은지를 확실히 하는 것이다. 이를 `assert!` 매크로에 `==` 를 이용한 표현식을 넘기는 식으로 할 수도 있다. 그러나 이러한 테스트를 더 편리하게 수행해주는 표준 라이브러리가 제공하는 한 쌍의 매크로 - `assert_eq!`와 `assert_ne!`  가 있다. 이 매크로들은 각각 동치와 부동을 위해 두 인자를 비교한다. 또한 이들은 만일 `assert`에 실패한다면 두 값을 출력해 주는데, 이는 왜 테스트가 실패했는지를 알기 더 쉬워진다. 반면, `assert!`는 `==` 표현식에 대해 `false` 값을 얻었음을 가리킬 뿐, 어떤 값이 `false` 값을 야기 했는지는 알려주지 않는다.

---

`assert_eq!` 매크로에 제공한 첫번째 인자 `4`는 `add_two(2)` 호출 결과와 동일하다. 이 테스트에 대한 라인은 `test tests::it_adds_two … ok` 이고, 이는 테스트가 통과 했음을 나타낸다. 

```rust
pub fn add_two(a: i32) -> i32 {
    a + 2
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn it_adds_two() {
        assert_eq!(4, add_two(2));
    }
}
```

```
running 1 test
test tests::it_adds_two ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

---

만약 `add_two` 함수에 `3`을 더하는 것을 바꾸어서 버그를 유발해보자.

`it_adds_two` 테스트는 `assertion failed: `(left == right)`` 라는 메세지와 `left`는 `4`였고 `right`는 `5`였다는 것을 보여준다. 

몇몇 언어와 테스트 프레임워크 내에서는, 두 값이 같은지를 단언하는 함수의 파라미터를 `expected`와 `actual` 로 부르며, 인자를 넣는 순서가 중요하다. 하지만 Rust는 그 대신 `left`와 `right`라고 불리며 기대한 값과 실제 값을 지정하는 순서는 중요하지 않다. 이 테스트를 `assert_eq!(add_two(2), 4)` 로 작성할 수도 있는데, 이는 `assertion failed: `(left == right)`` 와 `left`는 `5`, `right`는 `4`라는 실패 메세지를 만들어낼 것이다.

---

`assert_ne!` 매크로는 제공한 두 개의 값이 서로 갖지 않으면 통과하고 동일하면 실패할 것이다. 이 매크로는 어떤 값이 될 것인지는 정확히 확신하지 못하지만, 어떤  값은 절대로 될 수 없다는 것을 알고있을 때 유용하다. 

```
running 1 test
test tests::it_adds_two ... FAILED

failures:

---- tests::it_adds_two stdout ----
        thread 'tests::it_adds_two' panicked at 'assertion failed: `(left == right)`
  left: `4`,
 right: `5`', src/lib.rs:11:8

failures:
    tests::it_adds_two

test result: FAILED. 0 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out
```

표면 아래에서, `assert_eq!` 와 `assert_ne!` 매크로는 각각 `==`와 `≠` 연산자를 이용한다. 단언(assert)에 실패하면, 이 매크로들은 디버그 포맷팅을 사용하여 인자들을 출력하는데, 이는 비교되는 값들이 `PartialEq`와 `Debug` 트레잇을 구현해야 한다는 의미다. 모든 기본 타입과 표준 라이브러리가 제공하는 대부분의 타입들은 이 트레잇들을 구현하고 있다. 만약 프로그래머가 정의한 구조체나 열거형에 대해서, 해당 타입의 값이 서로 같은지 혹은 다른지를 단언하기 위해서는 `PartialEq`를 구현할 필요가 있다. 단언에 실패할 경우에 값을 출력하기 위해서는 `Debug`를 구현해야 한다. 두 트레잇 모두 추론 가능한 (`derivable`) 트레잇이기 때문에, 이 트레잇의 구현은 보통 `#[derive(PartialEq, Debug)]` 어노테이션을 프로그래머가 작성한 구조체나 열거형 정의부에 추가하는 정도로 간단하다. 

---

### should_panic을 이용한 패닉에 대한 체크

코드가 기대한 정확한 값을 반환하는 것을 체크하는 것에 더해서, 코드가 기대한 대로 에러가 발생하는 경우를 처리할 수 있는지 체크하는 것도 중요하다. 예를 들면 `Guess`라는 타입을 만들고, 해당 타입이 `1`과 `100`사이의 값만 가질 것이라고 보장한다고 하자. 이 범위 밖의 값으로 `Guess` 인스턴스를 만드는 시도가 패닉을 일으킨다는 것을 테스트로 작성할 수 있다. 

이는 또 다른 속성인 `should_panic`을 테스트 함수에 추가함으로써 할 수 있다. 이 속성은 코드가 패닉을 일으키면 테스트가 통과하도록 만들어준다. 만약 함수 내의 코드가 패닉을 일으키지 않는다면 테스트는 실패할 것이다.

`#[should_panic]` 속성이 `#[test]` 속성 뒤, 그리고 적용될 테스트 함수 앞에 붙어 있다. 이 테스트가 통과될 때의 결과는 다음과 같다.

```rust
running 1 test
test tests::greater_than_100 ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

```rust
pub struct Guess {
    value: u32,
}

impl Guess {
    pub fn new(value: u32) -> Guess {
        if value < 1 || value > 100 {
            panic!("Guess value must be between 1 and 100, got {}.", value);
        }

        Guess {
            value
        }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    #[should_panic]
    fn greater_than_100() {
        Guess::new(200);
    }
}
```

만약 `100` 이상일때 패닉을 발생시키는 조건을 제거해서 오류가 발생하지 않도록 해보자. 

```rust
running 1 test
test tests::greater_than_100 ... FAILED

failures:

failures:
    tests::greater_than_100

test result: FAILED. 0 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out
```

`#[sholud_panic]` 어노테이션을 가지고 해당 함수에서 패닉이 발생하지 않았기 때문에, `FAILED`를 얻는다.

`sholud_panic` 테스트는 애매할 수 있는데, 그 이유는 이 속성이 단지 코드에서 어떤 패닉이 유발되었음만을 알려줄 뿐이기 때문이다. `sholud_panic` 테스트는 일어날 것으로 예상한 것 외의 다른 이유로 인한 패닉이 일어날 지라도 통과할 것이다. `should_panic` 테스트를 더 엄밀하게 만들기 위해서, `sholud_panic` 속성에 `expected` 파라미터를 추가할 수도 있다. 이것은 실패 메세지가 제공된 텍스트를 담고 있는지 확실히 할 것이다. 

오른쪽 테스트는 통과할 것인데, 그 이유는 `sholud_panic` 속성에 추가한 `expected` 파라미터의 값이 실행되었을 때  `else if value > 100` 경우에 발생할 `panic` 메세지에 해당하기 때문이다.

```rust
// --snip

impl Guess {
    pub fn new(value: u32) -> Guess {
        if value < 1 {
            panic!("Guess value must be greater than or equal to 1, got {}.",
                   value);
        } else if value > 100 {
            panic!("Guess value must be less than or equal to 100, got {}.",
                   value);
        }

        Guess {
            value
        }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    #[should_panic(expected = "Guess value must be less than or equal to 100")]
    fn greater_than_100() {
        Guess::new(200);
    }
}
```

---

## 테스트의 실행 방식 제어하기

`cargo test` 명령어는 우리의 코드를 테스트 모드에서 컴파일하고 결과로 발생한 테스트 바이너리를 실행한다. 우리는 커맨드 라인 옵션을 지정하여 `cargo test`의 기본 동작을 변경할 수 있다. 예를 들어, `cargo test`를 통해 생성된 바이너리의 기본 동작은 모든 테스트를 병렬적으로 수행하고 테스트가 실행되는 동안 생성된 결과를 캡쳐하는 것으로, 테스트 결과와 연관된 출력을 읽기 쉽도록 화면에 출력하는 것을 막아버린다.

어떤 커맨드 라인 옵션은 `cargo test`에 입력되고 어떤 옵션은 결과 테스트 바이너리에 입력된다. 이 두 가지 타입의 인자를 구분하기 위해서, `cargo test`에 저우질 인자를 먼저 나열하고, 그 다음 구분자(separator)로 `--` 를 넣고, 그 뒤 테스트 바이너리에 입력될 인자를 나열한다. `cargo test --help` 를 실행하는 것은 `cargo test` 에서 사용할 수 있는 옵션을 표시하고, `cargo test -- --help` 를 실행하는 것은 구분자 `—-` 이후에 나올 수 있는 옵션을 표시한다.

### 테스트를 병렬 혹은 연속으로 실행하기

여러 개의 테스트를 실행할 때는 기본적으로 스레드를 이용하여 병렬적으로 수행된다. 테스트가 동시에 실행되므로, 우리의 테스트가 서로 다른 테스트 혹은 공유 상태 값에 의존하지 않는지 주의해야 한다. 이를테면 현재 작업 디렉토리나 환경 변수와 같은 공유 환경 값을 생각해야 한다.

예를 들면, 만약 작성한 테스트 각각이 `test-output.txt`라는 파일을 디스크에 만들고 이 파일에 어떤 데이터를 쓰는 코드를 실행한다고 가정하자. 그럼 각 테스트는 그 파일로부터 데이터를 읽고, 이 파일이 특정한 값을 담고 있는지 확인하는데, 이 값들은 테스트마다 다르다. 모든 테스트들이 동시에 실행되기 때문에, 어떤 테스트가 파일을 쓰고 읽는 동안 다른 테스트가 파일을 덮어쓸지도 모른다. 두 번째 테스트는 실패할 것인데, 이는 코드가 정확하지 않아서가 아니라 테스트들이 병렬적으로 실행하는 동안 서로에게 간섭을 일으켰기 때문이다. 한 가지 해결책은 각 테스트가 서로 다른 파일을 쓰도록 확실히 하는 것이다. 또 다른 해결책으로는 테스트를 한 번에 하나씩만 실행하는 것이다.

만약 테스트들을 병렬적으로 실행하고 싶지 않을 경우, 혹은 사용되는 스레드의 개수에 대한 정밀한 제어를 하고 싶은 경우, `-—test-threads` 플레그와 테스트 바이너리에서 사용하고 싶은 스레드 개수를 넘길 수 있다.

```
cargo test -- --test-threads=1
```

여기서는 테스트 스레드의 개수를 1로 지정했는데, 이는 프로그램이 어떠한 병렬 처리도 사용하지 않음을 얘기해준다. 테스트를 하나의 스레드에서 실행하는 것은 병렬로 수행하는 것에 비해 시간이 더 오래 걸리겠지만, 테스트들이 어떤 상태를 공유할 경우 서로가 간섭할 가능성이 없어질 것이다.

---

### 함수 결과 보여주기

기본적으로 테스트가 통과하면, Rust의 테스트 라이브러리는 표준 출력(standard output)으로 출력되는 어떤 것이든 캡쳐한다. 예를 들면, 우리가 테스트 내에서 `println!`을 호출하고 이 테스트가 통과하면, `println!` 출력을 터미널에서 볼 수 없다. 우리는 그저 그 테스트가 통과되었다고 표시된 라인만 볼 수 있다. 만약 테스트가 실패하면, 실패 메세지 아래에 표준 출력으로 출력되었던 어떤 것이든 볼 수 있다.

```rust
fn prints_and_returns_10(a: i32) -> i32 {
    println!("I got the value {}", a);
    10
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn this_test_will_pass() {
        let value = prints_and_returns_10(4);
        assert_eq!(10, value);
    }

    #[test]
    fn this_test_will_fail() {
        let value = prints_and_returns_10(8);
        assert_eq!(5, value);
    }
}
```

```
running 2 tests
test tests::this_test_will_pass ... ok
test tests::this_test_will_fail ... FAILED

failures:

---- tests::this_test_will_fail stdout ----
        I got the value 8
thread 'tests::this_test_will_fail' panicked at 'assertion failed: `(left == right)`
  left: `5`,
 right: `10`', src/lib.rs:19:8
note: Run with `RUST_BACKTRACE=1` for a backtrace.

failures:
    tests::this_test_will_fail

test result: FAILED. 1 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out
```

---

위 코드의 에러메세지에서 `I got the value 4`라는 메세지를 어디에서도 볼 수 없는데, 이는 성공하는 테스트가 실행시키는 출력이라는 점을 주목하자. 이 출력 메세지는 성공한테스트이기 때문에 에러메세지에 등장하지 않는다. 반대로 실패한 테스트로부터 얻어진 출력 메세지인 `I got the value 8`은 테스트 정리 출력 부분에 나타나는데, 이는 테스트 실패 원인 또한 같이 보여준다.

만약 성공하는 테스트에 대한 출력 값을 보고 싶다면, `-—nocapture` 플래그를 이용하여 출력 캡처 동작을 비활성화시킬 수 있다.

```
$ cargo test -- --nocapture
```

테스트에서 출력과 테스트 결과 출력 부분이 분리되었다. 이는 이전 절에서 다룬 내용처럼 테스트가 병렬적으로 수행되기 때문이다. 

```
running 2 tests
I got the value 4
I got the value 8
test tests::this_test_will_pass ... ok
thread 'tests::this_test_will_fail' panicked at 'assertion failed: `(left == right)`
  left: `5`,
 right: `10`', src/lib.rs:19:8
note: Run with `RUST_BACKTRACE=1` for a backtrace.
test tests::this_test_will_fail ... FAILED

failures:

failures:
    tests::this_test_will_fail

test result: FAILED. 1 passed; 1 failed; 0 ignored; 0 measured; 0 filtered out
```

---

### 이름으로 테스트의 일부분만 실행하기

가끔, 모든 테스트를 실행하는 것은 시간이 오래 걸릴 수 있다. 만일 특정 영역의 코드에 대해서 작업하고 있다면, 그 코드와 연관된 테스트만 실행시키고 싶어 할 수도 있다. `cargo test`에 실행시키고 싶어 하는 테스트들의 이름들을 인자로 넘김으로써 어떤 테스트를 실행시킬지 고를 수 있다.

오른쪽 코드를 아무런 인자 없이 테스트를 실행시킨 결과이다. 전에 본 것과 같이 모든 테스트가 병렬적으로 수행된다.

```
running 3 tests
test tests::add_two_and_two ... ok
test tests::add_three_and_two ... ok
test tests::one_hundred ... ok

test result: ok. 3 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

---

단 하나의 테스트만을 실행시키기 위해 `cargo test`에 그 테스트 함수의 이름을 넘길 수 있다.

```
$ cargo test one_hundred
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running target/debug/deps/adder-06a75b4a1f2515e9

running 1 test
test tests::one_hundred ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 2 filtered out
```

`one_hundread` 라는 이름의 테스트만 실행되었다. 테스트 출력은 정리 라인의 끝에 `2 filtered out` 이라고 표시함으로써 이 커멘드로 지정한 것보다 2개 더 많은 테스트를 가지고 있음을 알려준다. 하지만 이 방법으로는 여러 테스트의 이름들을 특정할 수는 없고, 제일 첫 번째 값만 사용한다.

```rust
pub fn add_two(a: i32) -> i32 {
    a + 2
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn add_two_and_two() {
        assert_eq!(4, add_two(2));
    }

    #[test]
    fn add_three_and_two() {
        assert_eq!(5, add_two(3));
    }

    #[test]
    fn one_hundred() {
        assert_eq!(102, add_two(100));
    }
}
```

---

그렇다면 여러개의 테스트 이름을 지정하는 방법도 있다. 테스트 이름의 일부분을 인자로 넘기면 해당 테스트를 특정할 수 있고, 해당 값과 일치하는 이름의 테스트가 실행될 것이다. 예를 들면 테스트 이름들 중 두 개가 `add`를 포함하므로, `cargo test` `add`라고 실행하면 이 두 개의 테스트를 실행시킬 수 있다.

이는 `add`가 이름에 포함된 모든 테스트를 실행시켰고 `one_hundred`라는 이름의 테스트를 걸러냈다. 또한 테스트가 있는 모듈이 테스트의 이름의 일부가 된다. 따라서 모듈의 이름으로 필터링하여 그 모듈 내의 모든 테스트를 실행시킬 수도 있다.

```
$ cargo test add
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running target/debug/deps/adder-06a75b4a1f2515e9

running 2 tests
test tests::add_two_and_two ... ok
test tests::add_three_and_two ... ok

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 1 filtered out
```

---

### 특별한 요청이 없는 한 몇몇 테스트들 무시하기

가끔 몇몇 특정 테스들은 실행하는데 너무나 시간이 많이 소모될 수 있어서, `cargo test`의 실행 시 이 테스트들을 배제하고 싶을 수도 있다. 실행시키고자 하는 모든 테스트들을 인자로서 열거하는 것 대신, 오른쪽과 같이 `ignore` 속성을 어노테이션하여 이들을 배제시킬 수 있다.

```
$ cargo test
   Compiling adder v0.1.0 (file:///projects/adder)
    Finished dev [unoptimized + debuginfo] target(s) in 0.24 secs
     Running target/debug/deps/adder-ce99bcc2479f4607

running 2 tests
test expensive_test ... ignored 
test it_works ... ok

test result: ok. 1 passed; 0 failed; 1 ignored; 0 measured; 0 filtered out
```

만약  `ignored`된 테스트들만 실행시키고 싶다면, `cargo test —- —-ignored` 라고 실행함으로써 이를 요청할 수 있다. 

배제시키고 싶은 테스트에 대해서 `#[test]` 다음 줄에 `#[ignore]` 를 추가 했다. 이제 테스트를 실행시키면 `expensive_test`는 실행되지 않는 것을 볼 수 있다.

```rust
#[test]
fn it_works() {
    assert_eq!(2 + 2, 4);
}

#[test]
#[ignore]
fn expensive_test() {
    // code that takes an hour to run
}
```

---

## 테스트 조직화

테스팅은 복잡한 분야고, 여러 사람들이 서로 다른 용어와 조직화 방식을 이용한다. Rust 커뮤니티에서는 테스트에 대해서 두 개의 주요한 카테고리로 나눠 생각한다. 하나는 단위 테스트(unit test) 그리고 나머지는 통합 테스트(integration test)다. 단위 테스트는 작고 하나에 더 집중하며, 한 번에 하나의 모듈만 분리해 테스트하고, 비공개 인터페이스 (private interface)를 테스트한다. 통합 테스트는 완전히 우리의 라이브러리 외부에 있으며, 공개 인터페이스 (public interface)를 이용하고 테스트마다 여러 개의 모듈을 잠재적으로 실험함으로써, 다른 외부의 코드가 하는 방식과 동일한 형태로 우리의 코드를 이용한다.

### 단위 테스트

단위 테스트의 목적은 각 코드의 단위를 나머지 부분과 분리하여 테스트 하는것이다. 이는 코드가 어디 있고 어느 부분이 기대한 대로 동작하지 않는지를 빠르게 정확하 찾아낼 수 있다. 단위 테스트는 `src` 디렉토리 내에 넣는데, 각 파일마다 테스트하는 코드를 담고 있다. 관례는 각 파일마다 테스트 함수를 담고 있는 `tests`라는 이름의 모듈을 만들고, 이 모듈에 `cfg(test)`라고 어노테이션 하는 것이다.

**테스트 모듈과 `#[cfg(test)]`**

테스트 모듈 상의 `#[cfg(test)]` 어노테이션은 우리가 `cargo test`를 실행시킬 대에만 컴파일하고 실행시킨다. 이는 우리가 오직 라이브러리만을 빌드하고 싶을 때 컴파일 시간을 절약하고, 컴파일 결과물의 크기를 줄여준다. 통합 테스트는 다른 디렉토리에 위치하기 때문에, 여기에는 `#[cfg(test)]` 어노테이션이 필요치 않음을 앞으로 보게 될 것이다. 하지만 단위 테스트가 해당 코드와 동일한 파일에 위치하기 때문에, `#[cfg(test)]`를 사용하여 컴파일 결과물에 이들이 포함되지 않아야 함을 알려줘야 한다.

처음 예시로 들었던 `it_works` 함수다.

```rust
#[cfg(test)]
mod tests {
    #[test]
    fn it_works() {
        assert_eq!(2 + 2, 4);
    }
}
```

`cfg`속성은 환경 설정(configuration)을 의미하며, 뒤따르는 아이템이 특정한 환경 값에 대해서만 포함되어야 함을 의미한다. 위의 경우, 환경 값이 `test`인데, 테스트를 컴파일하고 실행하기 위해 Rust로부터 제공되는 것이다. 이 속성을 이용함으로써, `cargo`는 우리가 능동적으로 `cargo test`를 이용해서 테스트를 실행시킬 경우에만 테스트 코드를 컴파일 한다.

**비공개 함수 테스트 하기**

테스팅 커뮤니티 내에서 비공개 함수가 직접적으로 테스트되어야지에 대한 논쟁이 있었고, 다른 언어들은 비공개 함수를 테스트하는 것이 어렵거나 불가능하게 만들어 두었다. 하지만  Rust의 비공개 규칙은 우리의 비공개 함수를 테스트하도록 허용해 준다. 비공개 함수 `internal_adder`가 있는 다음 코드를 보자.

```rust
pub fn add_two(a: i32) -> i32 {
    internal_adder(a, 2)
}

fn internal_adder(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn internal() {
        assert_eq!(4, internal_adder(2, 2));
    }
}
```

`internal_Adder` 함수는 `pub`로 표시되어 있지 않지만, 테스트가 그저 Rust 코드일 뿐이고 `tests` 모듈도 그냥 또 다른 모듈이기 때문에, `internal_adder` 를 불러들여 호출하는 것이 가능하다. 만약 비공개 함수를 테스트해야 한다고 생각하지 않는다면, Rust가 그렇게 하도록 강제할 일은 없다.

---

### 통합 테스트

Rust에서 통합 테스들은 완전히 우리의 라이브러리 외부에 있다. 이들은 우리의 라이브러리를 다른 코드와 동일한 방식으로 이용하는데, 이는 이 외부 테스트들이 오직 우리의 라이브러리의 공개 API 부분에 속하는 함수들만 호출할 수 있다는 의미다. 이들의 목적은 우리의 라이브러리의 수많은 파트들이 올바르게 동작하는지를 시험하는 것이다. 그 자체로서는 올바르게 동작하는 코드의 단위들도 통합되었을 때는 문제를 일으킬 수 있으므로, 통합된 코드의 테스트 커버율도 중요하다. 통합 테스트를 만들기 위해서는 `tests` 디렉토리를 먼저 만들어야 한다.

**tests 디렉토리**

프로젝트 디렉토리의 최상위, 그러니까 `src` 옆에 `tests` 디렉토리를 만든다. `cargo`는 이 디렉토리 내의 통합 테스트 파일들을 찾는다. 그런 후에는 이 디렉토리에 원하는 만큼 많은 테스트 파일을 만들 수 있으며, `cargo`는 각각의 파일들을 개별적인 크레이트처럼 컴파일할 것이다. 통합 테스트를 만들어 보자.

 `src/lib.rs` 코드를 그대로 유지한 채로, `tests` 디렉토리를 만들고, `tests/integration_test.rs`라는 이름의 새 파일을 만들자.

```rust
extern crate adder;

#[test]
fn it_adds_two() {
    assert_eq!(4, adder::add_two(2));
}
```

코드의 상단에  `extern crate adder`를 추가했는데, 이는 단위 테스트에서는 필요가 없다. 이는 `tests` 디렉토리 내의 각 테스트가 모두 개별적인 크레이트라서, 라브리러리를 각각에 가져올 필요가 있기 때문이다.

`tests/integration_test.rs` 에는 `#[cfg(test)]` 어노테이션을 해줄 필요각 없다. `cargo` 는 `test` 디렉토리를 특별 취급하여 `cargo test`를 실행시켰을 때에만 이 디렉토리 내의 파일들을 컴파일 한다. 

```
$ cargo test
   Compiling adder v0.1.0 (file:///projects/adder)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running target/debug/deps/adder-abcabcabc

running 1 test
test tests::internal ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

     Running target/debug/deps/integration_test-ce99bcc2479f4607

running 1 test
test it_adds_two ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests adder

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

---

테스트 결과를 보면 세 개의 섹션이 있다. 단위테스트, 통합 테스트, 그리고 문서 테스트다. 단위 테스트를 위한 첫 번째 섹션은 원래 보아오던 것과 동일하다. 각각의 단위 테스트마다 한 라인, 그다음 단위 테스트들의 정리 라인이 있다.

통합 테스트 섹션은 `Running target/debug/deps/integration_test-ce99bcc2479f4607` 라고 말하는 라인과 함께 시작된다. 그 다음 이 통합 테스트 안의 각 테스트 함수를 위한 라인이 있고, `Doc-tests adder` 섹션이 시작되기 직전에 통합 테스트의 결과를 위한 정리 라인이 있다.

어떠한 `src` 파일에 단위 테스트 함수를 더 추가하는 것이 단위 테스트 섹션의 테스트 결과 라인을 더 늘린다는 것을 기억하자. 통합 테스트 파일에 테스트 함수를 더 추가하는 것은 그 파일의 섹션의 라인을 더 늘릴 것이다. 각 통합 테스트 파일은 고유의 섹션을 가지고 있으므로, 만약 `tests` 디렉토리에 파일을 더 추가하면, 통합 테스트 섹션이 더 생길 것이다.

`cargo test`의 인자로서 테스트 함수의 이름을 명시하는 식으로 특정 통합 테스트 함수를 실행시키는 것도 여전히 가능하다. 

```
$ cargo test --test integration_test
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
     Running target/debug/integration_test-952a27e0126bb565

running 1 test
test it_adds_two ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

---

통합 테스트 내의 서브모듈

더 많은 통합 테스트를 추가하게 되면, 원활한 조직화를 위해 `tests` 디렉토리 내에 하나 이상의 파일을 만들고 싶을수도 있다. 예를 들면, 우리는 테스트하는 기능별로 테스트 함수들을 묶을 수도 있다. 앞선 말했듯이, `tests` 디렉토리 내의 각 파일은 고유의 개별적인 크레이트인 것처럼 컴파일 된다.

각 통합 테스트 파일을 고유한 크레이트인 것 처럼 다루는 것은 우리의 크레이트를 이용하게 될 사용자들의 방식과 더 유사하게 분리된 스코프를 만들어 내기에 유용하다. 하지만  이는 `src` 내의 파일들이 동일한 동작을 공유하는 것을 `tests` 디렉토리 내의 파일들에서는 할수 없음을 의미한다.

만일 우리가 여러 개의 통합 테스트 파일들 내에서 유용하게 사용될 헬퍼 함수들 묶음을 가지고 있으며, 이들을 공통 모듈로 추출할 수도 있다. 이를테면 `tests/common.rs` 라는 파일을 만들어서 그 다음과 같이 `setup` 이라는 이름의 함수를 위치시키고, 여기에 여러 테스트 파일들 내의 함수로부터 호출될 헬퍼함수를 집어넣는다고 해보자.

```rust
pub fn setup() {
    // 여러분의 라이브러리 테스트에 특화된 셋업 코드가 여기 올 것입니다
}
```

만약 테스트를 다시 실행시키면, 비록 이 코드가 어떠한 테스트 함수도 담고 있지 않고, `setup` 함수를 다른 어딘가에서 호출하고 있지 않을지라도, `common.rs` 파일을 위한 테스트 출력 내의 새로운 섹션을 보게 될 것이다.

```
running 1 test
test tests::internal ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

     Running target/debug/deps/common-b8b07b6f1be2db70

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

     Running target/debug/deps/integration_test-d993c68b431d39df

running 1 test
test it_adds_two ... ok

test result: ok. 1 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out

   Doc-tests adder

running 0 tests

test result: ok. 0 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out
```

---

`common`이 결과 출력에서 나타나는 것을 막기 위해서는, `tests/common.rs` 를 만드는 대신, `tests/common/mod.rs`를 만들 수 있다. 앞서 서브 모듈을 가지고 있는 모듈의 파일들을 위해 `module_name/mod.rs`라는 이름 규칙을 이용했었고, 여기서 `common`에 대한 서브모듈을 가지고 있지는 않지만, 이러한 방식으로 파일명을 정하는 것이 Rust에게 `common` 모듈을 통합 테스트 파일로 취급하지 않게끔 전달한다. `setup` 함수 코드를 `tests/common/mod.rs`로 옮기고 `tests/common.rs`파일을 제거하면, 테스트 출력에서 해당 섹션이 나타나지 않을 것이다. `tests` 디렉토리의 서브 디렉토리 내의 파일들은 개별적인 크레이트처럼 컴파일되지도, 테스트 출력의 섹션을 갖지도 않는다.

`tests/common/mod.rs`를 만든 뒤에는, 어떤 통합 테스트 파일에서라도 이를 모듈처럼 쓸 수 있다. 다음 예제는 `tests/integration_test.rs` 내의 `it_adds_two` 테스트로부터 `setup` 함수를 호출하는 예시다.

```rust
extern crate adder;

mod common;

#[test]
fn it_adds_two() {
    common::setup();
    assert_eq!(4, adder::add_two(2));
}
```

---

만약 프로젝트가  `src/lib.rs` 파일이 없고 `src/main.rs` 파일만 갖고 있는 바이너리 프로젝트라면, `tests` 디렉토리 내에 통합 테스트를 만들어서 `src/main.rs`에 정의된 함수를 가져오기 위하여 `extern crate`를 이용할 수 없다. 오직 라이브러리 크레이트만 다른 크레이트에서 호출하고 사용할 수 있는 함수를 노출시킨다. 바이너리 크레이트는 그 스스로 실행될 것이다.

이는 바이너리를 제공하는 Rust 프로젝트들인 `src/lib.rs`에 위치한 로직을 호출하는 간단한 형태의 `src/main.rs`를 가지고 있는 이유 중 하나다. 이러한 구조와 함께라면, `extern crate`를 이용해 중요한 기능들을 커버하도록 하기 위해 통합 테스트가 라이브러리 크레이트를 테스트할 수 있다. 만일 중요 기능이 작동한다면, `src/main.rs`내의 소량의 코드  또한 동작할 것이고, 이 소량의 코드는 테스트할 필요가 없다.

---

# I/O 프로젝트 커맨드 라인 프로그램 만들기

Rust는 성능, 안전성, ‘단일 바이너리’로 출력, 그리고 교차 플랫폼 지원으로 커맨드 라인 툴을 제작하기 좋은 언어다. 커맨드 라인 툴 `grep` 을 만들어보자.

---

## 커맨드라인 인자 허용하기

첫 번째 작업은 `greprs`가 두 개의 커맨드 라인 인자를 받는 것이다. 하나는 파일이름, 나머지는 검색할 문자다. `cargo run` 을 통해 프로그램을 실행시킬 때, 검색할 문자와 검색할 파일의 경로를 사용할 수 있도록 하고자 한다.

```
cargo run searchstring example-filename.txt
```

`cargo new` 를 통해 생성된 프로그램은 입력한 인자를 모두 무시한다. `crates.io` 라이브러리에 커맨드라인 인자들을 받아들일수 있는 라이브러리가 이미 존재하지만, 따로 구현해보자.

---

### 인자값 받아들이기

프로그램에 전달되는 커맨드라인 인자의 값을 얻으려면 Rust의 표준 라이브러리에서 제공하는 함수를 호출해야 한다. `std::env::args.` 이 함수는 `반복자(iterator)` 형식으로 커맨드라인 인자들을 우리 프로그램에 전달해준다. 아직 반복자에 대해 다루지 않았고, 두 가지 성질만 기억하자.

1. 반복자는 하나의 연속된 값을 생성한다
2. 반복자에 `collect`함수를 호출을 통해 벡터로 변환할 수 있다.

```rust
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();
    println!("{:?}", args);
}
```

---

가장 먼저, `std::env` 모듈을 `use`를 통해 모듈 범위 내로 가져와서 `args`함수를 호출한다. 

> 어떤 인자가 잘못된 유니코드를 포함하고 있으면 `std::env::args`는 패닉을 발생한다. 유효하지 않은 유니코드를 포함한 인자를 허용해야 하는 경우에는 `std::env::args_os`를 사용하자. 이는 `String`대신 `OsString` 값을 반환한다. `OsString` 값은 플랫폼마다 다르며, `String` 값보다 다루기 더 복잡하기 때문에 여기서는 `std::env::args`를 사용하여 좀 더 단순화 한다.
> 

`main`의 첫 번째 줄에서, `args`함수와 `collect`함수를 이용해 반복자가 가진 모든 값들을 벡터 형태로 변환하여 반환한다. `collect` 함수는 많은 종류의 콜렉션들과 사용될 수 있기 때문에, 원하는 타입이 `Vec<String>` 이라고 명시한다. Rust에서 타입 명시를 할 필요는 거의 없지만, Rust는 원하는 콜렉션의 타입을 추론 할  수 없기 때문에 `collect`는 타입을 명시할 필요가있는 함수 중 하나다.

```rust
$ cargo run
["target/debug/greprs"]

$ cargo run needle haystack
...snip...
["target/debug/greprs", "needle", "haystack"]
```

벡터의 첫 번째 값은 바이너리의 이름인 “`target/debug/greprs”`이다. 메세지를 인쇄하거나 프로그램을 호출하는 데 사용 된 명령 줄별칭을 기반으로 프로그램의 동작을 변경하려는 경우 프로그램 이름에 액세스하는 것이 편리하지만, 여기서는 무시하고 필요로 하는 두 개의 인자만 저장한다.

---

### 변수에 인자 값들 저장하기

```rust
use std::env;

fn main() {
    let args: Vec<String> = env::args().collect();

    let query = &args[1];
    let filename = &args[2];

    println!("Searching for {}", query);
    println!("In file {}", filename);
}
```

벡터를 출력했을 때 봤듯이, 프로그램의 이름이 벡터의 첫 번째 값으로 `args[0]`에 저장되어 있으니, `1`번째 인자부터 인덱싱하면된다. `greprs`의 첫 번째 인자는 검색하고자 하는 문자열이므로, `query`에 저장한다. 두 번째 인자는 파일이름이니 두 번째 인자의 참조자를 변수 `filename`에 저장한다.

---

## 파일 읽기

이제 커맨드 라인 인자 파일이름으로 지정된 파일을 읽을 것이다. 먼저, 다음은 테스트 파일을 프로젝트 최상위에 생성한다.

```
I'm nobody! Who are you?
Are you nobody, too?
Then there's a pair of us — don't tell!
They'd banish us, you know.

How dreary to be somebody!
How public, like a frog
To tell your name the livelong day
To an admiring bog!
```

`use`를 이용해 파일 관련하여 표준 라이브러리에서 필요한 것을 가져온다. `std::fs::File`과, 파일 I/O를 포함한 I/O 작업을 위해 유용한 특성이 있는 `std::io::prelude::*`이 필요하다.

`main`에서, `File::open` 함수를 호출하고 `filename` 값을 전달하여 파일을 변경할 수 있는 핸들을 얻는다. 그리고 `contents`라는 빈 `String` 가변 변수를 만든다. 이 변수에 읽어들일 내용을 보관하기 위한 용도다. 마지막으로 `read_to_string`을 호출하여 가변 참조를 `contents`의 인자를 전달한다.

```rust
use std::env;
use std::fs::File;
use std::io::prelude::*;

fn main() {
    let args: Vec<String> = env::args().collect();

    let query = &args[1];
    let filename = &args[2];

    println!("Searching for {}", query);
    println!("In file {}", filename);

    let mut f = File::open(filename).expect("file not found");

    let mut contents = String::new();
    f.read_to_string(&mut contents).expect("Something went wrong reading the file");

    println!("With text:\n{}", contents);
}
```

---

## 모듈성과 에러처리의 향상을 위한 리팩토링

프로그램을 향상시키기 위해 네 가지 문제가 존재한다. 이들은 모두 프로그램을 구조화하고 발생가능한 에러를 처리하는 방식과 관련있다.

첫 번째, `main` 함수는 두 가지 작업을 수행한다. 인자들을 분석하고 파일을 여는 작업을 한다. 이런 작은 함수에서 이것은 큰 문제가 되지 않지만 계속해서  함수 안에 프로그램을 작성하여 커지게 되면,  함수가 처리하는 작업의 수도 늘어나게 된다. 함수가 갖게 되는 책임들만큼, 원인을 파악하기도, 테스트 하기에도, 부분별로 나누지 않고는 수정하기 어려워진다. 함수는 나뉘어 하나의 작업에 대해서만 책임을 지는 것이 더 좋은 구조다.

두 번째, query와 filename은 프로그램의 설정을 저장하는 변수고 f와 contents 같은 변수는 프로그램의 논리 수행에 사용된다. main이 길어질수록 범위 내에 더 많은 변수가 생기게 된다. 범위 내에 더 변수가 존재할수록, 각각의 변수를 추적하기 힘들어진다. 목적을 분명히 하기 위해 설정 변수를 그룹화하여 하나의 구조로 결합시키는 것이 좋다.

---

세 번째, 파일 열기가 실패 할 경우 expect를 이용하여 오류 메시지를 출력해주는데, 에러 메세지가 Something went wrong reading the file 밖에 없다. 파일이 존재하지 않는 경우 외에도 파일 열기가 실패하는 경우들이 있다. 예를 들어 파일은 존재하지만 파일을 열 수 있는 권한이 없을 수 있다. 

네 번째, 서로 다른 오류를 다루기 위해  expect를 반복적으로 사용하고 있다. 만약 사용자가 충분한 인수를 지정하지 않고 프로그램을 실행하면 Rust의 “index out of bouds” 오류가 발생하는데 이는 문제를 명확하게 설명하지 않는다. 모든 오류처리 코드를 한 군데 모아놓으면 후에 관리자는 오류처리 로직을 변경해야 할 때 오직 이 곳의 코드만 참고하면 된다. 또한 모든 오류 처리 코드를 한 곳에 저장하면 최종 사용자에게 도움이 되는 메시지를 출력하고 있는지 확신하는데 도움이 된다.

---

### 바이너리 프로젝트를 위한 관심사의 분리

main 함수가 여러 작업에 책임을 갖게 되는 구조적 문제는 많은 바이너리 프로젝트에서 공통적이다. 그래서 Rust 커뮤니티는 main이 커지기 시작할 때 바이너리 프로그램의 핵심기능을 나누기 위한 가이드라인 프로세스를 개발했다. 

이 패턴이 핵심 기능을 분리하는데 관한 모든 것이다. main.rs는 프로그램 실행을 담당하고, lib.rs는 맡은 작업에 관한 로직을 담당한다. main 함수는 직접 테스트 할 수 없지만, 이런 구조로 lib.rs으로 프로그램의 모든 함수와 로직을 옮긴 후에는 테스트가 가능해진다.

1. 프로그램을 main.rs와 lib.rs로 나누고 프로그램의 로직을 lib.rs로 옮긴다.
2. 커맨드라인 파싱 로직이 크지 않으면, main.rs에 남겨둬도 된다.
3. 커맨드라인 파싱 로직이 복잡해지기 시작할 것 같다면, lib.rs로 옮기자.
4. 이런 절차를 통해 main함수에는 다음 기능들만 남아있어야 한다.
    1. 인자 값들로 커맨드라인을 파싱하는 로직 호출
    2. 다른 환경들 설정
    3. [lib.rs](http://lib.rs)의 run 함수 호출
    4. run이 에러를 리턴하면, 에러 처리.

---

### 인자 파서의 추출

커맨드라인 인자를 분석하는 기능을 추출해보자. `main`의 시작 부분이 새로운 함수 `parse_config`를 호출한다.

아직 커맨드라인 인자들을 벡터로 수집하고 있는데, 인덱스 1의 인수 값을 변수 `query`에, 인덱스 2의 인수 값을 `main` 함수 내의 변수 `filename`에 할당하는 대신에 전체 벡터를 `parse_config` 함수로 전달한다.

`parse_config` 함수는 어디에 위치한 인자가 어떤 변수에 대입 되는지에 대한 로직을 보유하고, 그 값들을 `main`으로 되돌려 준다. 여전히 `query`와 `filename` 변수를 `main`에 생성하지만, `main`은 더 이상 커맨드라인 인자와 변수간의 상관 관계를 책임지지도 알아야 할 필요도 없다.

```rust
fn main() {
    let args: Vec<String> = env::args().collect();

    let (query, filename) = parse_config(&args);

    // ...snip...
}

fn parse_config(args: &[String]) -> (&str, &str) {
    let query = &args[1];
    let filename = &args[2];

    (query, filename)
}
```

---

### 설정 변수들을 그룹짓기

현재 `parse_config`는 튜플을 반환하고 있는데, 그 시점에 즉시 튜플을 개별된 부분으로 나눌 수 없다. 이는 아직은 제대로 된 추상화를 하지 못하고 있는 신호다. 이 두 개의 값은 관련되어 있으며 모두 하나의 설정 값에 대한 부분이다. 현재 두 값을 튜플로 그룹화하는 것 이외의 다른 의미를 전달하지 않는다. 두 값을 하나의 구조체에 넣고 각 구조체 필드에 의미있는 이름을 지정할 수 있다. 이렇게 하면 이 코드의 향후 유지 보수 담당자가 서로 다른 값이 서로 어떻게 관련되어 있고 그 목적이 무엇인지 쉽게 이해할 수 있다. 

> 어떤 사람들은 복합 타입(complex type)이 더 적절한 경우에도 기본 타입(primitive type)을 사용하는데 이러한 안티 패턴을 강박적 기본타입 사용(primitive obsession) 라 부른다.
> 

---

이제 `parse_config`의 선언은 `Config`를 반환한다. `parse_config`의 내부에서는 `args`의 `String` 값을 참조하는 문자열 조각을 반환했었지만,  이제는 `Config`를 정의하고 자체 `String`의 값을 포함하도록 했다. `main`의 `args` 변수는 인자 값을 그저 `parse_config`에 대여해줄 뿐이다. 그렇기에 만약 `Config`가 `args`의 값들에 대한 소유권을 가지려고 시도하면 Rust의 대여 규칙을 위반하게 된다.

`String` 데이터를 관리하는 방식은 여러가지가 있겠다만, 가장 쉽고 비효율적인 방법은 `clone` 메소드를 호출하는 것이다. 이 방식은 `Config` 객체에서 소유하게 할 `data` 전체에 대한 복사본을 만들 것이며, 이런 방식은 참조만 보관하는 것에 비해 약간 더 많은 비용과 메모리가 소비된다. 하지만 데이터의 복제본을 만드는 것은 참조의 생명주기를 관리하지 않아도 되기 때문에 코드를 매우 직관적이게 한다. 따라서 이런 상황에서는 약간의 성능을 포기하고 간소함을 유지하는 것이 가치있는 거래다.

```rust
fn main() {
    let args: Vec<String> = env::args().collect();

    let config = parse_config(&args);

    println!("Searching for {}", config.query);
    println!("In file {}", config.filename);

    let mut f = File::open(config.filename).expect("file not found");

    // ...snip...
}

struct Config {
    query: String,
    filename: String,
}

fn parse_config(args: &[String]) -> Config {
    let query = args[1].clone();
    let filename = args[2].clone();

    Config { query, filename }
}
```

---

### Config를 위한 생성자 만들기

이제 `parse_config` 함수의 목적은 `Config` 객체를 생성하는 것이다. 하지만 이는 `Config` 구조체와 관련된 `new`라는 함수로 변경할 수 있다. 이런 변경은 코드를 보다 자연스럽게 만들어 준다. `String::new`를 호출하여 `String`형의 객체를 생성하는 것처럼 표준 라이브러리들의 객체를 생성할 수 있다. 

---

```rust
fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::new(&args);

    // ...snip...
}

// ...snip...

impl Config {
    fn new(args: &[String]) -> Config {
        let query = args[1].clone();
        let filename = args[2].clone();

        Config { query, filename }
    }
}
```

---

### 에러 메세지 향상시키기

`new` 함수에 검사를 추가해서 커맨드라인에 두 개 이상의 인자가 들어오는지 확인한다. 더 많은 인자가 들어온다면 `“index out of bouds"`를 보여주고 패닉을 일으킨다.

만약 `args`가 3개보다 적은 아이템을 가진다면, `panic!` 매크로를 호출해 프로그램을 즉시 종료시킨다.

```rust
// ...snip...
fn new(args: &[String]) -> Config {
    if args.len() < 3 {
        panic!("not enough arguments");
    }
    // ...snip...
```

---

이번에는 `Result`를 반환 값으로 선택해 성공인 경우에는 `Config` 객체를 포함시키고, 에러가 발생하는 경우에는 문제가 무엇인지 설명할 수 있도록 하자. `Config::new`가 `main`과상호작용할 시에, `Result`를 반환해서 문제가 있다면 알려줄 수 있다. 그래서 `main`에서 `Err`의 값을 사용자들에게 보다 실용적인 방식으로 변환하여 보여줄 수 있다. 

`new` 함수는 성공 시 `Config`, 에러 시에는 `&’static str`이 포함된 `Result`를 반환한다. `Err`값을 반환하고 `Config`를 반환할 때는 `Ok`로 포장해 반환한다. 이런 변경으로 인해 함수는 새로운 타입 선언을 갖게 된다.

`Config::new`가 `Err`값을 반환하게 함으로써, `main` 함수는 `new` 함수로부터 반환된 `Result` 값을 처리하고 에러 상황에 프로세스를 더 깨끗하게 종료 할 수 있다.

```rust
impl Config {
    fn new(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("not enough arguments");
        }

        let query = args[1].clone();
        let filename = args[2].clone();

        Ok(Config { query, filename })
    }
}
```

---

### Config::new를 호출하고 에러 처리하기

이제 `main`함수는 `Config::new`가 리턴하는 `Result`를 처리해야 한다. `0`이 아닌 종료 값은 우리 프로그램을 호출한 프로그램에게 프로그램이 에러 상태로 종료되었음을 알리는 규칙이다.

`unwrap_or_else`는 표준 라이브러리에 의해 Result<T, E>에 정의되어 있다. `unwrap_or_else`를 사용하면 `panic!`이 아닌 에러 처리를 직접 정의 할 수 있다. `Result`가 `Ok`값이면, 이 메소드의 동작은 `unwrap`과 유사하게 `Ok`로 포장한 내부 값을 반환한다. 그러나 `Err`값이면 `*closure*` 코드를 호출한다. `*closure*`는 익명 함수로 `unwrap_or_else`에 인수로 전달된다. `unwrap_or_else`가 `Err`의 내부 값, 여기서는 정적 문자열인 `“not enough arguments"`를 수직파이프 사이에 위치하는 `err`로 인자로서 클로저를 전달한다. 클로정레 있는 코드는 이런 과정을 거쳐 실행시에 `err` 값을 사용할 수 있다.

아본앤 `use`를 통해 `process`를 공유 라이브러리에서 `import` 했다. 에러 상황에 실행될 클로저 함수는 에러 값을 출력해주고 `process::exit`를 호출한다. `process::exit`함수는 프로그램을 즉시 중단시키고 종료 상태 코드로 전달받은 값을 반환한다. 

```rust
use std::process;

fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::new(&args).unwrap_or_else(|err| {
        println!("Problem parsing arguments: {}", err);
        process::exit(1);
    });

    // ...snip...
```

---

### run 함수 추출하기

이제 프로그램의 로직으로 돌아가보자. main 함수에 구성 설정 또는 오류 처리와 관계 없는 남아있는 모든 로직들을 담고 있는 run 함수를 추출하자. 이 과정이 종료되면, main은 간결해져 쉽게 검증할 수 있어지고, 다른 모든 로직에 대한 테스트를 작성할 수 있다.

---

이제 run 함수에는 main에 잔존하는 파일을 읽는 것부터 나머지 모든 로직이 포함된다. run 함수는 Config 객체를 인수로 받는다.

```rust
fn main() {
    // ...snip...

    println!("Searching for {}", config.query);
    println!("In file {}", config.filename);

    run(config);
}

fn run(config: Config) {
    let mut f = File::open(config.filename).expect("file not found");

    let mut contents = String::new();
    f.read_to_string(&mut contents).expect("something went wrong reading the file");

    println!("With text:\n{}", contents);
}

// ...snip...
```

---

### run 함수에서 에러 반환하기

나머지 프로그램 로직을 `main`이 아닌 `run` 함수로 분리하면, `Config::new` 처럼 에러 처리를 향상시킬 수 있다. `expect`를 호출해서 프로그램을 패닉을 만드는 대신, `Result<T, E>`를 리턴할 것이다. 

여기서 세 가지 큰 변화를 만들었다. 먼저 `run` 함수의 리턴 타입을 `Result<(), Box <dyn Error>>`로 바꾸었다. 

여기서 에러 타입으로, 특성 오브젝트 `Box`를 사용한다. 그리고 `use`문으로 `std::error::Error`를 범위 내로 임포트했다. 지금 당장은 `Box<dyn Error>`는 함수가 `Error` 특성을 구현하는 타입을 반환한다는 것만 알면 된다. 이러한 방식은 다양한 에러 상황에 다른 타입의 오류 값을 반환 할 수 있는 유연성을 확보 할 수 있다. `dyn`은 “dynamic”의 약자다. 

두 번째 변화는 `?`를 사용해 `expect`에 대한 호출을 제거한 것이다. 에러 시에 `panic!`을 호출하는 것보다 현재 함수에서 에러 값을 반환하며 호출자가 처리 할 수 있도록 했다. 

세 번째, 이 함수는 성공시 `Ok`값을 반환한다. 선언부에서 `run` 함수의 성공 타입을 `()`로 선언했다. `Ok(())` 구문은 조금 이상하게 보일 수 있지만, `()`를 사용하는 것과 마찬가지로 이는 사이드이펙트 없이 `run`을 호출하는 관용적인 방법이다. 

---

실행해보면`Result` 값을 무시했다는 것을 알려준다. 에러가 발생했는지 확인하지 않고, 컴파일러는 여기에 에러 처리를 해야한다는 것을 알려준다.

```rust
use std::error::Error;

// ...snip...

fn run(config: Config) -> Result<(), Box<dyn Error>> {
    let mut f = File::open(config.filename)?;

    let mut contents = String::new();
    f.read_to_string(&mut contents)?;

    println!("With text:\n{}", contents);

    Ok(())
}
```

---

```
warning: unused result which must be used, #[warn(unused_must_use)] on by default
  --> src/main.rs:39:5
   |
39 |     run(config);
   |     ^^^^^^^^^^^^
```

---

### main안의 run에서 반환되는 에러 처리하기

`Config::new`를 사용해 오류를 처리하는 방식과 비슷하게 오류를 검사해보자. 그러나 약간의 차이점이 있다.

먼저 `unwrap_or_else`를 호출하기 보다 `if let`을 사용해 `run`이 `Err` 값을 반환하는지 검사하고 만약 그렇다면 `process::exit(1)`으로 종료한다. `run`은 `Config::new`가 `Config` 객체를 반환하는 것처럼 `unwrap`할 필요가 없다. 왜냐면 `run`은 성공하면 `()`을 반환하기 때문에, 에러가 발생한 경우만 신경쓰면 된다. 

```rust
fn main() {
    // ...snip...

    println!("Searching for {}", config.query);
    println!("In file {}", config.filename);

    if let Err(e) = run(config) {
        println!("Application error: {}", e);

        process::exit(1);
    }
}
```

---

### 라이브러리 크레이트로 코드를 나누기

이제 `src/main.rs`를 나눠서 `src/lib.rs`에 몇 개의 코드를 넣어서 테스트 할 수 있다.

`src/main.rs`에 존재하는 다음 파일들을 `src/lib.rs`로 옮기자.

- `run` 함수 정의
- 관련있는 `use` 문들
- `Config` 정의
- `Config::new` 함수와 정의

`Config`의 필드 및 `new` 메소드와 `run` 함수에 대해 `pub`를 사용했다. 이제 테스트할 수 있는 공개 API를 가진 라이브러리 크레이트가 생겼다.

```rust
use std::error::Error;
use std::fs::File;
use std::io::prelude::*;

pub struct Config {
    pub query: String,
    pub filename: String,
}

impl Config {
    pub fn new(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("not enough arguments");
        }

        let query = args[1].clone();
        let filename = args[2].clone();

        Ok(Config { query, filename })
    }
}

pub fn run(config: Config) -> Result<(), Box<dyn Error>>{
    let mut f = File::open(config.filename)?;

    let mut contents = String::new();
    f.read_to_string(&mut contents)?;

    println!("With text:\n{}", contents);

    Ok(())
}
```

---

### 바이너리 크레이트에서 라이브러리 크레이트 호출하기

`src/main.rs`에 있는 바이너리 크레이트의 범위에 `src/lib.rs`로 옮긴 코드를 `extern crate greprs`를 사용해 가져와야 한다. 이후 `use greprs::Config` 행을 추가해 `Config` 타입을 범위로 가져오고 같은 크레이트 이름으로 `run` 함수 앞에 접두사를 붙인다. 

라이브러리 크레이트를 바이너리 크레이트에 가져 오려면 `extern crate greprs`를 사용한다. 그런 다음 `greprs::Config` 줄을 추가해 `Config` 타입을 범위로 가져오고 `run`함수 접두어에 크레이트 이름을 붙인다. 이를 통해 모든 기능이 연결되어 있어야  작동한다. 

```rust
extern crate greprs;

use std::env;
use std::process;

use greprs::Config;

fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::new(&args).unwrap_or_else(|err| {
        println!("Problem parsing arguments: {}", err);
        process::exit(1);
    });

    println!("Searching for {}", config.query);
    println!("In file {}", config.filename);

    if let Err(e) = greprs::run(config) {
        println!("Application error: {}", e);

        process::exit(1);
    }
}
```

---

## 테스트 주도 개발로 라이브러리의 기능 개발하기

`src/lib.rs`로 로직을 추출했으므로 핵심 기능 코드에 대한 테스트를 작성하는 것이 쉬워졌다. 커맨드라인에서 바이너리를 실행할 필요없이 다양한 인수를 사용해 함수를 직접 호출하고 반환 값을 확인할 수 있다. 

이번엔 TDD(Test Driven Development)프로세스에 따라 검색 로직을 추가한다. 

1. 실패할 테스트를 작성하고 의도한 대로 실패하는 지 실행
2. 새 테스트를 통과하기 충분할 정도로 코드를 작성하고 수정
3. 추가하거나 수정하는 정도의 리팩토링을 해보고, 여전히 테스트를 통과하는지 확인
4. 1단계로 반복! 

소프트웨어를 작성하는 여러 가지 방법 중 하나지만 TDD는 코드 설계를 좋은 상태로 유지시켜 준다. 코드를 작성하기 전에 테스트를 작성하고 테스트를 통과시키면 높은 테스트 범위를 유지하는데 도움이 된다. 실제로 파일 내용에서 쿼리 문자열을 검색하고 쿼리와 일치하는 줄의 목록을 생성하는 기능의 구현을 TDD로 개발해보자. 이 기능을 `search`라는 함수에 추가할 것이다.

---

### 실패 테스트 작성하기

이제 프로그램의 동작을 확인하는데 사용했던 `println!` 문을 `src/lib.rs`및 `src/main.rs`에서 제거하자. 그런 다음 `src/lib.rs`에 `test`함수가 있는 `test` 모듈을 추가 하자. `test`함수는 `search`함수에 필요한 동작을 지정한다. 쿼리와 텍스트를 가져와서 쿼리를 검색하고 쿼리를 포함하는 텍스트의 줄만 반환한다. 

---

이 테스트는 `“duct”`라는 문자열을 검색한다. 검색할 대상은 세 줄로, 그 중 한줄은 `“duct”`를 포함한다. 코드에서는 `search`함수에서 `“safe, fast, productive.”`를 반환할 것이라고 단정(assert)했다.

아직 `search` 함수가 존재하지 않기 때문에 테스트를 실행할 수는 없다. 테스트만을 간단하게 실행시키기 위해 항상 빈 벡터를 반환하는 `search` 함수의 정의를 추가해 컴파일만 시켜보자. 빈 벡터가 `"safe, fast, productive.”` 와 동일하지 않기 때문에 테스트는 컴파일 되지만 실패하게 된다. 

```rust
#[cfg(test)]
mod test {
    use super::*;

    #[test]
    fn one_result() {
        let query = "duct";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.";

        assert_eq!(
            vec!["safe, fast, productive."],
            search(query, contents)
        );
    }
}
```

---

자 이제 테스트를 실행해보면, 의도한대로 실패한다.

```
running 1 test
test test::one_result ... FAILED

failures:

---- test::one_result stdout ----
thread 'test::one_result' panicked at 'assertion failed: `(left == right)`
  left: `["safe, fast, productive."]`,
 right: `[]`', src/lib.rs:49:9
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace

...
```

---

## 테스트를 통과하는 코드 작성

현재는 `search`가  늘 빈 벡터를 반환하니까 테스트가 실패하게 된다. 이를 수정하고 `search`를 구현하기 위해 프로그램은 다음 단계를 따라야 한다.

- `contents`의 각 줄에 대한 반복작업
- 해당 줄에 쿼리 문자열이 포함되어 있는지 검사
- 그렇다면, 반환할 값 목록에 추가
- 그렇지 않다면, 통과
- 일치하는 결과 목록을 반환

---

1. lines 메소드를 사용해 줄들에 대한 반복 작업

Rust는 문자열의 줄 단위로 반복 작업을 할 수 있는 `lines`라는 메소드가 있다.

`lines` 메소드는 반복자를 리턴한다.  반복자와 함께 `for`반복문을 사용하여 컬렉션의 각 항목에 대해 코드를 수행할 수 있다.

```rust
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    for line in contents.lines() {
        if line.contains(query) {
            // do something with line
        }
    }
}
```

---

1. Query로 각 줄을 검색하기

다음으로 현재 줄에 쿼리 문자열이 포함되어 있는지 확인한다. 문자열에는 `contains`라는 메소드를 사용할 수 있다. 

```rust
for line in contents.lines() {
	if line.contains(query) {
		// do something with line
	}
}
```

---

1. 일치하는 줄 보관하기

쿼리 문자열이 포함된 줄이 저장할 방법이 필요하다. `for` 반복문 전에 가변 벡터를 만들고 `push` 메소드를 호출해 벡터에 `line`을 저장한다. `for` 반복문이 끝난 다음에 벡터를 반환한다.

이제 `search` 함수는 `query`를 포함하는 줄들만 반환하게 되었으니 테스트는 통과되어야 한다. 

```rust
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let mut results = Vec::new();

    for line in contents.lines() {
        if line.contains(query) {
            results.push(line);
        }
    }

    results
}
```

---

### run 함수에서 search함수를 사용하기

이제 `search` 함수는 실행되고 테스트 되지만, `run`함수에서 `search`를 호출하게 해야 한다. `config.query` 값과 `run`으로 파일에서 읽어온 `contents`를 `search`함수에 전달해야 한다. 그 이후 `run`은 `search`로부터 반환된 각 줄을 출력한다. 

```rust
pub fn run(config: Config) -> Result<(), Box<Error>> {
    let mut f = File::open(config.filename)?;

    let mut contents = String::new();
    f.read_to_string(&mut contents)?;

    for line in search(&config.query, &contents) {
        println!("{}", line);
    }

    Ok(())
}
```

---

## 환경 변수들을 활용하기

### 대소문자를 구분하는 search 함수의 실패 케이스 작성하기

새로운 `search_case_insensitive` 함수를 추가하고 환경 변수가 적용되어 있으면 호출해보자. 

---

```rust
#[cfg(test)]
mod test {
    use super::*;

    #[test]
    fn case_sensitive() {
        let query = "duct";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.
Duct tape.";

        assert_eq!(
            vec!["safe, fast, productive."],
            search(query, contents)
        );
    }

    #[test]
    fn case_insensitive() {
        let query = "rUsT";
        let contents = "\
Rust:
safe, fast, productive.
Pick three.
Trust me.";

        assert_eq!(
            vec!["Rust:", "Trust me."],
            search_case_insensitive(query, contents)
        );
    }
}
```

---

### search_case_insensitive 함수 구현

`search_case_insensitive`는 `search` 함수와 거의 동일하다. 유일하게 다른 점은 `query`와 각 `line`을 소문자로 만들어 인자의 대소문자 여부와 무관하게 검사할 수 있다.

---

`to_lowercase` 호출은 기존 데이터를 참조하는 것이 아니라 새로운 데이터를 생성하기 때문에 `query`는 문자열 슬라이스가 아니라 `String`이다. 따라서 `contains` 메소드에 인자로 `query`를 전달할 때 앰퍼샌드(`&`)를 추가해야 한다.

```rust
fn search_case_insensitive<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let query = query.to_lowercase();
    let mut results = Vec::new();

    for line in contents.lines() {
        if line.to_lowercase().contains(&query) {
            results.push(line);
        }
    }

    results
}
```

---

이제 `run` 함수에서 `search_case_insensitive`를 호출해보자. 먼저 `Config` 구조체에 대소문자를 구분할지 설정 옵션을 추가한다. 이 필드를 추가하면 컴파일러가 필드 값을 초기화 하지 않았다고 에러를 낸다. 

`bool` 값 `case_sensitive`를 추가했고 `run`함수를 실행해서 `case_sensitive` 필드의 값을 확인해서 `search` 함수와 `search_case_insensitive`함수 중 어느 쪽을 호출할 것인지 결정하면 된다.

```rust
pub struct Config {
    pub query: String,
    pub filename: String,
    pub case_sensitive: bool,
}
```

---

그렇다면 환경변수를 검사해야한다. 환경 변수를 다루기 위한 함수들은 `env` 모듈이 있는 표준 라이브러리에 있다. 그래서 `use std::env;` 를 `src/lib.rs`의 최상단에 추가하고 `env`에 있는 `var` 메소드를 사용해 `CASE_INSENSITIVE`란 이름의 환경변수를 검사할 수 있다. 

---

```rust
use std::env;

// --snip--

impl Config {
    pub fn new(args: &[String]) -> Result<Config, &'static str> {
        if args.len() < 3 {
            return Err("not enough arguments");
        }

        let query = args[1].clone();
        let filename = args[2].clone();

        let case_sensitive = env::var("CASE_INSENSITIVE").is_err();

        Ok(Config { query, filename, case_sensitive })
    }
}
```

```rust
pub fn run(config: Config) -> Result<(), Box<Error>> {
    let mut f = File::open(config.filename)?;

    let mut contents = String::new();
    f.read_to_string(&mut contents)?;

    let results = if config.case_sensitive {
        search(&config.query, &contents)
    } else {
        search_case_insensitive(&config.query, &contents)
    };

    for line in results {
        println!("{}", line);
    }

    Ok(())
}
```

---

## 표준 출력 대신 표준에러로 에러메시지 출력하기

지금까지 모든 출력을 println!을 사용해 터미널에 출력했다. 대부분의 터미널은 두 가지 방식의 출력을 지원한다. *표준 출력*(stdout)은 일반적인 정보전달용이고 *표준 에러*(stderr)는 에러 메시지용이다. 이렇게 구분지음으로 인해 사용자는 프로그램의 출력을 직접 파일에 작성하면서도 여전히 에러 메시지를 화면에 출력할 수 있다. 

`println!` 함수는 오직 표준 출력만을 사용하므로 표준에러에 출력을 위한 다른 것을 알아보자. 

---

### 에러를 표준에러로 출력하기

이전 장에서 리팩토링한 결과 모든 에러는 하나의 함수 `main`에서 출력되고 있다. 표준라이브러리에 존재하는 표준에러에 출력해주는 매크로 `eprintln!`를 사용하는 것으로 변경하자. 

```rust
fn main() {
    let args: Vec<String> = env::args().collect();

    let config = Config::new(&args).unwrap_or_else(|err| {
        eprintln!("Problem parsing arguments: {}", err);
        process::exit(1);
    });

    if let Err(e) = minigrep::run(config) {
        eprintln!("Application error: {}", e);

        process::exit(1);
    }
}
```

---

# 함수형 언어의 특성들: 반복자들과 클로저들

Rust의 디자인은 많은 기존 언어들과 기술들에서 영감을 얻었으며, 중요한 영향 중에 하나는 함수형 프로그래밍이다. 함수형 프로그래밍은 함수를 값처럼 인자로 넘기는 것, 다른 함수들에서 결괏값으로 함수들을 돌려주는 것, 나중에 실행하기 위해 함수를 변수에 할당하는 것 등을 포함한다. 이번에는 다른 언어에서 함수형으로 언급되는 특성들과 유사한 Rust의 특성들에 대해 정리한다. 

- 클로저, 변수에 저장할 수 있는 함수와 유사한 구조.
- 반복자, 일련의 요소들을 처리할 수 있는 방법.
- 이 두가지 특성을 이용해 이전장의 `minigrep` 을 향상시킬 수 있는 방법
- 이 두 특성들의 성능

---

## 클로저: 환경을 캡처할 수 있는 익명 함수

Rust의 클로저는 변수에 저장하거나 다른 함수에 인자로 넘길 수 있는 익명 함수다. 한 곳에서 클로저를 만들고 다른 문맥에서 그것을 호출할 수 있다. 함수와 다르게 클로저는 그들이 호출되는 스코프로부터 변수들을 캡처할 수 있다. 이 클로저 특성이 코드 재사용과 동작 사용자 정의를 어떤 식으로 허용하는지 예를 들어 보여줄 것이다.

### 클로저로 행위를 추상화 하기

클로저를 나중에 실행하기 위해 저장하는 것이 유용할 때를 예제로 보자. 

다음 상황을 가정해보자. 맞춤 운동 계획을 생성하는 앱을 만드는 스타트업에서 일한다. 백엔드는 러스트로 작성되어 있고, 운동 계획을 생성하는 알고리즘은 앱 사용자의 나이, 체질량 지수, 선호도, 최근 운동들과 그들이 지정한 강도 숫자와 같은 많은 다른 요소들을 고려한다. 이 예제에서 실제 알고리즘은 중요하지 않고, 우리가 주목해야 하는 것은 알고리즘이 몇 초가 걸린다는 점이다. 이 알고리즘을 우리가 필요할 대 한번만 호출하기를 원하고, 그래서 사용자가 필요 이상으로 기다리지 않게 만들고 싶다. 

`simulated_expensice_calculation` 함수를 이용해 이 가상의 알고리즘 호출을 실험해보자. 이 함수는 `calculating slowly…` 를 출력하고, 2초를 기다린 다음, 인자로들어온 어떤 값이든 돌려준다.

```rust
use std::thread;
use std::time::Duration;

fn simulated_expensive_calculation(intensity: u32) -> u32 {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    intensity
}
```

---

다음은 이 예제에서 중요한 운동 앱의 일부를 담고 있는 `main`함수다. 이 함수는 사용자가 운동 계획을 물어볼 때 앱이 호출 할 코드를 나타낸다. 앱의 프론트엔드와의 상호작용은 클로저를 사용하기에 적합하지 않기 때문에, 프로그램에 대한 입력을 나타내는 값을 코드상에 넣어두고 결과를 출력 할 것이다.

필요한 입력들은

- 사용자로부터의 강도 숫자, 이것은 그들이 운동을 요청할 때 지정되며, 낮은 강도 운동을 원하는지 혹은 고강도 운동을 원하는지를 나타낸다.
- 임의의 숫자는 몇 가지 다양한 운동 계획들을 생성할 것이다.

결과는 추천 운동 계획이 될 것이다. 

단순함을 위해 `simulated_user_specified_value` 변수의 값을 `10`으로 하고 `simulated_random_number` 변수의 값도 `7`로 하드코딩했다. 실제 프로그램에서는, 강도 숫자를 앱 프론트엔드에서 얻고, `rand`크레이트를 사용해야한다. 

```rust
fn main() {
    let simulated_user_specified_value = 10;
    let simulated_random_number = 7;

    generate_workout(
        simulated_user_specified_value,
        simulated_random_number
    );
}
```

---

이제 상황이 만들어 졌으니, 알고리즘으로 넘어가자. `generate_workout` 함수는 이 예제에서 가장 신경써야 할 앱의 비지니스 로직을 담고 있다. 

이 코드는 느린 계산 함수에 대해 여러번 호출한다. 첫번째 `if` 블럭은 `simulated_expensive_calculation` 함수를 두번 호출하고, 바깥 `else`의 안쪽에 있는 `if` 문에서는 전혀 호출하지 않으며, 두 번째 `else`문에는 한번 호출한다.

---

데이터 과학팀은 앞으로 알고리즘 호출 방식을 일부 변경해야 한다고 알렸다. 이러한 변경이 발생 했을 때 업데이트를 단순화 하기 위해서, 이 코드를 리팩토링 하여 `simulated_expensive_calculation` 함수를 단지 한 번만 호출 하도록 하려 한다. 또한 현재  프로세스에서 해당 함수에 대한 다른 호출을 추가하지 않고 불필요하게 함수를 두 번 호출하는 위치를 없애고 싶다. 즉 결과가 필요 없다면 함수를 호출하고 싶지 않고, 필요하다면 딱 한 번만 호출하고 싶다. 

```rust
fn generate_workout(intensity: u32, random_number: u32) {
    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            simulated_expensive_calculation(intensity)
        );
        println!(
            "Next, do {} situps!",
            simulated_expensive_calculation(intensity)
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                simulated_expensive_calculation(intensity)
            );
        }
    }
}
```

---

### 함수를 사용해 리팩토링 하기

여러 방향으로 운동 프로그램을 다시 구조화 할 수 있다. 우선 다음처럼 중복된 `expensive_calculation` 함수 호출을 하나의 변수로 추출해보자.

이 변경은 `simulated_expensive_calculation`에 대한 모든 호출을 하나로 합치고 첫번째 `if` 문에서 불필요하게 이 함수를 여러번 호출하던 문제를 해결한다. 하지만 모든 경우에 대해서 이 함수를 호출하고 결과를 기다린다. 심지어 이 결과를 전혀 사용하지 않는 안쪽 `if` 블럭도 해당된다. 

```rust
fn generate_workout(intensity: u32, random_number: u32) {
    let expensive_result =
        simulated_expensive_calculation(intensity);

    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            expensive_result
        );
        println!(
            "Next, do {} situps!",
            expensive_result
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_result
            );
        }
    }
}
```

---

### 코드를 저장하기 위해 클로저를 사용해서 리팩토링 하기

`if` 블럭 전에 항상 `simulated_expensive_calculation` 함수를 호출하는 대신, 그러니까 변수에 함수의 결과값을 저장하기 보단, 다음처럼 *클로저*를 변수에 저장할 수 있다. 여기서 소개하는 것처럼 실제로 클로저 안에 `simulated_expensive_calculation`의 전체 내용을 옮길 수 있다. 

클로저 정의는 변수 `expensive_closure`에 그것을 할당하기 위해 `=` 다음에 온다. 클로저를 정의하기 위해, 수직의 파이프(`|`) 한쌍으로 시작하며, 그 사이에 클로저에 대한 파라미터를 기술한다. 이 클로저는 `num` 이라는 하나의 파라미터를 갖는다. 하나 이상의 파라미터를 갖는다면, `|param1, param2|` 와 같이 콤마로 구분한다.

파라미터들 다음에, 클로저의 바디를 포함하는 중괄호를 넣는다. 클로저 바디가 하나의 표현식이라면 이것은 선택적이다. 중괄호 다음에 클로저의 끝에는 `let` 문을 완성하기 위해 세미콜론이 필요하다. 클로저 바디에서 마지막 줄로부터 반환되는 값은 (`num`)은 그것이 호출되었을 때 클로저로 부터 반환되는 값이 될 것이다. 

`let` 문은 `expensive_closure`가 익명함수의 정의를 포함하지만, 그 익명함수를 호출한 **결과 값은 포함하지 않는다.** 클로저를 사용하는 이유는 호출할 코드를 한 곳에서 정의하고, 그 코드를 저장하며, 이후 다른 곳에서 그것을 호출하길 원하기 때문이다. 

```rust
let expensive_closure = |num| {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    num
};
```

---

클로저를 정의하면서, 저장된 코드를 실행하고 결과값을 얻기 위하여 `if` 블록 안의 코드를 클로저 호출 방식으로 변경할 수 있다. 함수를 호출하는 것처럼 클로저를 호출 한다. 클로저 정의를 갖고 있는 변수명을 쓰고 다음엔 사용할 인자값을 포함하는 괄호가 따라온다. 

이제 비용이 큰 계산은 단 한 곳에서만 호출 되고, 필요한 곳에서만 그 코드를 실행한다. 

하지만 여전히 첫번째 `if` 블럭에서 클로저를 두번 호출하는데, 이는 비용이 큰 코드를 두번 호출하고 사용자가 실행시간 만큼 긴시간을 두번 기다리게 한다. 이는 `if` 블럭 안에 클로저 호출의 결과를 저장하는 로컬 변수를 만들어서 그 문제를 해결할 수 있지만, 클로저는 다른 해결책을 제공한다. 그러나 우선 클로저 정의에 타입 어노테이션이 없는 이유와 클로저와 연관된 트레잇에 대해 짚고 가자.

```rust
fn generate_workout(intensity: u32, random_number: u32) {
    let expensive_closure = |num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    };

    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            expensive_closure(intensity)
        );
        println!(
            "Next, do {} situps!",
            expensive_closure(intensity)
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_closure(intensity)
            );
        }
    }
}
```

---

### 클로저 타입 추론과 어노테이션

클로저는 `fn` 함수처럼 파라미터나 반환값의 타입을 명시할 것을 요구하지 않는다. 타입 어노테이션은 사용자에게 노출 되는 명시적인 인터페이스의 일부이기 때문에 함수에 필요하다. 이 인터페이스를 엄격하게 정의하는 것은 함수가 어떤 타입의 값을 사용하고 반환하는지에 대해 모두가 합의 한다는 것을 보장하는데 중요하다. 하지만 클로저는 이와 같이 노출된 인터페이스에 사용되지 않는다. 변수에 저장되고 이름없이 우리의 라이브러리 사용자들에게 노출되지 않고 사용된다.

추가적으로, 클로저는 보통 짧고 임의의 시나리오 보다 좁은 문맥 안에서만 관련이 있다. 이런 제한된 문맥 안에서만, 컴파일러는 안정적으로 파라미터와 리턴타입을 추론할 수 있으며, 이는 대부분의 변수 타입을 추론 할 수 있는 방법과 비슷하다. 

프로그래머들에게 이런 작고 익명의 함수들에 타입을 달도록 하는 것은 짜증나고 컴파일러가 이미 사용할수 있는 정보와 대게는 중복 된다. 

변수처럼, 엄밀하게 필요한 것 이상으로 자세히 표현하는 비용을 지불하고서라도 명확성과 명료성을 높고 싶다면 타입 어노테이션을 추가할 수 있다. 

---

타입 어노테이션이 있으면 클로저와 함수의 문법은 더 비슷해 보인다. 다음은 파라미터에 1을 더하는 함수 정의와 동일한 행위를 하는 클로저를 수직으로 비교한 것이다. 관련 있는 부분들을 정렬하기 위해 약간의 공백을 추가했다. 이것은 파이프를 사용하는 것과 선택적인 문법의 양을 제외하고 클로저 문법과 함수 문법이 얼마나 비슷한지 보여준다. 

```rust
let expensive_closure = |num: u32| -> u32 {
    println!("calculating slowly...");
    thread::sleep(Duration::from_secs(2));
    num
};
```

---

```rust
fn  add_one_v1   (x: u32) -> u32 { x + 1 }
let add_one_v2 = |x: u32| -> u32 { x + 1 };
let add_one_v3 = |x|             { x + 1 };
let add_one_v4 = |x|               x + 1  ;
```

---

클로저 정의는 각 파라미터들과 그들의 반환값에 대해 단 하나의 추론된 구체적인 타입을 갖는다. 예를 들면 다음 예제는, 파라미터로 받은 값을 그대로 반환하는 짧은 클로저의 정의를 보여준다. 이 클로저는 정의에 타입 어노테이션을 추가하지 않았다. 클로저를 두번 호출하는데, 첫번째는 `String`을 인자로 사용하고 두번째는 `u32`를 사용한다면 에러가 발생한다. 

처음 `String` 값으로 `example_closure`를 호출하면, 컴파일러는 `x`의 타입과 클로저의 반환 타입을 `String`으로 추론한다. 이 타입들은 그 다음에는 `example_closure`에 있는 클로저에 고정되고, 같은 클로저를 다른 타입으로 사용하려고 할때 타입 에러를 얻게 된다. 

```rust
let example_closure = |x| x;

let s = example_closure(String::from("hello"));
let n = example_closure(5);
```

---

### 제네릭 파라미터와 Fn 트레잇을 사용해 클로저 저장하기

운동 생성 앱으로 돌아가자. 코드는 아직도 비용이 큰 계산을 하는 클로저를 필요한 것보다 더 많이 호출한다. 이 문제를 풀기위한 한가지 옵션은 비싼 비용의 클로저 결과를 재활용을 위해 변수에 저장하고 결과가 필요한 부분에서 클로저를 다시 호출하는 대신 그 변수를 사용하는 것이다. 그러나 이 방법은 많은 반복된 코드를 만들 수 있다. 

다른 해결책으로는 클로저와 클로저를 호출한 결과값을 갖고 있는 구조체를 만들 수 있다. 그 구조체는 결과값을 필요로 할 때만 클로저를 호출할 것이며, 결과값을 캐시에 저장해 두어 나머지 코드에서 결과를 저장하고 재사용 하지 않아도 되도록 할 것이다. 이 패턴을 *메모이제이션(memoization)* 혹은 *지연평가(lazy evalutaion)*로 알고 있을 것이다.

구조체에서 클로저를 갖고 있도록 하기 위해, 클로저 타입을 기술 할 필요가 있는데, 구조체 정의는 각 필드의 타입을 알 필요가 있기 때문이다. 각 클로저 인스턴스는 자신의 유일한 익명 타입을 갖는다. 즉 두 클로저가 동일한 타입 서명을 갖더라도 그들의 타입은 여전히 다른 것으로 간주 된다. 클로저를 사용하는 구조체, 열거형, 함수 파라미터를 정의하기 위해, 제네릭과 트레잇 바운드를 사용한다.

`Fn` 트레잇은 표준 라이브러리에서 제공한다. 모든 클로저들은 `Fn`, `FnMut`, `FnOnce` 트레잇 중 하나를 구현한다. 환경을 갭처하는 것에 대한 다음 절에서 이 트레잇들의 차이점에 대해 설명한다. 이 예제에서는 `Fn` 트레잇을 사용할 수 있다.

클로저가 이 트레잇 바운드에 맞춰야 하는 파라미터와 반환값의 타입을 표현하기 위해 `Fn` 트레잇 바운드에 타입을 추가한다. 이 경우, 클로저는 파라미터 타입이 `u32`이고 `u32`타입을 반환하므로, 명시하는 트레잇 바운드는 `Fn(u32) → u32` 이다. 

`Cacher` 구조체는 제네릭 타입 `T`의 `calculation` 필드를 갖는다. `T`에 대한 트레잇 바운드는 `Fn` 트레잇을 사용하여 그것이 클로저라는 것을 기술한다. `calculation`필드에 저장하고자 하는 클로저는 하나의 `u32` 타입 파라미터를 갖고 `u32` 타입의 값을 반환해야 한다.

```rust
struct Cacher<T>
    where T: Fn(u32) -> u32
{
    calculation: T,
    value: Option<u32>,
}
```

---

`value` 필드는 `Option<u32>`타입이다.  클로저를 실행하기 전에는 `value`는 `None`일 것이다. `Cacher`를 사용하는 코드에서 클로저의 결과를 요청할 경우, `Cacher`는 그 때 클로저를 실행하고 결과를 `Some` variant에 넣어서 `value` 필드에 저장할 것이다. 그 다음에는 코드에서 클로저의 결과를 다시 요청하면 클로저를 다시 실행하는 대신, `Cacher`는 `Some` variant안에 있는 결과를 돌려줄 것이다.

이 필드에 있는 값을 호출하는 코드에서 잠재적으로 변경하도록 두기 보다 `Cacher`가 구조체 필드의 값을 관리하도록 하고 싶기 때문에, 이 필드는 비공개(*private*)다.

`Cacher::new` 함수는 제네릭 제네릭 파라미터 `T`를 받는데, `Cacher` 구조체와 동일한 트레잇 바운드를 갖도록 정의 되었다. 그 다음 `Cacher::new는 calculation` 필드에 명시된 클로저를 포함하고 클로저를 아직 실행한 적이 없기 때문에 `value` 필드가 `None` 값을 갖는 `Cacher` 인스턴스를 반환한다.

호출하는 코드에서 클로저를 평가한 결과값을원할 때, 클로저를 직접 호출하기 보다, `value` 메서드를 호출 할 것이다. 이 메서드는 이미 `self.value`에 결과값을 `Some`으로 갖고 있는지 확인한다. 만약 그렇다면 클로저를 다시 실행하는 대신 `Some` 안에 있는 값을 반환한다. 

만약 `self.value` 가 `None` 이라면, `self.calculation`에 저장된 클로저를 호출하고, 나중에 재사용 하기 위해 결과를 `self.value`에 저장한 다음 그 값을 반환한다.

```rust
impl<T> Cacher<T>
    where T: Fn(u32) -> u32
{
    fn new(calculation: T) -> Cacher<T> {
        Cacher {
            calculation,
            value: None,
        }
    }

    fn value(&mut self, arg: u32) -> u32 {
        match self.value {
            Some(v) => v,
            None => {
                let v = (self.calculation)(arg);
                self.value = Some(v);
                v
            },
        }
    }
}
```

---

클로저를 변수에 직접 저장하는 대신, 클로저가 갖는 `Cacher`의 새 인스턴스를 저장했다. 그러고는 결과가 필요한 각 위치에 `Cacher` 인스턴스의 `Value` 메소드를 호출했다. `value` 메소드를 원하는 만큼 많이 호출할 수 있고, 전혀 호출하지 않을 수도 있으며, 비싼 비용의 계산은 최대 한 번만 수행 될 것이다. 

다양한 `if` 와 `else` 블럭에 있는 모든 케이스들을 검증하기 위해 `simulated_user_specified_value`와 `simulated_random_number` 변수들을 변경해 보면, `calculation slowly..` 메세지는 필요할 때 단지 한 번만 나타난다. `Cacher`는 필요한 것보다 더 많이 비싼 비용의 계산을 호출하지 않도록 보장하는 필요한 로직을 처리해서, `generated_workout`이 비지니스 로직에 집중하도록 해준다.

```rust
fn generate_workout(intensity: u32, random_number: u32) {
    let mut expensive_result = Cacher::new(|num| {
        println!("calculating slowly...");
        thread::sleep(Duration::from_secs(2));
        num
    });

    if intensity < 25 {
        println!(
            "Today, do {} pushups!",
            expensive_result.value(intensity)
        );
        println!(
            "Next, do {} situps!",
            expensive_result.value(intensity)
        );
    } else {
        if random_number == 3 {
            println!("Take a break today! Remember to stay hydrated!");
        } else {
            println!(
                "Today, run for {} minutes!",
                expensive_result.value(intensity)
            );
        }
    }
}
```

---

### Cacher 구현의 제약사항

값을 캐싱하는 것은 일반적으로 유용한 동작이기 때문에 이와는 다른 클로저를 사용 해서 우리 코드의 다른 부분에서 적용하고 싶을 수도 있다. 그러나 현재 `Cacher` 구현은 다른 문맥에서 다르게 재사용 하기에는 두 가지 문제가 있다. 

첫 번째 문제는 `Cacher` 인스턴스가 `value` 메소드의 `arg` 파라미터에 대해 항상 같은 값을 얻는다는 가정을 한다는 것이다. 즉 오른쪽 테스트는 실패할 것이다.

문제는 `c.value` 를 `1`로 호출했을때 이미 `self.value`에 `Some(1)`로 저장한다. 그 후에 `value` 값으로 무엇을 넘기던, 항상 1을 반환 할 것이다. 

`Cacher`가 하나의 값보다 해시맵을 사용하도록 수정해보자. 해시맵은 키는 넘겨받은 `arg` 값이 될 것이고, 해시맵의 값은 그 키로 클로저를 호출한 결과가 될 것이다. `self.value` 가 `Some` 혹은 `None` 값인지 살펴보는 대신 `value`함수는 해시맵의 arg 값을 살펴보고 값이 있으면 반환할 것이다. 값이 없으면, 클로저를 호출해서 해당 `arg` 값과 연관된 해시맵에 결과값을 저장할 것이다. 

 두 번째 문제는 `u32` 타입 파리미터 한 개만 받고 하나의 `u32`을 반환한다는 것이다. 예를 들면, 문자열 슬라이스를 넘겨주고 `usize`값을반환하는 클로저의 결과를 캐시에 저장하고 싶을 수도 있다. 이 이슈를 수정하기 위해, `Cacher` 기능에 유연성을 높여주도록 더 중립적인 파라미터를 사용해 보자. 

```rust
#[test]
fn call_with_different_values() {
    let mut c = Cacher::new(|a| a);

    let v1 = c.value(1);
    let v2 = c.value(2);

    assert_eq!(v2, 2);
}
```

---

### 클로저로 환경 캡처 하기

운동 생성 예제에서, 클로저를 단지인라인 익명 함수로 사용했다. 그러나 클로저는 함수에 없는 추가적인 능력을 갖고 있다. 환경을 캡처해서 클로저가 정의된 스코프의 변수들을 접근할 수 있다.

`equal_to_x` 변수에 저장된 클로저를 둘러싼 환경에 있는 `x` 변수를 사용하는 예제다.

비록 `equal_to_x`의 파라미터 중에 하나가 아니더라도, `equal_to_x`는 `equal_to_x` 가 정의된 동일한 스코프에 정의된 `x` 변수를 사용하는 것이 허용된다.

```rust
fn main() {
    let x = 4;

    let equal_to_x = |z| z == x;

    let y = 4;

    assert!(equal_to_x(y));
}
```

---

함수로는 이처럼 할 수 없다. 오른쪽 코드는 에러가 발생한다.

```
error[E0434]: can't capture dynamic environment in a fn item; use the || { ...
} closure form instead
 --> src/main.rs
  |
4 |     fn equal_to_x(z: i32) -> bool { z == x }
  |                                          ^
```

컴파일러는 이것이 클로저에서만 동작한다고 알려주기 까지 한다. 

```rust
fn main() {
    let x = 4;

    fn equal_to_x(z: i32) -> bool { z == x }

    let y = 4;

    assert!(equal_to_x(y));
}
```

---

클로저가 그것의 환경에서 값을 캡처할 때, 클로저 바디에서 사용하기 위해 그 값을 저장하기 위한 메모리를 사용한다. 이 메모리 사용은 환경을 캡처하지 않는 코드를 실행하길 원하는 상황에서는 지불하고 싶지 않은 오버헤드다. 왜냐하면 함수는 그들의 환경을 캡처할 수 없기 때문에, 함수를 정의하고 사용하는데 결코 이런 오버헤드는 발생하지 않을 것이기 때문이다. 

클로저는 세가지 방식으로 그들의 환경에서 값을 캡처 할 수 있는데, 함수가 파라미터를 받는 세가지 방식과 직접 연결 된다: 소유권 받기, 불변으로 빌려오기, 가변으로 빌려오기. 이것을 다음과 같이 세개의 `Fn`  트레잇으로 표현한다.

- `FnOnce`는 클로저의 환경으로 알고 있는, 그것을 둘러싼 환경에서 캡처한 변수들을 소비한다. 캡처한 변수를 소비하기 위해, 클로저는 이 변수의 소유권을 가져야 하고 그것이 정의될 때 클로 안으로 그것들을 옮겨와야 한다. `Once`는 그 클로저가 동일한 변수들에 대해 한번이상 소유권을 얻을수 없다는 사실을 의미하며, 그래서 한 번만 호출될 수 있다.
- `Fn`은 그 환경으로부터 값들을 불변으로 빌려 온다.
- `FnMut` 값들을 가변으로 빌려오기 때문에 그 환경을 변경할 수 있다.

클로저를 만들 때, Rust는 클로저가 환경에 있는 값을 어떻게 사용하지는에 근거 해서 어떤 트레잇을 사용할지 추론한다. 그래서 위 예제에서 `equal_to_x` 클로저의 바디에서는 `x`에 있는 값을 읽기만 하면 되기 때문에 클로저는 `x`를 불변으로 빌려온다. 그래서 `equal_to_x`는 `Fn` 트레잇이다.

만약 클로저가 환경으로부터 사용하는 값에 대해 소유권을 갖도록 강제하고 싶다면, 파라미터 리스트 앞에 `move` 키워드를 사용할 수 있다. 이 기법은 클로저를 다른 쓰레드로 넘길때 데이터를 이동시켜 새로운 쓰레드가 소유하도록 할때 대부분 유용한다. 

이후에 병렬성에 대해 이야기 하는 부분에서 더 많은 `move`클로저에 대한 예제가 있다. 지금은 클로저에 `move` 키워드를 추가하고 정수 대신 벡터를 사용하도록 했는데, 정수는 이동되지 않고 복사되기 때문이다. 따라서 다음 코드는 컴파일 되지 않는다.

```rust
fn main() {
    let x = vec![1, 2, 3];

    let equal_to_x = move |z| z == x;

    println!("can't use x here: {:?}", x);

    let y = vec![1, 2, 3];

    assert!(equal_to_x(y));
}
```

```
error[E0382]: use of moved value: `x`
 --> src/main.rs:6:40
  |
4 |     let equal_to_x = move |z| z == x;
  |                      -------- value moved (into closure) here
5 |
6 |     println!("can't use x here: {:?}", x);
  |                                        ^ value used here after move
  |
  = note: move occurs because `x` has type `std::vec::Vec<i32>`, which does not
  implement the `Copy` trait
```

---

`move` 키워드를 추가했기 때문에 클로저가 정의될 때 `x`값은 클로저 안으로 이동된다. `x`의 소유권은 클로저가 갖게 되었고, `main`은 더이상 `println!`문에서 `x` 를 사용하도록 허용하지 않는다. 

`Fn`트레잇 바운드 중 하나를 기술할 때 대부분의 경우, `Fn`으로 시작해보면 컴파일러는 클로저 바디에서 무슨일을 하는지에 근거해서 `FnMut` 혹은 `FnOnce`이 필요한지 말해준다. 

---

## 반복자로 일련의 항목들 처리하기

Rust에서 반복자는 항목들을 사용하기 위해 반복자를 소비하는 메서드를 호출하기 전까지 반복자는 아무런 동작을 하지 않는다. 예를 들면 다음 예제는  `Vec`에 정의된 `iter` 메서드를 호출함으로써, 벡터 `v1`에 있는 항목들에 대한 반복자를 생성한다. 이 코드 자체로는 어떤 유용한 동작을 하진 않는다. 

```rust
let v1 = vec![1, 2, 3];

let v1_iter = v1.iter();
```

일단 반복자를 만들면, 다양한 방법으로 사용할 수 있다. 다음 예제는 `for` 루프에서 반복자 생성을 분리 했다. 반복자는 `v1_iter` 변수에 저장되고, 그 시점에 순회는 발생하지 않는다. `v1_iter`에 있는 반복자를 사용하는 `for` 루프가 호출되면, 루프 순회 마다 반복자의 각 요소가 사용되는데, 각각의 값을 출력한다. 

표준 라이브러리에서 반복자를 제공하는 않는 언어에서는, 변수 인덱스 `0`으로 시작해서, 그 변수로 벡터를 인덱싱해서 값을 가져오는데 사용하며, 루프안에서 벡터에 있는 아이템의 총 갯수까지 그 변수를 증가시키는 방식으로 동일한 기능을 작성할 수 있다. 반복자는 그러한 모든 로직을 대신 처리하며 잠재적으로 엉망이 될 수 있는 반복적인 코드를 줄여 준다. 반복자는 벡터처럼 색인할 수 있는 자료구조 뿐만 아니라, 많은 다른 종류의 시퀀스에 대해 동일한 로직을 사용할 수 있도록 더 많은 유연성을 제공한다. 

```rust
let v1 = vec![1, 2, 3];

let v1_iter = v1.iter();

for val in v1_iter {
    println!("Got: {}", val);
}
```

---

### Iterator 트레잇과 next 메서드

모든 반복자는 표준 라이브러리에 정의된 `Iterator`라는 이름의 트레잇을 구현한다. 트레잇의 정의는 다음과 같다. 

이 정의에서는 몇 개의 새로운 문법을 사용한다. `type Item`과 `Self::Item`은 이 트레잇과 연관 타입을 정의한다. 이후에 연관 타입에 대해서 자세히 설명 하겠지만, 지금 당장 알아야 할 것은 이 코드가 `Iterator`트레잇을 구현하는 것은 `Item`타입을 정의하는 것 또한 요구하며, 이 `Item` 타입이 `next` 메서드의 리턴 타입으로 사용된다는 것이다. 다시말하면, `Item` 타입은 반복자로부터 반환되는 타입이다.

`Iterator` 트레잇은 단지 구현자가 하나의 메서드를 정의하도록 요구한다. 그것은 `next` 메서드인데, 이 메서드는 반복자의 하나의 항목을 `Some`에 넣어서 반환하고, 반복자가 종료되면 `None`을 반환한다.

```rust
trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // methods with default implementations elided
}
```

---

다음 예제는 벡터로부터 생성된 반복자에 대해 반복된 `next`호출이 어떤 값들을 반환하는지 보여준다. 

여기서 `v1_iter`가 변경 가능하도록 만들필요가 있다는 것에 유의하자. 반복자에 대해 `next` 메서드를 호출하면 시퀀스의 어디에 있는지 추적하기 위해 반복자가 사용하는 내부 상태를 변경한다. 다른 말로, 이 코드는 반복자를 소비 한다라고 표현한다. `next`에 대한 각 호출은 반복자로부터 하나의 항목을 소비한다. `for`루프를 사용할 때는 `v1_iter`를 변경할 수 있도록 만들 필요가 없는데, 루프가 `v1_iter`의 소유권을 갖고 내부적으로 변경가능하도록 만들기 때문이다.

`next`호출로 얻어온 값들은 벡터 안에 있는 값들에 대한 불변 참조라는 점 역시 주의해야한다. `iter` 메서드는 불변 참조에 대한 반복자를 만든다. 만약 `v1`의 소유권을 갖고 소유된 값들을 반환하도록 하고 싶다면, `iter`대신 `into_iter`를 호출해야 한다. 비슷하게, 가변 참조에 대한 반복자를 원한다면, `iter` 대신 `iter_mnut`를 호출할 수 있다.

```rust
#[test]
fn iterator_demonstration() {
    let v1 = vec![1, 2, 3];

    let mut v1_iter = v1.iter();

    assert_eq!(v1_iter.next(), Some(&1));
    assert_eq!(v1_iter.next(), Some(&2));
    assert_eq!(v1_iter.next(), Some(&3));
    assert_eq!(v1_iter.next(), None);
}
```

---

### 반복자를 소비하는 메서드들

`Iterator` 트레잇에는 표준 라이브러리에서 기본 구현을 제공하는 다수의 다른 메서드들이 있다. `Iterator`트레잇에 대한 표준 라이브러리 API문서를 살펴 보면, 이 메서드들을 찾을 수 있다. 이 메서드들 중 일부는 그들의 구현에서 `next` 메서드를 호출하는데, 이것이 `Iterator` 트레잇을 구현할 때 `next` 메서드를 구현해야만 하는 이유다. 

`next`를 호출하는 메서드들을 소비하는 *어댑터*들이라고 하는데, 그들을 호출하면 반복자를 써버리기 때문이다. `sum` 메서드가 하나의 예인데, 반복자의 소유권을 가져오고 반복적으로 `next`를 호출해서 순회함으로써 반복자들를 소비한다. 

`sum`은 호출한 반복자의 소유권을 갖기 때문에, `sum`을 호출한 후 `v1_iter`은 사용할 수 없다. 

```rust
#[test]
fn iterator_sum() {
    let v1 = vec![1, 2, 3];

    let v1_iter = v1.iter();

    let total: i32 = v1_iter.sum();

    assert_eq!(total, 6);
}
```

---

### 다른 반복자를 생성하는 메서드들

`Iterator` 트레잇에 정의된 다른 메서드들 중에 반복자 어댑터들로 알려진 메서드들은 반복자를 다른 종류의 반복자로 변경한다. 복잡한 행위를 수행하기 위해 읽기 쉬운 방법으로 반복자 어댑터에 대한 여러개의 호출을 연결할 수 있다.

다음은 반복자 어댑터 메서드인 `map` 을 호출하는 예를 보여주는데, 새로운 반복자를 생성하기 위해 각 항목에 대해 호출할 클로저를 인자로 받는다. 여기서 클로저는 벡터의 각 항목에서 `1`이 증가된 새로운 반복자를 만든다. 그러나, 이 코드는 경고가 발생한다. 

```rust
let v1: Vec<i32> = vec![1, 2, 3];

v1.iter().map(|x| x + 1);
```

---

경고가 발생하는 이유는 위 코드가 아무것도 하지 않기 때문이다. 인자로 넘긴 클로저는 결코 호출되지 않는다. 반복자는 그 존재만으로는 아무것도 하지 않고 그것을 소비하는 메서드를 사용해야 한다. 

이 반복자를 소비하기 위해, `collect` 메서드를 사용할 것이다. 이 메서드는 반복자를 소비하고 결과값을 수집 데이터 타입으로 모은다. 

```
warning: unused `std::iter::Map` which must be used: iterator adaptors are lazy
and do nothing unless consumed
 --> src/main.rs:4:5
  |
4 |     v1.iter().map(|x| x + 1);
  |     ^^^^^^^^^^^^^^^^^^^^^^^^^
  |
  = note: #[warn(unused_must_use)] on by default
```

---

벡터에 대한 `map` 호출로부터 반환된 반복자를 순회하면서 결과를 모은다. 이 벡터는 각 항목이 원본 벡터로부터 1씩 증가된 상태로 될 것이다. 

`map`은 클로저를 인자로 받기 때문에, 각 항목에 대해 수행하기를 원하는 어떤 연산도 기술할 수 있다.

```rust
let v1: Vec<i32> = vec![1, 2, 3];

let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();

assert_eq!(v2, vec![2, 3, 4]);
```

---

### 환경을 캡쳐하는 클로저 사용하기

반복자의 `filter` 메서드는 반복자로부터 각 항목을 받아 `Boolean` 을 반환하는 클로저를 인자로 받는다. 만약 클로저가 `true`를 반환하면, 그 값은 `filter`에 의해 생성되는 반복자에 포함될 것이다. 클로저가 `false`를 반환하면, 결과로 나오는 반복자에 포함되지 않을 것이다.

`shoes_in_my_size` 함수는 파라미터로 신발들의 벡터에 대한 소유권과 신발 크기를 받는다. 구현부에서 벡터의 소유권을 갖는 반복자를 생성하기 위해 `into_iter`를 호출한다. 그 다음 그 반복자를 클로저가 `true`를 반환한 요소들만 포함하는 새로운 반복자로 바꾸기 위해 `filter`를 호출한다. 클로저는 환경에서 `shoe_size` 매개 변수를 캡처하고, 지정된 크기의 신발만 유지하면서 각 신발의 크기와 값을 비교한다. 마지막으로, `collect`를 호출하면 적용된 반복자에 의해 리턴된 값을 함수가 리턴한 벡터로 모으게 된다. 

테스트는 `shoes_in_my_size`를 호출 했을 때, 지정된 값과 동일한 사이즈를 갖는 신발들만 돌려받는 다는것을 보여준다. 

```rust
#[derive(PartialEq, Debug)]
struct Shoe {
    size: u32,
    style: String,
}

fn shoes_in_my_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
    shoes.into_iter()
        .filter(|s| s.size == shoe_size)
        .collect()
}

#[test]
fn filters_by_size() {
    let shoes = vec![
        Shoe { size: 10, style: String::from("sneaker") },
        Shoe { size: 13, style: String::from("sandal") },
        Shoe { size: 10, style: String::from("boot") },
    ];

    let in_my_size = shoes_in_my_size(shoes, 10);

    assert_eq!(
        in_my_size,
        vec![
            Shoe { size: 10, style: String::from("sneaker") },
            Shoe { size: 10, style: String::from("boot") },
        ]
    );
}
```

---

### Iterator 트레잇으로 자신만의 반복자 만들기

벡터에 대해 `iter`, `into_iter` 혹은 `iter_mut`를 호출해서 반복자를 생성할 수 있다는 것을 보았다. 해시맵과 같은 표준 라이브러리에 있는 다른 컬렉션 타입으로부터 반복자를 생성할 수 있다. 자신만의 타입에 대해 `Iterator` 트레잇을 구현하는해서 원하는 동작을 하는 반복자를 생성하는 것 또한 가능하다. 그러기 위해서 정의 해야하는 유일한 메서드는 `next` 메서드다. 그러고 나면, `Iterator` 트레잇에서 제공하는 기본 구현을 갖는 다른 모든 메서드를 사용할 수 있다. 

---

`1`부터 `5`까지 셀 수 있는 반복자를 만들어 보자. 우선 어떤 값을 저장하는 구조체를 만들자. 그 다음 `Iterator`트레잇을 구현하고  그 구현에서 값들을 사용함으로써 이 구조체를 반복자로 만들 것이다. 

`Counter` 구조체는 `count`라는 하나의 필드를 갖는다. 이 필드는 `u32` 값을 갖는데 1부터 5까지 순회하는데 어디까지 진행했는지 저장할 것이다. `count` 필드는 `Counter` 구현이 그 값을 관리하길 원하기 때문에 외부로 노출되지 않는다. `new` 함수는 항상 새로운 인스턴스가 `count` 필드에 `0`을 담은 채로 시작하도록 한다. 

다음으로, 반복자가 사용될 때 원하는 것을 지정하기 위해 `next` 메소드의 본문을 정의함으로써 `Counter` 타입에 대한 `Iterator` 특성을 구현한다. 

반복자를 위해 연관된 `Item` 타입을 `u32`로 지정했는데, 반복자가 `u32`값을 반환한다는 것을 의미한다. 

우리는 반복자가 현재 상태에 `1`을 더하길 원하기 때문에 처음에 `count`를 `0`으로 초기화 했고 처음엔 `1`을 반환할 것이다. `count`의 값이 `6`보다 작다면, `next`는 `Some`으로 포장된 현재 값을 리턴할 것이며, `count`가 `6`이거나 더  크다면, 반복자는 `None`을 반환할 것이다.

```rust
struct Counter {
    count: u32,
}

impl Counter {
    fn new() -> Counter {
        Counter { count: 0 }
    }
}

impl Iterator for Counter {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        self.count += 1;

        if self.count < 6 {
            Some(self.count)
        } else {
            None
        }
    }
}
```

---

`Iterator` 트레잇을 구현 했다면, 반복자를 사용할  수 있다. 다음은 벡터로부터 생성된 반복자에게 했던 것처럼, `Counter` 구조체에 직접 `next` 메서드를 호출함으로써 반복자 기능을 사용할 수 있다.

```rust
#[test]
fn calling_next_directly() {
    let mut counter = Counter::new();

    assert_eq!(counter.next(), Some(1));
    assert_eq!(counter.next(), Some(2));
    assert_eq!(counter.next(), Some(3));
    assert_eq!(counter.next(), Some(4));
    assert_eq!(counter.next(), Some(5));
    assert_eq!(counter.next(), None);
}
```

---

이제 `Iterator` 트레잇 메서드들의 기본 구현을 사용할 수 있다! 

```rust
#[test]
fn using_other_iterator_trait_methods() {
    let sum: u32 = Counter::new().zip(Counter::new().skip(1))
                                 .map(|(a, b)| a * b)
                                 .filter(|x| x % 3 == 0)
                                 .sum();
    assert_eq!(18, sum);
}
```

---

## 성능 비교하기: 루프 vs. 반복자

```rust
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    contents
        .lines()
        .filter(|line| line.contains(query))
        .collect()
}
```

```rust
pub fn search<'a>(query: &str, contents: &'a str) -> Vec<&'a str> {
    let mut results = Vec::new();

    for line in contents.lines() {
        if line.contains(query) {
            results.push(line);
        }
    }
    results
}
```

루프와 반복자 중에 어떤것을 사용할지 결정하기 위해 위 `search`함수중 어떤 버전이 더 빠른지 알 필요가 있다. 각가의 버전을 적용한 프로젝트의 벤치마크 결과는 다음과 같다.

```
test bench_search_for  ... bench:  19,620,300 ns/iter (+/- 915,700)
test bench_search_iter ... bench:  19,234,900 ns/iter (+/- 657,200)
```

반복자 버전이 약간 더 빠르다. 반복자는 비록 고수준의 추상이지만, 컴파일되면 대략 직접 작성한 저수준의 코드와 같은 코드 수준으로 내려간다. 반복자는 러스트의 *제로 비용 추상화* 중 하나이며, 이는 추가적인 실행시간 오버헤드가 없다는 것을 의미한다. 최초의 C++ 디자이너이자 구현자인 비야네 스트롭스트룹이 *“Foundations of C++”(2012)* 에서 *제로 오버헤드*를 정의한 것과 유사하다.

> 일반적으로, C++ 구현은 제로-오버헤드 원리를 따른다. 사용하지 않는 것은, 비용을 지불하지 않는다. 그리고 더 나아가 사용하는 것은 더 나은 코드를 제공할 수 없다.
> 

---

다른 예시로, 이 코드는 디코더에서 가져왔다. 디코딩 알고리즘은 이전 샘플의 선형 함수에 기반해서 미래의 값을 추정하기 위해 선형 예측이라는 수학적 연산을 사용한다. 이 코드는 반복자 체인을 사용해서 스코프에 있는 세 개의 변수로 수학 연산을 한다. 데이터의 `buffer` 슬라이스, 12개의 `coefficients` 베열, 그리고 데이터를 쉬프트 하기 위한 `qlp_shift` 값. 이 예제에서 변수를 선언했지만 값을 할당하지는 않았다. 이 코드는 이 문맥밖에서는 크게 의미가 없지만, 러스트가 어떻게 고수준의 개념을 저수준의 코드로 변환하는지에 대한 간결하고 실질적인 예제다.

`prediction`의 값을 계산하기 위해, 이 코드는 `coefficients`에 있는 12개의 값을 순회하면서 각각의 계수와 `buffer`의 이전 12개의 값의 쌍을 만들기 위해 `zip` 메서드를 사용한다. 그런 다음, 각 쌍에 대해 값들을 모두 곱하고 모든 결과를 더한 후 더한 값을 `qlp_shift` 비트만큼 우측으로 쉬프트 한다. 

오디오 디코더와 같은 어플리케이션에서의 계산은 종종 성능에 가장 높은 우선순위를 둔다. 여기서 우리는 두 개의 어댑터를 사용하는 반복자를 생성하고 값을 소비한다. 이 러스트 코드가 컴파일되면 어떤 어셈블리 코드가 될까? 이 글을 쓰는 시점에선 그것은 직접 손으로 작성한 것과 같은 어셈블리 코드로 컴파일된다. 거기엔 `coefficients`의 값들을 순회하기 위한 어떤 루프도 없다. 러스트는 12개의 순회가 있다는 것을 알고 있고, 루프를 “풀어(unrolling)” 놓는다. 언롤링(Unrolling)은 루프 제어 코드의 오버헤드를 제거하고 대신 루프의 각 순회에 해당하는 반복적인 코드를 생성하는 최적화 방법이다.

모든 계수(`coefficients`)값들은 레지스터에 저장되는데 값에 대한 접근이 매우 빠르다는 것을 의미한다. 실행시간에 배열 접근에 대한 경계체크가 없다. 러스트가 적용할 수 있는 이런 모든 최적화들은 결과 코드를 아주 효율적으로 만든다. 이제 이것을 알게 되었으니, 반복자와 클로저를 공포없이 사용할 수 있다! 이것들은 코드를 고수준으로 보이도록 하지만, 그렇게 하기 위해 실행시간 성능 저하를 만들지 않는다.

```rust
let buffer: &mut [i32];
let coefficients: [i64; 12];
let qlp_shift: i16;

for i in 12..buffer.len() {
    let prediction = coefficients.iter()
                                 .zip(&buffer[i - 12..i])
                                 .map(|(&c, &s)| c * s as i64)
                                 .sum::<i64>() >> qlp_shift;
    let delta = buffer[i];
    buffer[i] = prediction as i32 + delta;
}
```

---

클로저와 반복자는 함수형 프로그래밍 아이디어에서 영감을 받은 러스트의 특징들입니다. 이것들은 고수준의 개념을 저수준의 성능으로 명확하게 표현할 수 있는 러스트의 능력에 기여하고 있다. 클로저와 반복자의 구현들은 런타임 성능에 영향을 미치지 않는다. 이것이 제로비용 추상을 제공하기 위해 노력하는 러스트의 목표 중의 일부다.

---

# Cargo와 Crates.io 더 알아보기

지금까지 빌드, 실행, 코드 테스트 등 Cargo의 가장 기본적인 기능만 사용하였지만, Cargo는 훨씬 더 많은 일을 할 수 있다.

- 릴리즈 프로필을 이용해 빌드 커스터마이징하기
- crates.io 에 라이브러리 배포하기
- 대규모 작업을 위한 작업공간 구성하기
- crates.io 에서 바이너리 설치하기
- 커스텀 명령어로 Cargo 확장하기

---

## 릴리즈 프로필을 이용해 빌드 커스터마이징하기

러스트에서 릴리즈 프로필(*release profiles*) 은 프로그래머가 코드 컴파일에 관련된 여러가지 옵션을 제어할 수 있도록 다양한 구성으로 사전 정의되고 커스텀 가능한 프로필이다. 각 프로필은 다른 프로필과 독립적으로 설정된다.

Cargo는 두 메인 프로필을 가진다. 우리가 `cargo build` 를 실행할 때 쓰는 `*dev` 프로필*과 `cargo build --release` 를 실행할때 사용하는 `*release` 프로필*이다. `dev` 프로필은 개발에 적합한 설정을 기본값으로 갖고, `release` 프로필은 릴리즈 빌드용 설정을 기본값으로 가진다. 

빌드 출력에서 이 프로필들의 이름을 몇 번 보았을 수도 있다.

```
$ cargo build
    Finished dev [unoptimized + debuginfo] target(s) in 0.0 secs
$ cargo build --release
    Finished release [optimized] target(s) in 0.0 secs
```

위 출력의 `dev`와 `release` 는 컴파일러가 다른 프로필을 사용한다는 것을 나타낸다.

Cargo는 프로젝트의 *Cargo.toml* 파일에 `[profile.*]` 구획이 따로 없을때 적용되는 각 프로필의 기본 설정을 가지고 있다. 이때 우리가 원하는 프로필에 `[profile.*]` 구획을 추가해 기본 설정을 덮어 씌울 수 있다. 다음 예시는 `dev`와 `release` 프로필 각각의 `opt-level` 기본 설정 값이다.

```
[profile.dev]
opt-level = 0

[profile.release]
opt-level = 3
```

`opt-level` 설정은 러스트가 우리의 코드에 적용할 최적화 수치며, 0 ~ 3 사이의 값을 가진다. 우리가 개발을할 때와 같이 코드를 자주 컴파일 하는 상황에서는 코드의 실행 속도가 조금 느려지는 한이 있더라도 컴파일이 빨리 되길 원한다. 하지만 높은 최적화 수치를 적용할 수록 컴파일에 걸리는 시간은 증가한다. 

따라서 `dev`의 기본 `opt-level` 값은 `0`이다. 만약 우리의 코드를 릴리즈 하려 한다면, 컴파일에 걸리는 시간이 늘어나도 상관이 없을 것이다. 릴리즈 할 경우 컴파일은 한 번이지만, 실행 횟수는 여러번 일 테니까. 따라서 릴리즈 모드에서는 컴파일 시간을 희생하는 대신 빠른 코드 실행 속도를 얻기 위해 `release` 프로필의 기본 `opt-level` 값이 `3`으로 되어 있다. 이 설정을 *Cargo.toml*에 다른 값을 넣어서 덮어 씌울 수 있다. 만약 개발용 프로필에 0이 아닌 1의 최적화 수치를 적용하고 싶다면 *Cargo.toml*에 다음 두 줄을 추가하면 된다.

```
[profile.dev]
opt-level = 1
```

이 코드는 기본 설정인 `0`을 덮어 씌운다. 이후 `cargo build`를 실행하면 Cargo는 커스텀 한 `opt-level`을 사용한다. 이를 `1`로 설정했기 때문에 Cargo 는 릴리즈 빌드 만큼은 아니지만 기본 설정보다 많은 최적화를 진행할 것이다.

---

## Crates.io에 크레이트 배포하기

## Cargo 작업공간

## cargo install을 아용해 Crates.io 에서 바이너리 설치하기

## 커스텀 명령어로 Cargo 확장하기

---

# 스마트 포인터

*포인터(pointer)*는 메모리의 주소 값을 담고 있는 변수에 대한 일반적인 개념이다. 이 주소 값은 어떤 다른 데이터를 참조한다. 혹은 바꿔 말하면, “가리킨다." 러스트에서 가장 흔한 종류의 포인터는 참조자인데, 이는 `&` 심볼에 의해 나타내지고 이들이 가리키고 있는 값을 빌린다. 참조자는 말 그대로 값을 참조하는 것 외에는 다른 어떠한 특별한 능력도 없다. 또한 어떠한 오버헤드도 발생하지 않으며 가장 자주 사용하는 포인터의 한 종류다.

한편, *스마트 포인터(smart pointer)*는 포인터처럼 작동하지만 추가적인 메타데이터와 능력들도 가지고 있는 데이터 구조다. 스마트 포인터의 개념은 러스트에만 존재하는 고유한 내용이 아니다. 스마트 포인터는 C++로부터 유래되었고 다른언어들에도 존재하는 개념이다. 러스트에서 표준 라이브러리에 정의된 다양한 종류의 스마트 포인터들은, 참조자들에 의해 제공되는 것을 넘어서는 추가 기능들을 제공한다. 한 가지 예시로, *참조 카운팅(reference counting)* 스마트 포인터 타입이 있다. 이 포인터는 소유자의 수를 계속 추적하고, 더 이상 소유자가 없으면 데이터를 정리하는 방식으로, 어떤 데이터에 대한 여러 소유자들을 만들 수 있게 해준다.

소유권과 빌림의 개념을 가지고 있는 러스트에서, 참조자와 스마트 포인터간의 추가적인 차이점은 참조자가 데이터를 오직 빌리기만 하는 포인터라는 점이다. 반면 대부분의 경우에서 스마트 포인터는 가리키고 있는 데이터를 *소유*한다.

비록 그때는 스마트 포인터인줄 몰랐지만 이미 앞서 `String`과 `Vec<T>`와 같은 스마트 포인터들을 마주쳤다. 이를 스마트 포인터라고 하는 이유는 이들이 얼마간의 메모리를 소유하고 프로그래머가 이를 다룰 수 있기  때문이다. 또한 (용량 등의) 메타데이터와 (`String`이 언제나 유효한 UTF-8일 것임을 보장하는 것 등의) 추가 능력 혹은 보장을 가지고 있다.

스마트 포인터는 보통 구조체를 이용해 구현되어 있다. 스마트 포인터가 일반적인 구조체와 구분되는 특성은 바로 스마트 포인터가 `Deref`와 `Drop` 트레잇을 구현한다는 것이다. `Deref` 트레잇은 스마트 포인터 구조체의 인스턴스가 참조자처럼 동작하도록 하여 참조자나 스마트 포인터 둘 중 하나와 함께 작동하는 코드를 작성하게 해 준다. `Drop`트레잇은 스마트 포인터의 인스턴스가 스코프 밖으로 벗어났을 때 실행되는 코드를 커스터마이징할 수 있도록 해준다. 

스마트 포인터 패턴이 러스트에서 자주 사용되는 일반적인 디자인 패턴으로 주어지므로, 모든 스마트 포인터를 다루지는 않을 것이다. 많은 라이브러리들이 자신만의 스마트 포인터를 가지고 있다. 이번엔 표준 라이브러리 내의 가장 흔한 스마트 포인터들을 다룰 것이다.

- 값을 힙에 할당하기 위한 `Box<T>`
- 복수개의 소유권을 가능하게 하는 참조 카운팅 타입인 `Rc<T>`
- `RefCell<T>` 를 통해 접근할 수 있는 빌림 규칙을 컴파일 타임 대신 런타임에 강제하는 `Ref<T>`와 `RefMut<T>`

---

## Box<T>는 힙에 있는 데이터를 가리키고 알려진 크기를 갖는다.

가장 직관적인 스마트 포인터는 박스(*box*)이고, 이 타입은 `Box<T>`라고 쓴다. 박스는 데이터를 스택이 아니라 힙에 저장할 수 있도록 해준다. 스택에 남는 것은 힙 데이터를 가리키는 포인터다. 

박스는 스택 대신 힙에 데이터를 저장한다는 점 외에는, 성능적인 오버헤드가 없다. 하지만 추가 기능 또한 가지고 있지 않다.  아마 다음과 같은 상황에서 자주 쓰이게 될 것이다.

- 컴파일 타임에 크기를 알 수 없는 타입을 갖고 있고, 정확한 사이즈를 알 필요가 있는 맥락 안에서 해당 타입의 값을 이용하고 싶을 때
- 커다란 데이터를 가지고 있고 소유권을 옮기고 싶지만 그렇게 했을 때 데이터가 복사되지 않을 것이라고 보장하고 싶을 때
- 어떤 값을 소유하고 이 값의 구체화된 타입을 알고 있기보다는 특정 트레잇을 구현한 타입이라는 점만 신경 쓰고 싶을 때

이번에는 첫 번째 상황을 예시로 들 것이다. 두 번째 상황은 방대한 양의 데이터의 소유권을 옮기는 긴 시간이 소요될 수 있는데 이는 그 데이터가 스택 상에서 복사되기 때문이다. 이러한 상황에서 성능을  향상하기 위해서, 박스 안의 힙에 그 방대한 양의 데이터를 저장할 수 있다. 그러면 작은 양의 포인터 데이터만 스택 상에서 복사되고, 데이터는 힙 상에서 한 곳에 머물게 된다. 세 번째 경우는 *트레잇 객체* 라고 알려진 것이고, 이후에 다룬다.

### Box<T>를 사용하여 힙에 데이터를 저장하기

`Box<T>`에 대한 사용례를 논의하기 전에, 먼저 문법 및 `Box<T>` 내에 저장된 값과 어떻게 상호작용 하는지 다뤄보자.

```rust
fn main() {
	let b = Box::new(5);
	println!("b = {}", b);
}
```

`5` 라는 값을 가리키는 `Box`의 값을 갖는 변수 `b`를 선언했는데, 여기서 `5`는 힙에 할당된다. 이 프로그램은 `b = 5`를 출력한다. 이 경우에는 마치 이 데이터가 스택에 있었던 것과 동일하게 박스 내의 데이터에 접근할 수 있다. 다른 어떤 소유한 값과 마찬가지로, `b`가 `main`의 끝에 도달하는 것처럼 어떤 박스가 스코프를 벗어날 때, 할당은 해제될 것이다. 여기서 할당 해제는 (스택에 저장된) 박스와 이것이 가리키고 있는 (힙에 저장된) 데이터 모두에게 일어난다.

단일 값을 힙에 집어넣는 것은 그다지 유용하지 않으므로, 이러한 방식처럼 박스를 이용하는 것은 자주 사용하지 않을 것이다. 단일한 i32같은 값을 스택에 갖는 것은, 스택이 해당 값이 기본적으로 저장되는 곳이기도 하고, 대부분의 다른 경우가 더 적절한 경우일 것이다. 만약 박스를 쓰지 않는다면 허용되지 않았을 타입을 정의하는 경우를 살펴 보자.

---

### 박스는 재귀적 타입을 가능하게 한다.

러스트는 컴파일 타임에 어떤 타입이 얼마나 많은 공간을 차지하는지 알아야 한다. 컴파일 타임에는 크기를 알 수 없는 한 가지 타입이 바로 재귀적 타입(recursive tyype)인데, 이는 어떤 값이 그 일부로서 동일한 타입의 다른 값을 가질 수 있는 것을 말한다. 이러한 값을 가지는 것은 이론적으로 무한하게 계속될 수 있으니 러스트는 재귀적 타입의 값이 얼마큼의 공간을 필요로 하는지 알 수없다. 하지만, 박스의 크기는 알려진 크기이므로, 재귀적 타입 정의 내에 박스를 넣음으로써 이를 사용할 수 있다. 

재귀적 타입의 예제로서, 함수형 프로그래밍 언어에서 일반적인 데이터 타입인 cons list를 탐구해 보자. 

*cons list*는 Lisp 프로그래밍 언어 및 그의 파생 언어들로부터 유래된 데이터 구조다. Lisp에서, (”생성 함수 (constructf function)”의 줄임말인) `cons` 함수는 두 개의 인자를 받아 새로운 한 쌍을 생성하는데, 이 인자는 보통 단일 값과 또 다른 새로운 쌍이다. 이러한 쌍들을 담고 있는 쌍들이 리스트를 형성한다.

cons 함수 개념은 더 일반적인 함수형 프로그래밍 용어로 연결된다. “to cons x onto y”는 약식으로 요소 x를 새로운 컨테이너에 집어넣고, 그 다음 컨테이너 y를 넣는 식으로 새로운 컨테이너 인스턴스를 생성하는 것을 의미한다.

cons list 내의 각 아이템은 두 가지의 요소(현재 아이템의 값과 다음 아이템)를 담고 있다. 리스트의 마지막 아이템은 다음 아이템 없이 `Nil` 이라 불리는 값을 담고 있다. cons list는 `cons` 함수를 재귀적으로 호출함으로써 만들어진다. 재귀의 기본 케이스를 의미하는 표준 이름이 바로 `Nil`이다. 유효하지 않은 값 혹은 값이 없는 것을 말하는 “null” 혹은 “nil”개념과 동일하지 않다는 점을 주의하자.

비록 함수형 프로그래밍 언어들이 cons list를 자주 사용할지라도, 러스트에서는 흔히 사용되는 데이터 구조가 아니다. 러스트에서 아이템의 리스트를 갖는 대부분의 경우에는, `Vec<T>`가 사용하기에 더 나은 선택이다. 그렇지만 cons list를 통해 박스가 어떻게 재귀적 데이터 타입을 정의하도록 해주는지 볼 수 있다. 

다음은 cons list를 위한 열거형 정의를 담고 있다. List 타입의 크기를 알 수 없기 때문에 이 코드는 아직 컴파일이 되지 않는다.

예제의 목적을 위해 오직 `i32` 값만 담는 cons list를 구현하고 있지만, 임의의 타입 값을 저장할 수 있는 cons list타입을 정의하기 위해 제네릭을 이용해 구현할 수도 있다.

```rust
enum List {
    Cons(i32, List),
    Nil,
}
```

위 `List` 타입을 이용해 리스트 `1, 2, 3`을 저장해보자.

첫 번째 `Cons` 값은 1과 List 값을 갖는다. 이 List 값은 2와 또다른 List 값을 갖는 `Cons` 값이다. 마지막 `Cons` 값은 3과 추가적인 `Cons`인데, 마지막의 `List`는 `Nil`로서, 리스트의 끝을 알리는 비재귀적인 variant다.

이 코드를 컴파일 하고자 하면 에러를 얻는다.

```rust
use List::{Cons, Nil};

fn main() {
    let list = Cons(1, Cons(2, Cons(3, Nil)));
}
```

이 에러는 이 타입이 “무한한 크기를 갖는다"라고 말한다. 그 원인은 재귀적인 variant를 이용해 `List`를 정의했기 때문이다. 즉 또 다른 자신을 직접 값으로 갖기 때문에 결과적으로, 러스트는 `List` 값을 저장하는데 필요한 크기가 얼마나 되는지 알아낼 수 없다. 왜 이런 에러를 얻게 되는지 생각해보자. 먼저, 러스트가 비재귀적인 타입의 값을 저장하는데 필요한 용량이 얼마나 되는지 결정하는 방법을 살펴보자.

```
error[E0072]: recursive type `List` has infinite size
 --> src/main.rs:1:1
  |
1 | enum List {
  | ^^^^^^^^^ recursive type has infinite size
2 |     Cons(i32, List),
  |               ----- recursive without indirection
  |
  = help: insert indirection (e.g., a `Box`, `Rc`, or `&`) at some point to
  make `List` representable
```

---

이전에 사용했던 `Message` 열거형을 기억해보자. 

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

`Message` 값을 할당하기 위해 얼마나 많은 공간이 필요한지를 경정하기 위해서, 러스트는 어떤 variant가 가장 많은 공간을 사용하는지 알기 위해 각각의 variant들 내부를 본다. 여기서 `Mesaage::Quit`가 어떤 공간도 필요 없음을 알게 되고, `Message::Move`는 두 개의 `i32`값을 저장하기에 충분한 공간이 필요함을 알게 되고, 그렇게 모든 variant를 살펴본다. 단 하나의 variant만을 사용할 것이기 때문에,  값이 필요로 하는 가장 큰 공간은 그것의 varient 중 가장 큰 것을 저장하는데 필요한 공간이다. 

그렇다면 동일하게 위에서 정의한 `List` 열거형과 같은 재귀적 타입이 필요로 하는 공간을 결정하고자 시도할 때 어떤 일이 일어나는지를 생각해보자. 컴파일러는 `Cons` variant를 살펴보는 것을 시작하는데, 이는 `i32`타입의 값과 `List` 타입의 값을 갖는다. 그러므로, `Cons`는 `i32`의 크기에 `List`크기를 더한 만큼의 공간을 필요로 한다. `List` 타입이 얼마나 많은 메모리를 차지하는지 알아내기 위해서, 컴파일러는 그것의 variants를 또 살펴보지만, 이는 재귀적으로 무한히 계속된다.

![Untitled](Rust%200d7ead296adb4593aa99b2b8cb2e5307/Untitled.png)

---

러스트는 재귀적으로 정의된 타입을 위해서 얼만큼의 공간을 할당하는지 알아낼 수 없으므로, 컴파일러는 에러를 발생시키는 것이다. 하지만 이 에러는 유용한 제안을 해준다.

```
= help: insert indirection (e.g., a `Box`, `Rc`, or `&`) at some point to
  make `List` representable
```

여기서 “간접 (indirection)”은 값을 직접 저장하는 대신, 간접적으로 값의 포인터를 저장하기 위해 데이터 구조를 바꿀 수 있음을 의미한다.

`Box<T>`는 포인터이기 때문에, 러스트가 언제나 `Box<T>`가 필요로 하는 공간이 얼마인지  알고 있다. 포인터의 크기는 그것이 가리키고 있는 데이터의 양에 기반해 변경되지 않는다. 이는 `Cons` variant내에 또 다른 `List` 값을 직접 넣는 대신 `Box<T>`를 넣을 수 있다는 것이다. `Box<T>`는 `Cons` variant 안에 있기 보다는 힙에 있을 다음 `List`를 가리킬 것이다.

이제 `List` 열거형의 정의와 사용을 다음과 같이 할 수 있다.

`Cons` variant는 i32와 박스의 포인터 데이터를 저장할 공간을 더한 크기를 요구한다. `Nil` variant는 아무런 값도 저장하지 않으므로, List 값은 i32의 크기 + 박스의 포인터 데이터의 크기 만큼을 차지할 것임을 알 수 있다. 박스를 사용해 무한하고 재귀적인 연결을 없애고, 이제 컴파일러는 List 값을 저장하는데 필요한 크기를 알아낼 수 있다.

```rust
enum List {
    Cons(i32, Box<List>),
    Nil,
}

use List::{Cons, Nil};

fn main() {
    let list = Cons(1,
        Box::new(Cons(2,
            Box::new(Cons(3,
                Box::new(Nil))))));
}
```

박스는 단지 간접 및 힙 할당만을 제공할 뿐이다. 다른 어떤 특별한 능력들, 다른 스마트 포인터 타입들에서 보게 될 것 같은 능력들이 없다. 또한 이들은 이러한 특별한 능력들이 초래하는 성능적인 오버헤드도 가지고 있지 않으므로, 필요로 하는 기능이 딱 간접 하나인 cons list와 같은 경우에 유용할 수 있다.

`Box<T>` 타입은 스마트 포인터인데, 그 이유는 이것이 `Deref` 트레잇을 구현하고 있기 때문이며, 이는 `Box<T>` 값이 참조자와 같이 취급되도록 해준다. `Box<T>` 값이 스코프 밖으로 벗어날 때, 박스가 가리키고 있는 힙 데이터도 마찬가지로 정리되는데 이는 `Drop` 트레잇의 구현 때문에 그렇다. 이 두가지 트레잇에 대해서 더 알아보자. 이 두 가지 트레잇이 뒤에 다룰 스마트 포인터 타입에 의해 제공되는 기능들 보다 더 중요할 것이다.

---

## Deref 트레잇을 가지고 스마트 포인터를 평범한 참조자와 같이 취급하기

`Deref` 트레잇을 구현하는 것은 *역참조 연산자 (dereference operator)* `*`의 동작을 커스터마이징 하는 것이다. 스마트 포인터가 평범한 참조자처럼 사용할 수 있는 방식으로 `Deref`를 구현함으로써, 참조자에 대해 작동하는 코드가 스마트 포인터도 작동되도록 할 수 있다.

먼저 `*`가 보통의 참조자와 어떤 식으로 동작하는지 살펴보고, 그런 다음 `Box<T>`와 비슷한 타입을 정의를 해보며 왜 `*`가 새로 정의된 타입에서는 참조자처럼 작동하지 않는지를 살펴보자. Deref 트레잇을 구현하는 것이 어떻게 스마트 포인터가 참조자와 유사한 방식으로 동작하는 것을 가능하게 해 주는지 탐구해보자. 그런 뒤 러스트의 *역참조 강제(deref coercion)* 기능과 이 기능이 어떻게 참조자 혹은 스마트 포인터와 함께 동작하도록 하는지 살펴보자.

### *와 함께 포인터를 따라가서 값을 얻기

보통의 참조자는 포인터 타입이며, 포인터를 생각하는 한 가지 방법은 다른 어딘가에 저장된 값을 가리키는 화살표로서 생각하는 것이다. 다음은 `i32` 값의 참조자를 생성하고 참조자를 따라가서 값을 얻기 위해 역참조 연산자를 사용한다.

변수 `x`는 `i32`값을 갖고 있다. `y`는 `x`의 참조자를 설정했다. `x`가 `5`임을 확신할 수 있지만 만약 `y`안의 값으로 `assert`문을 작성하고 싶다면, 참조자를 따라가서 이 참조자가 가리키고 있는 값을 얻기 위해 `*y`를 사용해야 한다.(그래서 역참조라 한다.) 일단 `y`를 역참조하면, `5`와 비교가능한 `y`가 가리키고 있는 정수 값에 접근하게 된다.

```rust
fn main() {
    let x = 5;
    let y = &x;

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

### Box<T>를 참조자처럼 사용하기

위 예제는 참조자 대신 `Box<T>`를 이용해 재작성될 수 있고 역참조 연산자는 동일한 방식으로 작동된다.

위 예제와의 차이점은 오직 `x`의 값을 가리키는 참조자보다는 `x`를 가리키는 박스의 인스턴스로 `y`를 설정했다는 점이다. 마지막 `assert`문에서, `y`가 참조자일 때 했던 것과 동일한 방식으로 박스 포인터 앞에 역참조 연산자를 사용할 수 있다. 다음으로 ,커스텀 박스 타입을 정의함으로써 `Box<T>`가 역참조 연산자를 사용 가능하게끔 해주는 특별함이 무엇인지 살펴보자.

```rust
fn main() {
    let x = 5;
    let y = Box::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

---

### 커스텀 스마트 포인터 정의하기

어떤 식으로 스마트 포인터가 기본적으로 참조자와 다르게 동작하는지 살펴보기 위해 표준 라이브러리가 제공하는 `Box<T>` 타입과 유사한 스마트 포인터를 만들어보자. 그런 다음 어떻게 역참조 연산자를 사용할 수 있는 기능을 추가하는지 살펴보자. `Box<T>` 타입은 하나의 요소를 가진 튜플 구조체로 정의되므로, `MyBox<T>` 타입을 동일한 방식으로 정의했다. 또한 `Box<T>`에 정의되어 있는 `new` 함수에 맞추기 위해 `new` 함수도 정의한다.

제네릭 파라미터 `T`를 선언했는데, 이는 어떠한 종류의 타입 값이든 가질 수 있길 원하기 때문이다. `MyBox` 타입은 T타입의 하나의 요소를 가진 튜플 구조체다.

`MyBox::new` 함수는 `T`타입인 하나의 파라미터를 받아서 그 값을 갖는 `MyBox` 인스턴스를 반환한다.

```rust
struct MyBox<T>(T);

impl<T> MyBox<T> {
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}
```

---

그렇다면 위 예제에 `Box<T>` 대신 새로 정의한 `MyBox<T>`를 이용해 수정해보자. 이는 컴파일 되지 않을 것인데, 그 이유는 러스트가 `MyBox<T>`를 어떻게 역참조 하는지 모르기 때문이다. `MyBox<T>` 타입은 역참조될 수 없는데, 그 이유는 이 타입에 대해 해당 기능을 아직 구현하지 않았기 때문이다. `*` 연산자로 역참조를 가능케 하기 위해서, `Deref`트레잇을 구현해야 한다.

```rust
fn main() {
    let x = 5;
    let y = MyBox::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

```
error[E0614]: type `MyBox<{integer}>` cannot be dereferenced
  --> src/main.rs:14:19
   |
14 |     assert_eq!(5, *y);
   |                   ^^
```

---

### Deref 트레잇을 구현해 임의의 타입을 참조자 처럼 다루기

앞서 논의한 바와 같이, 트레잇을 구현하기 위해서는 트레잇의 요구 메소드들에 대한 구현체를 제공할 필요가 있다. 표준 라이브러리가 제공하는 `Deref`트레잇은 `self`를 빌려서 내부 데이터에 대한 참조자를 반환하는 `deref`라는 이름의 메소드 하나를 구현하도록 요구한다. `MyBox`의 정의에 덧붙여 `Deref`의 구현을 담아보자.

`type Target = T;` 문법은 `Deref`트레잇이 사용할 연관 타입 (associated type)을 정의한다. 연관 타입은 제네릭 팔하미터를 정의하는 것과 약관 다른 방식이지만, 지금은  자세히 다루지 않고 이후에 자세히 다룬다.

`deref` 메소드의 본체를 `&self.0`으로 채웠으므로 `deref`는 `*`연산자를 이용해 접근하고자 하는 값의 참조자를 반환한다. `MyBox<T>` 값에 대해  `*`를 호출하는 함수는 이제 컴파일되고 `assert`문을 통과할 것이다.

```rust
use std::ops::Deref;

impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &T {
        &self.0
    }
}
```

`*y`에 들어설 때, 무대 뒤에서 러스트는 다음 코드를 실행한다.

```rust
*(y.deref())
```

러스트는 `*` 연산자에 `deref` 메소드 호출 후 보통의 역참조를 대입하므로 프로그래머로서 우리가 `deref` 메소드를 호출할 필요가 있는지 없는지를 생각하지 않아도 된다. 이 러스트의 기능은 보통의 참조자를 가지고 있는 경우 혹은 `Deref`를 구현한 타입을 가지고 있는 경우에 대해서 동일하게 기하는 코드를 작성하도록 해준다.

`deref` 메소드가 값의 참조자를 반환하고 `*(y.deref())` 에서의 괄호 바깥의 평범한 역참조가 여전히 필요한 이유는 소유권 시스템 때문이다. 만일 `deref` 메소드가 값의 참조자 대신 값을 직접 반환했다면, 그 값은 `self` 바깥으로 이동될 것이다. 위의 경우 및 우리가 역참조 연산자를 사용하는 대부분의 경우에서 우리는 `MyBox<T>` 내부의 값에 대한 소유권을 얻길 원치 않는다.

코드에 `*`를 한번 타이핑할 때마다, `*`는 deref함수의 호출 후 `*`를 한번 호출하는 것으로 대치된다는 점을 기억하자. `*`의 대입이 무한히 재귀적으로 실행되지 않기 때문에, 결국 `i32` 타입의 데이터를 얻게 되는데, 이는 `assert_eq!` 내의 `5`와 일치한다.

---

### 함수와 메소드를 이용한 암묵적 역참조 강제

*역참조 강제(deref coercion)* 는 러스트가 함수 및 메소드의 인자에 수행하는 편의성 기능이다. 역참조 강제는 `Deref`를 구현한 어떤 타입의 참조자를 `Deref`가 본래의 타입으로부터 바꿀 수 있는 타입의 참조자로 바꾼다. 역참조 강제는 특정 타입의 값에 대한 참조자를 함수 혹은 메소드의 인자로 넘기는 중 정의된 파라미터 타입에는 맞지 않을 때 자동적으로 발생한다. 일련의 `deref` 메소드 호출은 제공한 타입을 파라미터가 요구하는 타입으로 변경해 준다.

역참조 강제가 러스트에 도입되어서 함수와 메소드 호출을 작성하는 프로그래머들은 `&`와 `*`를 이용한 많은 수의 명시적참조 및 역참조를 추가하지 않아도 된다. 역참조 강제 기능은 또한 우리가 참조자나 스마트 포인터 둘 중 어느 경우라도 작동할 수 있는 코드를 더 많이 작성할 수 있도록 해 준다.

역참조 강제가 실제 작동하는 것을 보기 위해서, 위에서 정의한 `MyBox<T>`와 `Deref`의 구현체를 이용해보자. 스트링 슬라이스 파라미터를 갖는 함수의 정의를 보여준다.

```rust
fn hello(name: &str) {
	println!("Hello, {}!", name);
}
```

예를 들면 `hello(”Rust”);` 와 같이 스트링 슬라이스를 인자로 `hello` 함수를 호출할 수 있다. 또한 위에서 설명한 바와 같이 역참조 강제를 이용해 `MyBox<String>` 타입의 값에 대한 참조자를 이용해 `hello` 함수를 호출하는 것도 가능하다.

```rust
fn main() {
    let m = MyBox::new(String::from("Rust"));
    hello(&m);
}
```

여기서 `hello` 함수를 호출하는 인자로서 `&m`을 이용했는데, 이는 `MyBox<String>`의 참조자다. 위에서 `MyBox<T>`의 `Deref`트레잇을 구현했기 때문에, 러스트는 `deref`를 호출해 `&MyBox<String>` 을 `&String`으로 바꿀 수 있다. 표준 라이브러리는 스트링 슬라이스를 반환하는 `String`의 `Deref`구현체를 제공하는데, 이는 `Deref`에 대한 API문서에도 있다. 따라서 러스트는 한번 더 `deref`를 호출해 `&String`을 `&str`로 변환하고 이제야 `hello`함수를 사용할 수 있게 되는 것이다.

만일 러스트가 역참조 강제 기능을 구현하지 않았다면, 아마 `&MyBox<String>` 타입의 값을 가지고 `hello` 함수를 호출하는 데 있어 다음과 같은 코드를 작성해야 할 것이다.

```rust
fn main() {
    let m = MyBox::new(String::from("Rust"));
    hello(&(*m)[..]);
}
```

역참조 강제가 없는 코드는 이러한 모든 기호들이 수반된 상태에서 읽기도, 쓰기도, 이해하기도 더 힘들어 진다. 역참조 강제는 러스트가 우리를 위해 이러한 변환을 자동적으로 다룰 수 있도록 해 준다. 

`Deref` 트레잇이 관련된 타입에 대해 정의될 때, 러스트는 해당 타입을 분석해 파라미터의 타입에 맞는 참조자를 얻기 위해 필요한 수만큼의 `Deref::deref`를 사용할 것이다. 이때 `Deref::deref`가 호출될 횟수는 컴파일 타임에 분석되므로, 역참조 강제의 이점을 얻는 데에 관해서 어떠한 런타임 페널티도 없다.

## 역참조 강제가 가변성과 상호작용 하는 법

불변 참조자에 대한 `*`를 오버라이딩하기 위해 `Deref`트레잇을 이용하는 방법과 비슷하게, 러스트는 가변 참조자에 대한 `*`를 오버라이딩 하기 위한 `DerefMut` 트레잇을 제공한다.

러스트는 다음 세 가지 경우에 해당하는 타입과 트레잇 구현을 찾았을 때 역참조 강제를 수행한다.

- `T: Deref<Target=U>` 일때 `&T`에서 `&U`로
- `T: DerefMut<Target=U>` 일때 `&mut T`에서 `&mut U`로
- `T: Deref<Target=U>` 일때  `&mut T`에서 `&U`로

첫 두 가지 경우는 가변성 부분만 제외하고는 동일하다. 첫 번째 경우는 만약 `&T`를 가지고 있고, `T`가 어떤 타입 `U`에 대한 `Deref`를 구현했다면, `&U`를 얻을 수 있다. 두 번째 경우는 동일한 역참조 강제가 가변 참조자에 대해서도 발생함을 기술한다.

세 번째 경우를 생각해보자. 러스트는 가변 참조자를 불변참조자로 바꿀 수 도 있다. 하지만 그 역은 불가능하다. 불변 참조자는 가변 참조자로 절때 바뀔 수 없다. 빌림 규칙 때문에, 만일 가변 참조자를 가지고 있다면 , 그 가변 참조자는 해당 데이터에 대한 유일한 참조자임에 틀림없다. (만일 그렇지 않다면, 그 프로그램은 컴파일 되지 않을 것이다). 가변 참조자를 불변 참조자로 변경하는 것은 결코 빌림 규칙을 깨트리지 않을 것이다. 불변 참조자를 가변 참조자로 변경하는 것은 해당 데이터에 대한 단 하나의 불변 참조자가 있어야 한다는 요구를 하게 되고, 이는 빌림 규칙이 보장해줄 수 없다.

---

## Drop 트레잇은 메모리 정리 코드를 실행시킨다.

스마트 포인터 패턴에서 중요한 두 번째 트레잇은 `Drop`인데, 이는 값이 스코프 밖으로 벗어나려고 할때 어떤 일이 발생 될지 커스터마이징 하게끔 해준다. 어떤 타입이든 간에 `Drop` 트레잇을 위한 구현을 제공할 수 있고, 우리가 특정한 코드는 파일이나 네트워크 연결 같은 자원을 해제하는 데에 사용될 수 있다. 지금 스마트 포인터의 맥락 안에서 `Drop`을 소개하고 있는데 그 이유는 `Drop` 트레잇의 기능이 언제나 대부분 스마트 포인터를 구현할 때에 사용되기 때문이다. 예를 들면 `Box<T>`는 박스가 가리키고 있는 힙 상의 공간을 할당 해제하기 위해 `Drop`을 커스터마이징 한다.

몇몇 언어들에서 프로그래머는 스마트 포인터의 인스턴스 사용을 종료하는 매번 마다 메모리 혹은 자원을 해제하기 위해 코드를 호출해야 한다. 만일 이를 잊어버리면, 그 시스템은 과부하가 걸리거나 멈출지도 모른다. 러스트에서는 값이 스코프 밖으로 벗어날 때마다 실행되어야 하는 특정한 코드 조각을 설정할 수 있고, 컴파일러는 이 코드를 자동으로 삽입해줄 것이다. 결과적으로, 프로그램 내에서 특정한 타입의 인스턴스가 종료되는 곳마다 정리 코드를 집어넣는 것에 관한 걱정을 할 필요가 없지만, 여전히 자원 누수는 발생하지 않을 것이다.

`Drop` 트레잇을 구현함으로써 값이 스코프 밖으로 벗어났을 때 실행될 코드를 특정한다. `Drop`트레잇은 `self`에 대한 가변 참조자를 파라미터로 갖는 `drop`이라는 이름의 하나의 메소드를 구현하도록 요구한다. 러스트가 언제 `drop`을 호출하는지 보기 위해서 지금은 `println!` 구문과 함께 `drop` 을 구현해보자.

인스턴스가 스코프 밖으로 벗어났을 때 `Dropping CusomSmartPointer!`를 출력하는 커스텀 기능만을 갖춘 `CustomSmartPointer` 구조체를 구현했다. 이 예제는 러스트가 `drop` 함수를 실행시는 때를 보여준다.

`Drop` 트레잇은 프렐루드에 포함되어 있으므로 따로 가져오지 않아도 된다. 이는 `CustomSmartPointer` 상에 `Drop` 트레잇을 구현했고, `println!`을 호출하는 `drop` 메소드 구현을 제공했다. `drop` 함수 내용은 우리가 만든 타입의 인스턴스가 스코프 밖으로 벗어났을 때 실행시키고자 하는 어떠한 로직이라도 위치시킬 수 있는 곳이다. 러스트가 `drop`을 호출하게 될 때를 보여주기 위해서 어떤 텍스트를 출력하는 중이다.

`main`에서는 두 개의 `CustomSmartPointer` 인스턴스를 만든 다음 `CustomSmartPointers created.`를 출력한다. `main`의 끝에서, `CustomSmartPointer` 인스턴스는 스코프 밖으로 벗어날 것이고, 러스트는 `drop` 메소드 내에 집어넣은 코드, 즉 마지막 메시지를 출력하는 코드를 호출할 것이다. 따로 `drop` 메소드를 명시적으로 호출할 필요가 없다는 점을 주의하자.

```rust
struct CustomSmartPointer {
    data: String,
}

impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        println!("Dropping CustomSmartPointer with data `{}`!", self.data);
    }
}

fn main() {
    let c = CustomSmartPointer { data: String::from("my stuff") };
    let d = CustomSmartPointer { data: String::from("other stuff") };
    println!("CustomSmartPointers created.");
}
```

---

```
CustomSmartPointers created.
Dropping CustomSmartPointer with data `other stuff`!
Dropping CustomSmartPointer with data `my stuff`!
```

러스트는 우리의 인스턴스가 스코프 밖으로 벗어났을 때 `drop`을 호출했고, 우리가 작성한 그 코드를 호출하게 된다. 변수들은 만들어진 순서의 역순으로 버려지므로, `d`는 `c`전에 버려진다. 이 예제 `drop` 메소드가 어떻게 동작하는지에 대한 가이드만을 제공하지만, 보통 메시지 출력보다는 타입이 실행할 필요가 있는 정리 코드를 작성할 것이다.

---

### `std::mem::drop`을 이용하여 값을 일찍 버리기

불행하게도, 자동적인 `drop` 기능을 비활성화하는 것은 직관적이지 않다. 보통, 보통 `drop`을 비활성화할 필요가 없다. `Drop` 트레잇의 전체적 관점은 자동적으로 이루어진다는 것이다. 하지만 가끔은 값을 일찍 정리하기를 원할지도 모른다. 한 가지 예는 락을 관리하는 스마트 포인터를 이용할 때문이다. 우리가 실행할 락을 해제하는 `drop` 메소드를 강제로 실행시켜서 같은 스코프 내의 다른 코드가 락을 얻을 수 있길 원할지도 모른다. 러스트는 수동으로 `Drop` 트레잇의 `drop` 메소드를 호출하도록 해주지 않는다. 대신 우리가 스코프 밖으로 벗어나기 전에 값이 강제로 버려지길 원한다면 표준 라이브러리에서 제공하는 `std::mem::drop` 함수를 호출해야 한다.

다음 처럼 `Drop` 트레잇의 `drop` 메소드를 호출하려고 하면 어떤 일이 벌어지는지 보자.

```rust
fn main() {
    let c = CustomSmartPointer { data: String::from("some data") };
    println!("CustomSmartPointer created.");
    c.drop();
    println!("CustomSmartPointer dropped before the end of main.");
}
```

```
error[E0040]: explicit use of destructor method
  --> src/main.rs:14:7
   |
14 |     c.drop();
   |       ^^^^ explicit destructor calls not allowed
```

---

이 에러 메시지는 `drop`을 명시적으로 호출하는 것이 허용되지 않음을 기술하고 있다. 에러 메시지는 *소멸자(destructor)*라는 용어를 사용하는데, 이는 인스턴스를 정리하는 함수에 대한 일반적인 프로그래밍 용어다. 소멸자는 인스턴스를 생성하는 *생성자(constructor)*와 비슷하다. 러스트 내의 `drop`함수는 특정한 형태의 소멸자다.

러스트가 `drop`을 명시적으로 호출하도록 허용하지 않는 이유는 `main`의 끝에서 값에 대한 `drop` 호출을 자동적으로 할 것이기 때문이다. 이렇게 되면 러스트가 동일한 값을 두 번 메모리 정리를 시도할 도 있고, 이는 *중복 해제(double free)* 에러가 될 수 있다. 

이런 스코프 밖으로 벗어났을 때 자동적으로 `drop` 메소드가 실행되는 것을 비활성화할 수 없고, `drop` 메소드를 명시적으로 호출할 수도 없다. 따라서 값이 일찍 메모리 에서 정리되도록원한다면, `std::mem::drop` 함수를 이용할 수 있다.

`std::mem::drop` 함수는 `Drop` 트레잇 내에 있는 `drop` 메소드와 다르다.  일찍 버리고 싶은 값을 인자로 넘김으로써 이를 호출할 수 있다. 이를 이용해 위 코드를 다음과 같이 수정할 수 있다.

```rust
fn main() {
    let c = CustomSmartPointer { data: String::from("some data") };
    println!("CustomSmartPointer created.");
    drop(c);
    println!("CustomSmartPointer dropped before the end of main.");
}
```

위 코드의 실행은 다음을 출력할 것이다.

```
CustomSmartPointer created.
Dropping CustomSmartPointer with data `some data`!
CustomSmartPointer dropped before the end of main.
```

`Dropping CustomSmartPointer with data `some data`!`라는 텍스트가 `CustomSmartPointer created.`와 `CustomSmartPointer dropped before the end of main.` 사이에 출력되는데, 이는 `c`를 그 지점에서 버리기 위해 `drop` 메소드 코드가 호출되었음을 보여준다.

---

메모리 정리를 편리하고 안전하게 하기 위하여 `Drop` 트레잇 구현체 내에 특정된 코드를 다양한 방식으로 이용할 수 있다. 예를 들면, 이것을 우리만의 고유한 메모리 할당자를 만들기 위해 사용할 수도 있다. `Drop` 트레잇과 러스트의 소유권 시스템을 이용하면, 러스트가 메모리 정리를 자동적으로 수행하기 때문에 메모리 정리를 기억하지 않아도 된다.

또한 계속 사용 중인 값이 뜻하지 않게 정리되는 것을 걱정하지 않아도 되는데, 그런 것은 컴파일 에러를 야기할 것이기 때문이다. 참조자가 항상 유효하도록 확실히 해주는 소유권 시스템은 또한 값이 더 이상 사용되지 않을 때 `drop`이 오직 한 번만 호출될 것을 보장한다.

---

## Rc<T>, 참조 카운팅 스마트 포인터

소유권은 명확하다. 즉 어떤 변수가 주어진 값을 소유하는지 정확히 알고 있다. 그러나, 하나의 값이 여러개의 소유자를 가질 수도 있는 경우가 있다. 예를 들면, 그래프 데이터 구조에서, 여러 Edge가 동일한 노드를 가리킬 수도 없고, 그 노드는 개념적으로 해당 노드를 가리키는 모든 Edge들에 의해 소유된다. 노드는 어떠한 Edge도 이를 가리키지 않을 때까지는 메모리 정리가 되어서는 안된다.

복수 소유권을 가능하게 하기 위해서, 러스트는 `Rc<T>`라 불리우는 타입을 가지고 있다. 이 이름은 *참조 카운팅 (referenmce counting)*의 약자인데, 이는 어떤 값이 계속 사용되는지 혹은 그렇지 않은지를 알기 위해 해당 값에 대한 참조자의 갯수를 계속 추적하는 것이다. 만일 값에 대한 참조자가 0개라면, 그 값은 어떠한 참조자도 정리할 필요 없이 메모리 정리될 수 있다.

`Rc<T>`를 거실의 TV로 상상해 보자. 만일 한 사람이 TV를 보러 들어온다면, TV를 킨다. 다른 사람들은 거실로 돌아와서 TV를 볼 수 있다. 마지막 사람이 거실을 나선다면, TV는 더이상 사용되지 않으므로 이를 끈다. 만일 다른 사람들이 여전히 TV를 보고 있는 중에 누군가가 이를 끈다면, 남은 TV 시청자들로부터 엄청난 반란이 있을 것이다!

만약 프로그램의 여러 부분에서 읽을 데이터를 힙에 할당하고 싶고, 어떤 부분이 그 데이터를 마지막에 이용하게 될지 컴파일 타임에는 알 수 없는 경우 `Rc<T>` 타입을 사용한다. 만일 어떤 부분이 마지막으로 사용하는지 알 수 있다면, 그냥 그 해당 부분을 데이터의 소유자로 만들면 되고, 컴파일 타임에 집행되는 보통의 소유권 규칙이 효과를 발생시킬 것이다. 

`Rc<T>`가 오직 단일 스레드 시나리오 상에서만 사용 가능하다점을 주의하자. 이후에 동시성을 논의할 때, 다중 스레드 프로그램에서는 어떻게 참조 카운팅 하는지 다루겠다.

### Rc<T>를 사용하여 데이터 공유하기

cons list 예제로 돌아가 보자. `Box<T>`를 이용해서 정의했던 것을 상기하자. 이번에는 세 번째 리스트의 소유권을 둘 다 공유하는 두 개의 리스트를 만들 것인데, 이는 개념적으로 다음과 유사하게 보일 것이다.

![세 번째 리스트 a의 소유권을 공유하는 두 리스트 b와 c](Rust%200d7ead296adb4593aa99b2b8cb2e5307/Untitled%201.png)

세 번째 리스트 a의 소유권을 공유하는 두 리스트 b와 c

`5`와 `10`을 담은 리스트 `a`를 만들 것이다. 그런 다음 두 개의 리스트를 더 만들 것이다. `3`으로 시작하는 `b`와 `4`로 시작하는 `c`다. 그리고 나서 `b`와 `c`리스트 둘 모두 `5`와 `10`을 담고 있는 첫번째 `a` 리스트로 계속되게 할 것이다. 바꿔 말하면, 두 리스트 모두 `5`와 `10`을 담은 첫 리스트를 공유하게 될 것이다.

이전에 `Box<T>`를 가지고  정의한 `List`를 이용해 구현하려는 첫 번째 시도는 작동하지 않을 것이다.

```rust
enum List {
    Cons(i32, Box<List>),
    Nil,
}

use List::{Cons, Nil};

fn main() {
    let a = Cons(5,
        Box::new(Cons(10,
            Box::new(Nil))));
    let b = Cons(3, Box::new(a));
    let c = Cons(4, Box::new(a));
}
```

```
error[E0382]: use of moved value: `a`
  --> src/main.rs:13:30
   |
12 |     let b = Cons(3, Box::new(a));
   |                              - value moved here
13 |     let c = Cons(4, Box::new(a));
   |                              ^ value used here after move
   |
   = note: move occurs because `a` has type `List`, which does not implement
   the `Copy` trait
```

---

`Cons` variant는 이것을 가지고 있는 데이터를 소유하므로, 리스트 `b`를 만들 때 `a`는 `b` 안으로 이동되고 `b`는 `a`를 소유한다. 그 뒤, `c`를 생성할 때 `a`를 다시 이용하는 시도를 할 경우, 이는 `a`가 이미 이동 되었으므로 허용되지 않는다. 

`Cons`가 대신 참조자를 갖도록 정의를 변경할 수도 있지만, 그러면 라이프타임 파라미터를 명시해야 한다. 라이프타임 파라미터를 명시함으로써, 리스트 내의 모든 요소들이 최소한 전체 리스트 만큼 오래 살아있을 것이다. 예를 들어 빌림 검사기는 라이프타임 파라미터를 명시함으로써, `let a = Consd(10, &Nil);`의 `&Nil`과 같은 임시 값에 대한 참조를 사용할 수 있게 해준다. 그러나 경우에 따라 적합한 라이프타임 매개변수를 지정하는 것이 어렵거나 비실용적일 수 있다.

대신에, 아래와 같이 `Box<T>`의 자리에 `Rc<T>`를 이용하여 `List`의 정의를 바꿀 것이다. 각각의 `Cons variant`는 이제 어떤 값과 `List`를 가리키는 `Rc<T>` 를 갖게 될 것이다. `b`를 만들때는 `a`의 소유권을 얻는 대신, `a`를 가지고 있는  `Rc<List>`를 클론할 것인데, 이는 참조자의 갯수를 하나에서 둘로 증가시키고 `a`와 `b`가 `Rc<List>` 안에 있는 값을 공유하게 해준다. 또한 `c`를 만들때도  `a`를 클론할 것인데, 이는 참조자의 갯수를 둘에서 셋으로 늘릴 것이다. `Rc::clone`을 호출하는 매번, 해당 `Rc<List>` 가 가지고 있는 데이터에 대한 참조 카운트는 증가할 것이고, 그 데이터는 참조자가 `0`개가 되지 않으면 메모리 정리되지 않을 것이다.

```rust
enum List {
    Cons(i32, Rc<List>),
    Nil,
}

use List::{Cons, Nil};
use std::rc::Rc;

fn main() {
    let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
    let b = Cons(3, Rc::clone(&a));
    let c = Cons(4, Rc::clone(&a));
}
```

`Rc<T>` 는 프렐루드에 포함되어 있지 않으므로 이를 가져오기 위해 따로 `use` 구문을 추가할 필요가 없다. `main` 내에서, `5`와 `10`을 가지고 있는 리스트를 만들어서 이를 `a`의 새로운 `Rc<List>` 에 저장합니다. 그 다음 `b`와 `c`를 만들 때, 우리는 `Rc::clone` 함수를 호출하고 `a`의 `Rc<List>`에 대한 참조자를 인자로서 넘긴다.

`Rc::clone(&a)` 보다는 `a.clone()`을 호출할 수도 있지만, 위의 경우 러스트의 관례는 `Rc::clone` 을 이용하는 것이다. `Rc::clone`의 구현체는 대부분의 타입들의 `clone` 구현체들이 하는 것처럼 모든 데이터의 깊은 복사 (deep copy)를 만들지 않는다. `Rc::clone`의 호출은 오직 참조 카운트만 증가 시키는데, 이는 큰 시간이 들지 않는다. 데이터의 깊은 복사는 많은 시간이 걸릴 수 있다. 참조 카운팅을 위해 `Rc::clone`을이용함으로써, *<깊은 복사 종류의 클론>*과 *<참조 카운트를 증가시키는 종류의 클론>*을 시각적으로 구별할 수 있다. 코드 내에서 성능 문제를 찾고 있다면, 깊은 복사 클론만 고려할 필요가 있고 `Rc::clone`은 무시할 수 있다.

### Rc<T> 의 클론 생성은 참조 카운트를 증가시킨다

동작 예제를 변경해 `a` 내부의 `Rc<List>`에 대한 참조자가 생성되고 드롭될 때 참조 카운트의 변화를 볼 수 있도록 해보자.

다음은 `main`을 변경해 리스트 `c`를 감싸고 있는 내부 스코프를 갖도록 한다. 그런다음 `c`가 스코프 밖으로 벗어났을 때 참조 카운트가 어떻게 변하는지 볼 수 있다. 프로그램 내 참조 카운트가 변하는 각 지점에서, 참조 카운트 값을 출력할텐데, 이는 `Rc::strong_count` 함수를 호출함으로써 얻을 수 있다. 이 함수는 `count` 보다는 `strong_count`라는 이름을 갖고 있는데 이는 `Rc<T>` 타입이 `weak_count`도 갖고 있기 때문이다; `weak_count`가 무엇을 위해 사용 되는지는 이후에 다룬다.

```rust
fn main() {
    let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
    println!("count after creating a = {}", Rc::strong_count(&a));
    let b = Cons(3, Rc::clone(&a));
    println!("count after creating b = {}", Rc::strong_count(&a));
    {
        let c = Cons(4, Rc::clone(&a));
        println!("count after creating c = {}", Rc::strong_count(&a));
    }
    println!("count after c goes out of scope = {}", Rc::strong_count(&a));
}
```

```
count after creating a = 1
count after creating b = 2
count after creating c = 3
count after c goes out of scope = 2
```

기본적으로 `a`의 `Rc<List>`가 초기 참조 카운트로서 `1`을 갖는 것을 볼 수 있다. 그 다음 우리가 `clone`을 호출하는 매번마다, 카운트는 `1`씩 증가한다. `c`가 스코프 밖으로 벗어날 때, 카운트는 `1`만큼 감소한다. 참조 카운트를 증가시키기 위해서 `Rc::clone`을 호출해야 하는 것과 같이 참조 카운트를 감소시키기 위해 어떤 함수를 호출하지 않아도 된다. `Rc<T>` 값이 스코프 밖으로 벗어나면 `Drop` 트레잇의 구현체가 자동으로 참조 카운트를 감소시킨다.

이 예제에서 볼수 없는 것은 `main`의 끝에서 `b` 와 그 다음 `a`가 스코프 밖을 벗어나서 카운트가 0이되고 그 시점에서 `Rc<List>`가 완전히 메모리 정리되는 때다. `Rc<T>`를 이용하는 것은 단일값이 복수 개의 소유자를 갖도록 허용해주고, 이 카운트는 소유자중 누구라도 여전히 존재하는 한 값이 계속 유효함을 확실히 해준다.

불변 참조자를 통하여, `Rc<T>`는 읽기 전용으로 프로그램의 여러 부분 사이에서 데이터를 공유하도록 허용해준다. 만약 `Rc<T>`가 또한 복수 개의 가변 참조자도 갖는 것을 허용한다면, 이는 빌림 규칙 중 하나를 위반할지도 모른다. 다시말하면 동일한 위치에 대한 복수개의 가변 빌림은 데이터 레이스 및 데이터 불일치를 야기할 수 있다는 점이다. 하지만 데이터의 변형을 가능하게 하는 것은 매우 유용하다! 이후 내부 가변성 (interiro mutablility) 패턴과 이러한 불변성 제약과 함께 동작하기 위해 `Rc<T>`와 같이 결합하여 사용할 수 있는 `RefCell<T>`타입에 대해 논의할 것이다.

---

## RefCell<T>와 내부 가변성 패턴

내부 가변성 (interior mutabliliy) 은 어떤 데이터에 대한 불변 참조자가 있을 때라도 데이터를 변형할 수 있게 해주는 러스트의 디자인 패턴이다. 보통 이러한 동작은 빌림 규칙에 의해 허용되지 않는다. 그렇게 하기 위해서, 이 패턴은 변형과 빌림을 지배하는 러스트의 통상적인 규칙을 구부리기 위해서 데이터 구조 내에서 `unsafe (안전하지 않은)` 코드를 사용하다. 여태것 안전하지 않은 코드를 다루지 않았다. 이는 이후에 다룰 것이다. 만약 런타임에 빌림 규칙을 따를 것임을 보장할 수 있을 때라면, 심지어 컴파일러가 이를 보장하지 못하더라도 내부 가변성 패턴을 이용하는 타입을 사용할 수 있다. 포함되어 있는 `unsafe` 코드는 안전한 API로 감싸져 있고, 외부 타입은 여전히 불변이다. 내부 가변성 패턴을 따르는 `RefCell<T>` 타입을 살펴보는 것으로 이 개념을 탐구해 보자.

### RefCell<T>을 가지고 런타임에 빌림 규칙을 적용하기

`Rc<T>`와는 다르게, `RefCell<T>` 타입은 가지고 있는 데이터 상에 단일 소유권을 나타낸다. 그렇다면, `Box<T>`와 같은 타입에 비교해 `RefCell<T>`의 다른 부분은 무엇일까? 우선 앞서 배웠던 빌림 규칙을 기억 해보자.

- 어떤 경우든 간에, 다음의 둘 중 하나만을 가질 수 있다.
    - 하나의 가변 참조자
    - 임의 개수의 불변 참조자
- 참조자는 항상 유효해야 한다.

참조자와 `Box<T>`를 이용할 때, 빌림 규칙의 불변성은 컴파일 타임에 적용된다. 하지만 `RefCell<T>`를 이용할 때는, 이 빌림 규칙이 런타임에 적용된다. 참조자를 가지고 이 규칙을 어기면 컴파일러 에러를 얻게 될 것이다. 하지만 `RefCell<T>`를 가지고서 이 규칙을 어기면 이는 런타임에 어긴 것이므로, 프로그램은 `panic!`을 일으키고 종료될 것이다.

컴파일 타임에 빌림 규칙을 검사하는 것은 개발 과정에서 에러를 더 일찍 잡을 수 있다는 점, 그리고 이 모든 분석이 사전에 완료되기 때문에 런타임 성능에 영향이 없다는 점에서 장점을 가진다. 그렇기 때문에, 대부분의 경우 컴파일 타임에 빌림 규칙을 검사하는 것이 가장 좋은 선택이고, 이것이 러스트의 기본 설정인 이유이기도 하다.

대신 런타임에 빌림 규칙을 검사하는 것은 컴파일 타임 검사에 의해서는 허용되지 않았을, 특정한 메모리 안정성 시나리오가 허용된다는 점이 있다. 러스트 컴파일러와 같은 정적 분석은 태생적으로 보수적이다. 어떤 코드 속성은 코드의 분석을 이용해서는 발견이 불가능하다. 가장 유명한 예제는 정지 문제 (halting problem) 인데, 이는 러스트의 범위를 벗어나지만 연구하기에 흥미로운 주제다.

몇몇 분석이 불가능 하기 때문에, 만일 코드가 소유권 규칙을 준수한다는 것을 러스트 컴파일러가 확신할 수 없다면, 컴파일러는 올바른 프로그램을 거부할지도 모른다. 그렇기 때문에 컴파일러는 보수적이다. 만일 러스트가 올바르지 않은 프로그램을 받아들이면, 사용자들은 러스트가 보장하는 것을 신뢰할 수 없을 것이다. 하지만, 만일 러스트가 올바른 프로그램을 거부한다면, 프로그래머는 불편해할 것이지만, 어떠한 재앙도 일어나지 않을 수 있다. `RefCell<T>` 타입은 코드가 빌림 규칙을 따를 것을 프로그래머가 확신하지만, 컴파일러는 이를 이해하고 보장할 수 없을 경우 유용하다.

`Rc<T>`와 유사하게, `RefCell<T>`는 단일 스레드 시나리오 내에서만 사용 가능하고, 만일 다중 스레드 맥락 내에서 사용할 경우 컴파일 에러를 줄 것이다. `RefCell<T>`의 기능을 다중 스레드 프로그램 내에서 사용하는 방법은 이후에 다룬다.

`Box<T>`, `Rc<T>`, 혹은 `RefCell<T>`를 선택하는 이유의 요점은 다음과 같다.

- `Rc<T>`는 동일한 데이터에 대해 복수개의 소유자를 가능하게 한다. `Box<T>`와 `RefCell<T>`는 단일 소유자만 갖는다.
- `Box<T>`는 컴파일 타임에 검사된 불변 혹은 가변 빌림을 허용한다. `Rc<T>`는 오직 컴파일 타임에 검사된 불변 빌림만 허용한다. `RefCell<T>`는 런타임에 검사된 불변 혹은 가변 빌림을 허용한다.
- `RefCell<T>`가 런타임에 검사된 가변 빌림을 허용하기 때문에, `RefCell<T>`이 불변일 때라도 `RefCell<T>`내부의 값을 변경할 수 있다.

불변값 내부의 값을 변경하는 것을 내부 가변성 패턴이라고 한다. 내부 가변성 이 유용한 경우를 살펴보고 이것이 어떻게 가능한지 보자.

---

### 내부 가변성: 불변값에 대한 가변 빌림

빌림 규칙의 결과로 인해 불변값을 가지고 있다면 이를 변경 가능하게 빌릴 수 없다. 예를 들면 다음 코드는 컴파일 되지 않는다.

```rust
fn main() {
    let x = 5;
    let y = &mut x;
}
```

이 코드를 컴파일하면 다음과 같은 에러를 얻게 된다.

```
error[E0596]: cannot borrow immutable local variable `x` as mutable
 --> src/main.rs:3:18
  |
2 |     let x = 5;
  |         - consider changing this to `mut x`
3 |     let y = &mut x;
  |                  ^ cannot borrow mutably
```

---

하지만, 값이 자신의 메소드 내부에서는 변경되지만 그 외의 다른 코드에서는 불변인 것으로 보이는 것이 유용할 수 있는 경우가 있다. 그 메소드 바깥의 코드는 값을 변경할 수 없을 것이다. `RefCell<T>`를 이용하는 것은 내부 가변성의 기능을 얻는 한가지 방법이다. 그러나 `RefCell<T>`는 빌림 규칙을 완벽하게 피하는 것은 아니다. 컴파일러 내의 빌림 검사기는 이러한 내부 가변성을 허용하고, 빌림 규칙은 대신 런타임에 검사된다. 만일 이 규칙을 위반하면, 컴파일러 에러 대신 `panic!`을 얻을 것이다.

불변 값을 변경하기 위해 `RefCell<T>`를 이용할 수 있는 실질적인 예제를 살펴보고 이것이 왜 유용한지를 알아보자.

---

**내부 가변성에 대한 용례: 목(mock) 객체** 

*테스트 더블 (test double)* 은 테스트하는 동안 프로그래머는 특정한 행동을 관찰하고 그것이 올바르게 구현되었다고 주장하기 위해 다른 타입응ㄹ 사용하는 것이다. 목 객체 (mock object) 는 테스트 중 어떤 일이 일어났는지 기록해 정확한 동작이 일어났음을 단언할 수 있도록 하는 테스트 더블의 특정한 타입이다.

러스트는 다른 언어들이 객체를 가지는 것과 동일한 의미의 객체를 가지고 있지 않고, 러스트는 몇몇 다른 언어들이 제공하는 것 같은 표준 라이브러리에 미리 만들어진 목 객체 기능이 없다. 하지만 목 객체와 동일한 목적을 제공할 구조체를 당연히 만들 수 있다.

다음은 테스트할 시나리오다: 지금부터 최대값에 맞서 값을 추적하고 현재 값이 최대값에 얼마나 근접한지를 기반으로 메세지를 전송하는 라이브러리를 만들 것이다. 이 라이브러리는 예를 들면 한 명의 유저에게 허용되고 있는 API 호출수의 허용량을 추적하는데 사용될 수 있다.

이 라이브러리는 오직 어떤 값이 최대값에 얼마나 근접한지를 추적하고 어떤 시간에 어떤 메세지를 보내야 할지 정하는 기능만을 제공할 것이다. 이 라이브러리를 사용하는 어플리케이션이 메세지를 전송하는 것에 대한 메커니즘을 제공할 것이다. 해당 어플리케이션은 메세지를 어플리케이션 내에 집어넣거나, 이메일을 보내거나, 문자 메세지를 보내거나, 혹은 기타 다른 것을 할 수 있다. 라이브러리 입장에서는 그런 자세한 사항을 알 필요가 없다. 해야할 것은 우리가 제공할 `Messenger`라는 이름의 트레잇을 구현하는 것이다. 다음은 라이브러리 코드를 보여준다.

---

```rust
pub trait Messenger {
    fn send(&self, msg: &str);
}

pub struct LimitTracker<'a, T: 'a + Messenger> {
    messenger: &'a T,
    value: usize,
    max: usize,
}

impl<'a, T> LimitTracker<'a, T>
    where T: Messenger {
    pub fn new(messenger: &T, max: usize) -> LimitTracker<T> {
        LimitTracker {
            messenger,
            value: 0,
            max,
        }
    }

    pub fn set_value(&mut self, value: usize) {
        self.value = value;

        let percentage_of_max = self.value as f64 / self.max as f64;

        if percentage_of_max >= 0.75 && percentage_of_max < 0.9 {
            self.messenger.send("Warning: You've used up over 75% of your quota!");
        } else if percentage_of_max >= 0.9 && percentage_of_max < 1.0 {
            self.messenger.send("Urgent warning: You've used up over 90% of your quota!");
        } else if percentage_of_max >= 1.0 {
            self.messenger.send("Error: You are over your quota!");
        }
    }
}
```

```rust
#[cfg(test)]
mod tests {
    use super::*;

    struct MockMessenger {
        sent_messages: Vec<String>,
    }

    impl MockMessenger {
        fn new() -> MockMessenger {
            MockMessenger { sent_messages: vec![] }
        }
    }

    impl Messenger for MockMessenger {
        fn send(&self, message: &str) {
            self.sent_messages.push(String::from(message));
        }
    }

    #[test]
    fn it_sends_an_over_75_percent_warning_message() {
        let mock_messenger = MockMessenger::new();
        let mut limit_tracker = LimitTracker::new(&mock_messenger, 100);

        limit_tracker.set_value(80);

        assert_eq!(mock_messenger.sent_messages.len(), 1);
    }
}
```

---

위 테스트는 한 가지 문제점이 있다.

```
error[E0596]: cannot borrow immutable field `self.sent_messages` as mutable
  --> src/lib.rs:52:13
   |
51 |         fn send(&self, message: &str) {
   |                 ----- use `&mut self` here to make mutable
52 |             self.sent_messages.push(String::from(message));
   |             ^^^^^^^^^^^^^^^^^^ cannot mutably borrow immutable field
```

메세지를 추적하기 위해 `MockMessenger`를 수정할 수 없는데 그 이유는 `send` 메소드가 `self`의 불변 참조자를 파라미터로 갖기 때문이다. 또한 에러 메세지로부터 `&mut self`를 대신 사용하라는 제안도 얻을 수 없는데, 그렇게 되면 `send`의 시그니처가 `Messenger` 트레잇의 정의에 있는 시그니처와 일치하지 않을 것이다.

---

여기서 내부 가변성을 사용할 수 있다. `sent_messages`를 `RefCell<T>` 내에 저장할 것이고, 그러면 `send` 메소드는 `sent_message`를 수정할 수 있을 것이다.

```rust
#[cfg(test)]
mod tests {
    use super::*;
    use std::cell::RefCell;

    struct MockMessenger {
        sent_messages: RefCell<Vec<String>>,
    }

    impl MockMessenger {
        fn new() -> MockMessenger {
            MockMessenger { sent_messages: RefCell::new(vec![]) }
        }
    }

    impl Messenger for MockMessenger {
        fn send(&self, message: &str) {
            self.sent_messages.borrow_mut().push(String::from(message));
        }
    }

    #[test]
    fn it_sends_an_over_75_percent_warning_message() {
        // --snip--

        assert_eq!(mock_messenger.sent_messages.borrow().len(), 1);
    }
}
```

`sent_messages`의 타입을 `Vec<String>` 대신 `RefCell<Vec<String>>` 타입으로 변경했다.

`send` 메소드의 구현부의 첫번째 파라미터는 여전히 `self`의 불변 빌림 형태인데, 이는 트레잇의 정의와 일치한다. 하지만 `self.sent_messages` 내의 `RefCell<Vec<String>>` 상에 있는 `brrow_mut`를 호출해 `RefCell<Vec<String>>` 내의 값에 대한 가변 참조자를 얻을 수 있다. 이렇게 극 벡터에 대한 가변 참조자 상의 `push`를 호출할 수 있다.

마지막 `assert` 문에서 내부 벡터 내에 몇 개의 아이템이 있은지 보기 위해 `RefCell<Vec<String>>` 상의 `borrow`를 호출해 벡터에 대한 불변 참조자를 얻는다.

---

### RefCell<T>는 런타임에 빌림을 검사한다.

보통 불변 및  가변 참조자를 만들 때, 각각 `&`와 `&mut`을 사용한다. `RefCell<T>`을 이용할때는 `borrow`와 `borrow_mut` 메소드를 사용하는데, 이는 `RefCell<T>`가 가지고 있는 안전한 API 중 일부다. `borrow` 메소드는 스마트 포인터 타입인 `Ref<T>`를 반환하고, `borrow_mut`는 스마트 포인터 타입 `RefMut<T>`를 반환한다. 두 타입 모두 `Deref`를 구현하였으므로, 이들을 보통의 참조자처럼 다룰 수 있다.

`RefCell<T>`는 현재 활성화된 스마트 포인터 `Ref<T>`와 `RefMut<T>`가 몇 개나 있는지 추적한다. `borrow`를 호출할 때마다 활성화된 갯수를 증가시키며, 스코프 밖으로 벗어날 때, 갯수를 감소시킨다. 잘 알고 있는 빌림 규칙과 똑같이, `RefCell<T>`는 어떤 시점에서든 여러 개의 불변 빌림 혹은 하나의  가변 빌림을 가질 수 있도록 한다.

만일 이 규칙을 위반한다면, `RefCell<T>`는 컴파일 에러를 내기 보다는 런타임에 `panic!`을 일으킬 것이다.

---

```rust
impl Messenger for MockMessenger {
    fn send(&self, message: &str) {
        let mut one_borrow = self.sent_messages.borrow_mut();
        let mut two_borrow = self.sent_messages.borrow_mut();

        one_borrow.push(String::from(message));
        two_borrow.push(String::from(message));
    }
}
```

```
---- tests::it_sends_an_over_75_percent_warning_message stdout ----
    thread 'tests::it_sends_an_over_75_percent_warning_message' panicked at
    'already borrowed: BorrowMutError', src/libcore/result.rs:906:4
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```

빌림 에러를 컴파일 타임이 아니라 런타임에 잡는다는 것은 개발 과정 이에 코드의 실수를 발견할 것이란 의미고, 심지어는 코드가 프로덕션 으로 배포될 때 까지도 발견되지 않을 가능성도 있다.  또한,컴파일 타입 대신  런타임에 빌림을 추적하는 결과로서 약간의 런타임 성능 페널티도 초래할 것이다. 그러나 `RefCell<T>`를 사용하는 것은 오직 불변 값만 허용하는 콘텍스트 내에서 스스로를변경할 수 있는 목 객체를 작성하도록 도와준다. 일반적인 참조자가 제공하는 것보다 더 많은 기능을 얻기 위해서 트레이드 오프에도 불구하고 `RefCell<T>`를 사용할 수 있다.

---

### Rc<T>와 RefCell<T>를 조합해 가변 데이터의 복수 소유자 만들기

`RefCell<T>`를 사용하는 일반적인 방법은 `Rc<T>`와 함께 조합하는 것이다. `Rc<T>`이 어떤 데이터에 대해 복수의 소유자를 허용하지만, 그 데이터에 대한 불변 접근만 제공하는 것을 기억하자. 만일 `RefCell<T>`을 들고 있는 `Rc<T>`를 갖는다면, 변경 가능*하면서* 복수의 소유자를 갖는 값을 가질 수 있다!

예를 들면, Listing 15-18에서 우리가 어떤 리스트의 소유권을 공유하는 여러 개의 리스트를 가질 수 있도록 하기 위해 `Rc<T>`를 사용했던 cons 리스트 예제를 기억해보자. `Rc<T>`가 오직 불변의 값만을 가질 수 있기 때문에, 우리가 이들을 일단 만들면 리스트 안의 값들을 변경하는 것은 불가능했다. 이 리스트 안의 값을 변경하는 능력을 얻기 위해서 `RefCell<T>`을 추가해 보자. Listing 15-24는 `Cons` 정의 내에 `RefCell<T>`를 사용함으로써 우리가 모든 리스트 내에 저장된 값을 변경할 수 있음을 보여준다:

```rust
#[derive(Debug)]
enum List {
    Cons(Rc<RefCell<i32>>, Rc<List>),
    Nil,
}

use List::{Cons, Nil};
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
    let value = Rc::new(RefCell::new(5));

    let a = Rc::new(Cons(Rc::clone(&value), Rc::new(Nil)));

    let b = Cons(Rc::new(RefCell::new(6)), Rc::clone(&a));
    let c = Cons(Rc::new(RefCell::new(10)), Rc::clone(&a));

    *value.borrow_mut() += 10;

    println!("a after = {:?}", a);
    println!("b after = {:?}", b);
    println!("c after = {:?}", c);
}
```

우선, `Rc<RefCell<i32>>`의 인스턴스인 값을 생성하고 `value`라는 이름의 변수 안에 이를 저장하여 나중에 이를 직접 접근할 수 있게 했다. 그런 다음 `value`를 가지고 있는 `Cons` variant와 함께 `a`에다 `List`를 생성했다. `value`에서 `a`로 소유권이 이전되거나 `value`로부터 빌린 `a` 보다는 `a`와 `value` 둘다 내부의 `5` 값에 대한 소유권을 얻기 위해서는 `value`를 클론할 필요가 있다.

`a`, `b`, 그리고 `c` 리스트를 생성한 이후,  `value` 내의 값에 10을 더했다. `value` 상의 `borrow_mut`를 호출함으로써 수행되었는데, 이는 내부의 `RefCell<T>`값을 가리키는 `Rc<T>`를 역참조하기 위해서 5장에서 논의했던 자동 역참조 기능을 사용한 것이다.`borrow_mut` 메소드는 `RefMut<T>` 스마트 포인터를 반환하고, 우리는 여기에 역참조 연산자를 사용한 다음 내부 값을 변경한다.

`a`, `b`, 그리고 `c`를 출력할때,  이 리스트들이 모두 5가 아니라 변경된 값 15를 가지고 있는 것을 볼 수 있다.

```
a after = Cons(RefCell { value: 15 }, Nil)
b after = Cons(RefCell { value: 6 }, Cons(RefCell { value: 15 }, Nil))
c after = Cons(RefCell { value: 10 }, Cons(RefCell { value: 15 }, Nil))
```

`RefCell<T>`를 이용함으로써, 표면상으로는 불변인 `List`를 갖고 있다. 하지만 내부 가변성 접근을 활용해 원할때 데이터를 변경시킬 수 있는 `RefCell<T>`내의 메소드를 사용할 수 있다. 빌림 규칙의  런타임 검사는 데이터 레이스로부터 프로그래머를 지켜주지만, 데이터 구조의 이러한 유연성을 위해서 약간의 속도를 트레이드오프하는 것이 때때로 가치있을 수 있다.

표준 라이브러리는 내부 가변성을 제공하는 다른 타입도 가지고 있는데, 이를 테면 `Cell<T>`는 내부 값의 참조자를 주는 대신 값이 복사되어 `Cell<T>` 밖으로 나오는 점만 제외하면 비슷하다. 또한 `Mutex<T>`가 있는데, 이는 스레드들을 건너가며 사용해도 안전한 내부 가변성을 제공한다.

---

## 순환 참조는 메모리 릭을 발생시킬 수 있다.

러스트의 메모리 안정성 보장은 (*메모리  릭(memory leak)* 이라고도 알려져 있는) 뜻하지 않게 해제되지 않는 메모리를 생성하기 힘들게 한다. 하지만 그게 불가능한 것은 아니다. 메모리 릭을 완전히 방지하는 것은 컴파일 타임에 데이터 레이스를 허용하지 않는 것과 마찬가지로 러스트가 보장하는 것들 중 하나가 아니다. 메모리 릭도 러스트에서는 메모리 안정성에 포함됨을 의미한다. 러스트가 `Rc<T>` 및 `RefCell<T>`를 사용하면 메모리 릭이 발생할 수 있다. 즉 서로를 순환 참조하는 참조자를 만드는 것이 가능하다. 이는 메모리 릭을 발생시키는데, 그 이유는 순환 고리 안의 각 아이템들의 참조 카운트는 결코 0이 되지 않을 것이고, 그 값들은 버려지지 않을 것이다.

### 순환 참조 만들기

다음 코드의 `Cons`의 두 번째 요소는 `RefCell<Rc<List>>`인데, 이는 `Cons` variant가 가리키고 있는 `List`값을 변경하길 원한다는 의미다. 또한, `Cons` variant를 갖고 있다면 두번째 아이템에 접근하기 편하도록 `tail` 메소드도 가지고 있다.

```rust
use std::rc::Rc;
use std::cell::RefCell;
use List::{Cons, Nil};

#[derive(Debug)]
enum List {
    Cons(i32, RefCell<Rc<List>>),
    Nil,
}

impl List {
    fn tail(&self) -> Option<&RefCell<Rc<List>>> {
        match *self {
            Cons(_, ref item) => Some(item),
            Nil => None,
        }
    }
}
```

---

```rust
fn main() {
    let a = Rc::new(Cons(5, RefCell::new(Rc::new(Nil))));

    println!("a initial rc count = {}", Rc::strong_count(&a));
    println!("a next item = {:?}", a.tail());

    let b = Rc::new(Cons(10, RefCell::new(Rc::clone(&a))));

    println!("a rc count after b creation = {}", Rc::strong_count(&a));
    println!("b initial rc count = {}", Rc::strong_count(&b));
    println!("b next item = {:?}", b.tail());

    if let Some(link) = a.tail() {
        *link.borrow_mut() = Rc::clone(&b);
    }

    println!("b rc count after changing a = {}", Rc::strong_count(&b));
    println!("a rc count after changing a = {}", Rc::strong_count(&a));

    // Uncomment the next line to see that we have a cycle;
    // it will overflow the stack
    // println!("a next item = {:?}", a.tail());
}
```

위 코드에서는 `a`에 리스트를 만들고 `b`에는 `a`의 리스트를 가리키고 있는 리스트를 만들었다. 그 다음 `a`의 리스트가 b를 가리키도록 수정하는데, 이것이 순환 참조를 생성한다. 

초기값 리스트 `5`, `Nil`를 가진 `List` 값을 갖는 `Rc<List>` 인스턴스를 만들어 `a` 변수에 넣었다. 그런 다음 값 `10`과 `a`의 리스트를 가리키는 또다른 `List` 값을 갖는 `Rc<List>` 인스턴스를 `b` 변수에 넣었다.

그리고 `a`를 수정해 이것이 Nil 대신 b를 가리키도록 했다. `a` 내의 `RefCell<Rc<List>>`에 대한 참조자를 얻어오기 위해 `tail` 메소드를 사용했고, 이 참조자를 `link` 변수에 집어 넣었다. 그런 다음 `RefCell<Rc<List>>`의 `borrow_mut` 메소드를 사용해 Nil 값을 가지고 있는 `Rc<List>` 내부의 값을 `b`의 `Rc<List>` 로 바꾸었다.

`main`이 끝나는 지점에서 러스트는 `b`를 먼저 버리는(drop) 시도를 할 것인데, 이는 `a`와 `b`의 각각의 `Rc<List>` 인스턴스 내의 카운트를 `1`로 줄일 것이다. 하지만 `a`가 여전히 `b` 내의 있는 `Rc<List>`를 참조하는 상태기 때문에, 이 `Rc<List>`는 `0`이 아니라 `1`의 카운트를 갖게 되고, 따라서 `Rc<List>`가 힙에 가지고 있는 메모리는 버려지지 않을 것이다. 그 메모리는 참조 카운트 `1`을 가진 채로 영원히 그 자리에 그냥 있을 것이다. 

![a와 b가 서로를 가리키고 있는 순환 참조](Rust%200d7ead296adb4593aa99b2b8cb2e5307/Untitled%202.png)

a와 b가 서로를 가리키고 있는 순환 참조

---

만약 마지막 `println!`의 주석을 해제하고 프로그램을 실행해보면 스택 오버플로우가 날 것이다. 이렇게 순환 참조를 만든 직후, 프로그램은 종료된다. 만약 좀더 복잡한 프로그램이 많은 메모리를 순환 형태로할당했고 오랫동안 이를 유지했다면, 프로그램은 필요한 것보다 더 많은 메모리를 사용하게 되고, 사용 가능한 메모리를 동나게 해 시스템을 멈추게 했을지도 모른다.

순환 참조를 만드는 것이 쉬운 일은 아니지만, 불가능한 것도 아니다. 만일 `Rc<T>` 값을 가지고 있는 `RefCell<T>` 혹은 내부 가변성 및 참조 카운팅 기능이 있는 타입들로 유사한 조합을 사용한다면, 프로그래머는 순환을 만들지 않음을 보장해야 한다. 이 순환을 찾아내는 것을 러스트에 의지할 수는 없다. 순환 참조를 만드는 것은 프로그래머가 자동화된 테스트, 코드 리뷰, 그 외 소프트웨어 개발 연습 등을 이용해 최소화해야 할 프로그램 내의 논리적 버그다.

순환 참조를 피하는 또다른 해결책은 이러한 데이터 구조를 재구성하여 어떤 참조자는 소유권을 갖고 어떤 참조자는 그렇지 않도록 하는 것이다. 이렇게 하면 몇 개의 소유권 관계와 몇 개의 소유권 없는 관계로 이루어진 순환을 가질 수 있으며, 소유권 관계들만이 값을 버릴지 말지에 관해 영향을 주게 된다. 여기서는 `Cons` variant가 언제나 리스트를 소유하기를 원하기 때문에, 데이터 구조를 재구성하는 것은 불가능하다. 이번에는 언제 소유권 없는 관계가 순환 참조를 방지하는 적절한 방법이 될지 알기 위해서 부모 노드와 자식 노드로 구성된 그래프를 이용하는 예제를 살펴보자.

### 참조 순환 방지하기: Rc<T>를 Week<T>로 바꾸기

여태 `Rc::clone`을 호출하는 것이 `Rc<T>` 인스턴스의 `strong_count`를 증가시키고, `Rc<T>` 인스턴스는 `strong_count`가 `0`이 된 경우에만 제거된다. `Rc::downgrade`를 호출하고 여기에 `Rc<T>`에 대한 참조자를 넘기면 `Rc<T>` 인스턴스 내의 값을 가리키는 약한 참조(weak reference) 를 만들 수 있다. `Rc::downgrade`를 호출하면 `Week<T>` 타입의 스마트 포인터를 얻게 된다. `Rc::downgrade`는 `Rc<T>` 인스턴스의 `strong_count`를 `1` 증가시키는 대신 `week_count`를 `1` 증가시킨다. `Rc<T>` 타입은 몇 개의 `Week<T>` 참조가 있는지 추적하기 위해 `strong_count`와 유사한 방식으로 `weak_count`를 사용한다. 차이점은 `Rc<T>` 인스턴스가 제거되기 위해서 `weak_count`가 `0`일 필요는 없다는 것이다.

강한 참조는 `Rc<T>` 인스턴스의 소유권을 공유할 수 있는 방법이다. 약한 참조는 소유권 관계를 표현하지 않는다. 이러한 점은 순환 참조를 야기하지않는데, 그 이유는 몇몇의 약한 참조를 포함하는 순환이라도 강한 참조의 카운트가 `0`이되고 나면 깨지게 될 것이기 때문이다.

`Weak<T>`가 참조하고 있는 값은 이미 버려졌을지도 모르기 때문에, 가리키고 있는 값을 가지고 어떤 일을 하기 위해서는 그 값이 여전히 존재하는지를 반드시 확인해야 한다. 이를 이해 `Weak<T>`의 `upgrade` 메소드를 호출하는데, 이 메소드는 `Option<Rc<T>>`를 반환할 것이다. 만약 `Rc<T>` 값이 아직 버려지지 않았다면 `Some` 결과를 얻게 될 것이고 버려졌다면 `None` 값을 얻게 될 것이다. `upgrade`가 `Option<T>`를 반환하기 때문에, 러스트는 `Some`과 `None`의 경우가 반드시 처리되도록 할 것이고, 따라서 유효하지 않은 포인터는 없을 것이다.

이 예제로 어떤 아이템이 오직 다음 아이템에 대해서만 알고 있는 리스트보다, 자식 아이템 그리고 부모 아이템에 대해 모두 알고 있는 아이템을 갖는 트리를 만들어 보자.

### 트리 데이터 구조 만들기: 자식 노드를 가진 Node

자신의 자식 노드에 대해 알고 있는 노드를 갖는 트리를 만드는 것으로 시작해 보자. `i32` 값은 물론 자식 `Node`들의 참조자들 또한 가지고 있는 `Node`라는 이름의 구조체를 만들 것이다.

`Node`가 자신의 자식들을 소유하고, 이 소유권을 공유해 트리의 각 `Node`에 직접 접근할 수 있도록 하기를 원한다. 이를 위해 `Vec<T>` 아이템이 `Rc<Node>` 타입의 값이 되도록 정의했다. 또한 어떤 노드가 다른 노드의 자식이 되도록 원하기 때문에 `Vec<Rc<Node>>`를 `RefCell<T>`로 감싸주었다.

```rust
use std::rc::Rc;
use std::cell::RefCell;

#[derive(Debug)]
struct Node {
    value: i32,
    children: RefCell<Vec<Rc<Node>>>,
}
```

---

`leaf` 내의 `Rc<Node>`를 클론해 이를 `branch` 내에 저장했다. 이는 `leaf` 내의 `Node`가 이제 두 소유권자를 가지게 되었다는 것을 의미한다. 이제 `branch.children`을 통해 `branch`에서부터 `leaf`까지 접근할 수 있게 되었지만, `leaf`에서부터 `branch`로 접근할 방법은 없다. 그 이유는 `leaf`가 `branch`에 대한 참조자를 가지고 있지 않아서 이들간의 연관성을 알지 못하기 때문이다.

이제 `leaf`로 하여금 `branch`가 그의 부모임을 알도록 하길 원한다. 이것을 해보자.

```rust
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        children: RefCell::new(vec![]),
    });

    let branch = Rc::new(Node {
        value: 5,
        children: RefCell::new(vec![Rc::clone(&leaf)]),
    });
}
```

---

### 자식으로부터 부모로 가는 참조자 추가하기

자식 노드가 그의 부모를 알도록 만들기 위해, `parent` 필드를 `Node` 구조체 정의에 추가할 필요가 있다. 문제는 `parent`는 무슨 타입이 되어야 하는지를 결정하는 중에 발생한다. 이것이 `Rc<T>`가 되어버리면, `branch`를 가리키고 있는 `leaf.parent`와 `leaf`를 가리키고 있는 `branch.children`을 가지고 있는 순환참조를 만들게 되며, 이것들의 `strong_count` 값을 `0`으로 만드는 일이 없어지게 된다.

이 관계들을 생각해보면, 부모 노드는 그의 자식들을 소유하는 것이 타당하다. 만일 부모 노드가 버려지게 되면, 그의 자식 노드들도 또한 버려져야 한다. 하지만 자식은 그의 부모를 소유해서는 안된다. 만일 자식 노드가 버려져도, 그 부모는 여전히 존재해야 한다. 바로 이것을 위해 약한 참조를 사용할 수 있다.

따라서 `Rc<T>` 대신 `RefCell<Week<Node>>`를 이용해 `parent`의 타입을 만들자. 노드는 그의 부모 노드를 참조할 수 있게 되었지만 그 부모를 소유하지는 않는다. 

```rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

#[derive(Debug)]
struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>,
}
```

---

`leaf` 는 부모없이 시작되어서, 비어있는 `Weak<Node>` 참조자 인스턴스를 생성했다. 이 시점에서, `upgrade` 메소드를 사용해 `leaf`의 부모에 대한 참조자를 얻는 시도를 하면 `None` 값을 얻는다. 첫 번째 `println!` 구문에서는 다음과 같은 출력을 보게 된다.

```
leaf parent = None
```

이제 `leaf`에게 그의 부모 `branch`에 대한 `Weak<Node>` 참조자를 가지도록 수정한다. `leaf`의 `parent` 필드 내의 `RefCell<Weak<Node>>` 상의 `borrow_mut` 메소드를 사용하고, 그런 다음 `Rc::downgrade` 함수를 이용해 `branch`의 `Rc<Node>`로부터 `branch`에 대한 `Weak<Node>` 참조자를 생성했다.

`leaf`의 부모에 대해 접근할 때 이번에는 `branch`를 가지고 있는 `Some` variant를 얻게될 것이다. 이제는 그의 부모에 접근할 수 있다. `leaf`를 출력할 때, 스택 오버플로우가 발생하는 순환을 피하게 되었다. `Weak<Node>` 참조자는 `(Weak)`로 출력된다.

```rust
leaf parent = Some(Node { value: 5, parent: RefCell { value: (Weak) },
children: RefCell { value: [Node { value: 3, parent: RefCell { value: (Weak) },
children: RefCell { value: [] } }] } })
```

```rust
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![]),
    });

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());

    let branch = Rc::new(Node {
        value: 5,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![Rc::clone(&leaf)]),
    });

    *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
}
```

---

### strong_count와 weak_count의 변화 시각화

새로운 내부 스코프를 만들고 `branch`를 이 스코프에서 생성해보자. 이제 `Rc<Node>` 인스턴스의 `strong_count`와 `weak_count` 값이 어떻게 변하는지 살펴보자.

```rust
fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![]),
    });

    println!(
        "leaf strong = {}, weak = {}",
        Rc::strong_count(&leaf),
        Rc::weak_count(&leaf),
    );

    {
        let branch = Rc::new(Node {
            value: 5,
            parent: RefCell::new(Weak::new()),
            children: RefCell::new(vec![Rc::clone(&leaf)]),
        });

        *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

        println!(
            "branch strong = {}, weak = {}",
            Rc::strong_count(&branch),
            Rc::weak_count(&branch),
        );

        println!(
            "leaf strong = {}, weak = {}",
            Rc::strong_count(&leaf),
            Rc::weak_count(&leaf),
        );
    }

    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
    println!(
        "leaf strong = {}, weak = {}",
        Rc::strong_count(&leaf),
        Rc::weak_count(&leaf),
    );
}
```

`branch`를 생성한 스코프가 끝나면 `branch`의 `Rc<Node>`의 강한 참조 카운트가 `0`으로 줄어들게 되고, 결국 버려지게 된다. 이때 `leaf.parent`는 아직 `branch`의 약한 참조를 가지고 있지만 이것이 `branch`가 사라지는것을 막지 못하고 아무런 메모리 릭도 발생하지 않는다. 이렇게 `branch`가 버려지고 이후에 `upgrade` 메서드를 통해 `leaf`의 부모에 접근하려한다면, `None`을 얻게 될 것이다. 

참조 카운트들과 버리는 값들을 관리하는 모든 로직은 `Rc<T>`와 `Weak<T>`, 그리고 이들의 `Drop` 트레잇에 대한 구현부에 만들어져 있다. 자식으로부터 부모로의 관계가 `Node`의 정의 내에서 `Weak<T>` 참조자로 정의함으로서, 순환 참조와 메모리 릭을 만들지 않고도 자식 노드를 가리키는 부모노드, 혹은 그 반대의 것을 가지게 될 수 있다.

---

## 정리

이번엔 러스트가 일반적인 참조자를 가지고 기본적으로 보장하는 것들과는 다른 보장 및 트레이드 오프를 만들어내기 위해 스마트 포인러를 사용하는 방법을 다루었다. `Box<T>`타입은 알려진 크기를 갖고 있고 힙에 할당된 데이터를 가리킨다. `Rc<T>` 타입은 힙에 있는 데이터에 대한 참조자의 개수를 추적하여 그 데이터가 여러개의 소유자들을 가질 수 있도록 한다. 내부 가변성을 갖춘 `RefCell<T>` 타입은 불변 타입을 원하지만 그 타입의 내부 값을 변경하기를 원할 때 사용할 수 있는 타입을 제공한다. 그리고 이는 빌림 규칙을 컴파일 타임 대신 런타임에 따르도록 한다.

또한 `Deref` 및 `Drop` 트레잇을 다루었는데, 이는 스마트 포인터의 수많은 기능을 활성화해준다. 여기서 메모리 릭을 발생시킬 수 있는 순환 참조에 대한 것과 `Weak<T>`를 이용해 이들을 방지하는 방법도 탐구했다.

---

# 겁없는 동시성

안전하고 효율적으로 동시성 프로그래밍을 다루는 것은 러스트의 또 다른 주요 목표들 중 하나다. *동시성 프로그래밍(concurrent programming)*, 즉 프로그램의 서로 다른 부분이 독립적으로 실행되는 것과, *병렬 프로그래밍(parallel programing)*, 즉 프로그램의 서로 다른 부분이 동시에 실행되는 것은 더 많은 컴퓨터들이 여러 개의 프로세서로 이점을 얻음에 따라 그 중요성이 증가하고 있다. 역사적으로 이러한 맥락에서 프로그래밍하는 것은 어렵고 에러를 내기쉬웠다. 러스트는 이를 바꾸기를 바란다.

초기에 러스트 팀은 메모리 안전을 보장하는 것과 동시성 문제를 방지하는 것은 다른 방법으로 해결되야 하는 별개의 과제라 생각했다. 시간이 흘러 러스트 팀은 소유권과 타입 시스템이 메모리 안정성 및 동시성 문제를 관리하는 것을 돕기 위한 강력한 도구들의 집합이라는 사실을 깨달았다. 소유권과 타입 검사를 지렛대 삼아서 많은 동시성 에러들이 러스트 내에서 런타임 에러가 아닌 컴파일 타임 에러가 되었다. 그러므로, 런타임 동시성 버그가 발생하는 정확한 환경을 재현하는 시도를 하는데 시간을 낭비하도록 하지 않고, 부정확한 코드가 컴파일이 되지 않고 문제점을 설명하는 에러를 살펴보자. 결과적으로 프로덕션에 배포된 이후가 아니라 작업을 하는 동안에 코드를 고칠 수 있을 것이다. 러스트의 이러한 측면을 *겁없는 동시성 (fearless concurrency)*라 별명지었다. 겁없는 동시성은 우리가 감지하기 힘든 버그가 없고 새로운 버그 생성 없이 리팩토링하기 쉬운 코드를 작성하도록 해준다.

> 노트: 단순함을 목적으로 많은 수의 문제들을 더 정교하게 동시성 및 병렬성 이라고 말하기 보다는 그냥 동시성에 대한 문제로서 참고할 것이다. 이번 장에서 동시성이라고 말할 때마다 동시성 및 병렬성을 대입해 생각하자.
> 

많은 언어들은 동시성 문제를 다루기 위해 제공하는 해결책은 독단적이다. 예를 들어, Erlang은 메세지-패싱 (message-passing) 동시성을 위한 우아한 기능을 가지고 있지만 스레드 간에 상태를 공유하기 위한 이해하기 힘든 방법을 가지고 있다. 가능한 해결책 중 일부만을 제공하는 것은 고수준의 언어를 위한 타당한 전략인데, 이는 고수준의 언어가 추상화를 얻기 위해 몇몇의 제어권을 포기함으로써 얻는 이득을 약속하기 때문이다. 하지만 저수준의 언어는 어떠한 주어진 상황 내에서 최고의 성능을 갖는 해결책을 제공하도록 기대받고 있고 하드웨어에 대해서 더 적은 추상화를 갖는다. 그러므로, 러스트는 우리의 상황과 요구사항에 적합한 방법이 무엇이든 간에 문제를 모델링하기 위한 다양한 도구들을 제시한다.

- 여러 조각의 코드를 동시에 실행시키기 위해 스레드를 생성하는 방법
- 채널들이 스레드 간에 메세지를 보내는 메세지-패싱 동시성
- 여러 스레드가 어떤 동일한 데이터를 접근할 수 있는 상태-공유 (shared-state) 동시성
- 표준 라이브러리가 제공하는 타입 뿐만 아니라 러스트의 동시성 보장을 사용자 정의 타입으로 확장하는 `Sync`와 `Send`트레잇

---

## 스레드를 이용하여 코드를 동시에 실행하기

대부분의 운영 체제에서, 실행되는 프로그램의 코드는 *프로세스* 내에서 실행되고, 운영 체제는 한번에 여러 개의 프로세스들을 관리한다. 우리의 프로그램 내에서도 동시에 실행되는 독립적인 부분들을 가질 수 있다. 이러한 독립적인 부분들을 실행하는 기능을 *스레드*라고 부른다. 

프로그램 내에 계산 부분을 여러개의 스레드로 쪼개는 것은 프로그램이 동시에 여러개의 일을 할 수 있기 때문에 성능을 향상시킬 수 있지만, 프로그램을 복잡하게 만들기도 한다. 스레드가 동시에 실행될 수 있기 때문에, 다른 스레드 상에서 실행될 우리의 코드 조각드의 실행 순서에 대한 내재적인 보장이 없다. 이는 다음과 같은 문제들을 야기할 수 있다.

- 여러 스레드들이 일관성 없는 순서로 데이터 혹은 리소스에 접근하게 되는, 경쟁 조건 (race condition)
- 두 스레드가 서로 상대방 스레드가 가지고 있는 리소스의 사용을 끝내길 기다려서 양쪽 스레드 모두 계속 실행되는 것을 막아버리는, 데드록 (deadlock)
- 특정한 상황에서만 발생되어 재현하기와 안정적으로 수정하기가 힘든 버그들

러스트는 스레드 사용의 부정적인 효과를 줄이려고 하지만, 다중 스레드 콘텍스트 내에서의 프로그래밍은 여전히 신중하게 생각해야 하고 단일 스레드 내에서 실행되는 프로그램의 것과는 다른 코드 구조가 필요하다.

프로그래밍 언어들은 몇가지 다른 방식으로 스레드를 구현한다. 많은 운영 체제들이 새로운 스레드를 만들기 위한 API를 제공한다. 언어가 운영 체제의 API를 호출하여 스레드를 만드는 이러한 구조는 때때로 *1:1*이라 불리는데, 이는 하나의 운영 체제 스레드가 하나의 언어 스레드에 대응된다는 의미다.

많은 프로그래밍 언어들은 그들만의 특별한 스레드 구현을 제공한다. 프로그래밍 언어가 제공하는 스레드는 *그린(green)* 스레드라고 알려져 있고, 이러한 그린 스레드를 사용하는 언어들은 다른 숫자의 운영체제 스레드로 구성된 콘텍스트 내에서 그린 스레드들을 실행할 것이다. 이러한 이유로 인해 그린 스레드 구조는 M:N이라고 불린다. `M`개의 그린 스레드가 `N`개의 시스템 스레드에 대응되는데, 여기서 M과 N은 굳이 동일한 숫자가 아니어도 된다.

> 추가설명: 그린 스레드는 어플리케이션 레벨에서 관리되며, 커널레벨 스레드와 1:1방식 n:1방식 혼합방식 등으로 연결된다. 커널 레벨 스레드는 운영체제에서 직접 관리되는 스레드를 의미한다. 실제 일의 주체이며 만약 유저레벨 스레드가 커널레벨 스레드와 1:1로 연결되어있고 CPU코어가 8개면 한 시점에 8개의 코어가 내 프로그래밍 언어의 스레드를 위해 일할 수 있다.
> 
> 
> 커널 레벨 스레드가 여러개면 마냥 성능이 잘 나오고 좋을것만 같지만 커널단의 여러가지 동기화과정이 필요하므로 무겁다는 단점도 있다.
> 
> 예를 들어, CPU코어가 2개밖에 없는 환경이라면 유저 레벨 스레드 100개와 커널 레벨 스레드100개를 만들었을 때, 유저레벨 스레드가 100개가 커널레벨 스레드1개에 매핑 되더라도 인터럽트와 컨텍스트 스위칭이 잘됐다면 동시에 CPU를 하나밖에 사용하지 못하더라도 CPU 사용량도 충분히 나오면서 무거운 커널레벨 스레드 100개보다 성능면에서 떨어지지 않을 수도 있다.
> 

각각의 구조는 고유한 장점과 트레이드 오프를 가고 있으며, 러스트에 있어 가장 중요한 트레드 오프는 런타임 지원이다. 런타임은 혼동하기 쉬운 용어이고 맥락에 따라 다른 의미를 가질 수 있다.

이 글의 맥락에서 *런타임*이라 하는 것은 언어에 의해 모든 바이너리 내에 포함되는 코드를 의미한다. 이 코드는 언어에 따라 크거나 작을 수 있지만, 모든 어셈블리기 아닌 언어들은 어느 정도 크기의 런타임 코드를 가지게 될 것이다. 이러한 이유로, 흔히 사람들이 “런타임이 없다”라고 말할 때는, 종종 “런타임이 작다”는 것을 의미하는 것이다. 런타임이 작을 수록 더 적은 기능을 갖지만 더 작아진 바이너리로 인해 얻어지는 장점을 갖는데, 이는 더 큰 콘텍스트 내에서 다른 언어들과 조합하기 쉬워진다는 점이다. 비록 많은 언어들이 더 많은 기능을 위하여 런타임 크기를 늘리는 거래를 수락하더라도, 러스트는 거의 런타임이 없을 필요가 있고 성능을 관리하기 위해 C를 호출하는 것에 대해 타협할 수 없다.

그린 스레드 M:N 구조는 스레드들을 관리하기 위해 더 큰 언어 런타임이 필요하다. 그런 이유로 러스트 표준 라이브러리는 오직  1:1 스레드 구현을 제공한다. 러스트가 이러한 저수준의 언어이기 때문에 그린스레드를 원한다면, 예를 들면 어떤 스레드를 언제 실행시킬지에 대한 더 많은 제어권과 콘텍스트 교환(context switching)의 더 저렴한 비용 같은 관점을 위해 오버해드와 맞바꾸겠다면 M:N 스레드를 구현한 크레이트도 있다.

이제 러스트에서의 스레드를 정의했으니, 표준 라이브러리가 제공하는 스레드 관련 API를 어떻게 사용하는지를 탐구해보자.

---

### spawn으로 새로운 스레드 생성하기

새로운 스레드를 생성하기 위해서는 `thread::spawn` 함수를 호출하고 여기에 새로운 스레드 내에서 실행하고 싶은 코드가 담겨있는 클로저를 넘긴다.

다음은 메인 스레드에서 어떤 텍스트를 출력하고 새로운 스레드에서는 다른 텍스트를 출력한다.

```rust
use std::thread;
use std::time::Duration;

fn main() {
    thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }
}
```

새로운 스레드는 실행이 종료되었든 혹은 그렇지 않든 메인 스레드가 종료될 때 멈추게 될 것임을 주의하자. 이 프로그램의 출력은 매번 약간씩은 다를지 모르겠지만, 다음과 비슷할 것이다.

```
hi number 1 from the main thread!
hi number 1 from the spawned thread!
hi number 2 from the main thread!
hi number 2 from the spawned thread!
hi number 3 from the main thread!
hi number 3 from the spawned thread!
hi number 4 from the main thread!
hi number 4 from the spawned thread!
hi number 5 from the spawned thread!
```

`thread::sleep`의 호출은 강제로 스레드가 잠깐 동안 실행을 멈추게 하는데, 다른 스레드가 실행되는 것을 허용한다. 스레드들은 아마도 교대로 실행될 것이지만, 이는 보장되지 않는다. 각 운영체제가 어떻게 스레드를 스케줄링 하는지에 따라 달린 문제이기 때문이다. 위에 실행 예에서는 생성된 스레드로부터의 출력 구문이 코드의 첫번째에 나타나 있음에도 불구하고 메인 스레드가 먼저 출력되었다. 그리고 생성된 스레드에게 `i`가 `9`일때까지 출력하라고 했음에도 메인 스레드가 멈추기 전까지인 고작 `5`까지 밖에 도달하지 못했다. 

---

### join 핸들을 사용해 모든 스레드들이 끝날때까지 기다리기

위 코드와 출력결과에서 알 수 있는 내용은 메인 스레드가 종료된다면, 추가적으로 생성된 스레드가 조기에 멈출 뿐만 아니라, 생성된 스레드가 모든 코드를 실행할 것임을 보장해 줄 수도 없다. 그 이유는 스레드들이 실행되는 순서에 대한 보장이 없기 때문이다.

생성된 스레드가 실행되지 않거나, 전부 실행되지 않는 문제는 `thread::spwan`의 반환값을 변수에 저장함으로서 해결할 수 있다. `thread::spawn`의 반환 타입은 `JoinHandle`이다. `JoinHandle`값에 `join` 메소드를 호출 했을 때 그 스레드가 끝날때까지 기다릴 수 있다. 다음 예제는 앞서 만든 예제에서 `JoinHandle`을 사용하고 `join`을 호출하여 `main`이 끝나기 전에 생성된 스레드가 종료되는 것을 확실하게 하는지를 확인할 수 있다.

```rust
use std::thread;
use std::time::Duration;

fn main() {
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }

    handle.join().unwrap();
}
```

핸들에 대해 `join`을 호출하면 해당 핸들에 대한 스레드가 종료될 때까지 현재 실행중인 스레드를 블록한다. 스레드를 *블록 (Block)* 한다는 것은 그 스레드의 작업을 수행하거나 종료되는 것을 방지한다는 의미다. 메인 스레드의 `for` 루프 이후에 `join`을 호출했기때문에 실행결과는 다음과 비슷할 것이다.

```
hi number 1 from the main thread!
hi number 2 from the main thread!
hi number 1 from the spawned thread!
hi number 3 from the main thread!
hi number 2 from the spawned thread!
hi number 4 from the main thread!
hi number 3 from the spawned thread!
hi number 4 from the spawned thread!
hi number 5 from the spawned thread!
hi number 6 from the spawned thread!
hi number 7 from the spawned thread!
hi number 8 from the spawned thread!
hi number 9 from the spawned thread!
```

두 스레드가 계속 교차하겠지만, `handle.join()`의 호출로 인해 메인 스레드는 기다리고 생성된 스레드가 종료되기 전까지 끝나지 않을 것이다.  그런데 만약 다음과 같이 `main`의 `for` 루프 이전으로 `handle.join()`을 이동시키면 어떤일이 발생할까?

```rust
use std::thread;
use std::time::Duration;

fn main() {
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    handle.join().unwrap();

    for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }
}
```

메인 스레드는 생성된 스레드가 종료될 때까지 기다릴 것이고 그 다음 자신의 `for` 루프를 실행시키게 되어, 아래처럼 출력값이 더 이상 교차되지 않을 것이다.

```
hi number 1 from the spawned thread!
hi number 2 from the spawned thread!
hi number 3 from the spawned thread!
hi number 4 from the spawned thread!
hi number 5 from the spawned thread!
hi number 6 from the spawned thread!
hi number 7 from the spawned thread!
hi number 8 from the spawned thread!
hi number 9 from the spawned thread!
hi number 1 from the main thread!
hi number 2 from the main thread!
hi number 3 from the main thread!
hi number 4 from the main thread!
```

`join`이 호출되는 위치와 같은 작은 디테일들이 스레드들이 동시에 실행되는지, 혹은 아닌지에 대해 영향을 미칠 수 있다.

---

### 스레드에 move 클로저 사용하기

`move` 클로저는 `thread::spawn`과 함께 자주 사용되는데, 그 이유는 어떤 스레드의 데이터를 다른 스레드 내에서 사용하도록 해주기 때문이다.

앞서 클로저에 대해서 배울때, 클로저의 파라미터 목록 앞에 `move` 키워드를 이용해 클로저가 그 환경에서 사용하는 값의 소유권을 강제로 갖게 한다고 언급했다. 이 기술은 값의 소유권을 한 스레드에서 다른 스레드로 이전하기 위해 새로운 스레드를 생성할 때 특히 유용하다.

지금까지 예제에서는 `thread::spawn`에 넘기는 클로저는 아무런 인자도 갖기 않았다. 생성된 스레드의 코드 내에서는 메인 스레드로부터의 어떠한 데이터도 사용하고 있지 않다. 메인 스레드로부터의 데이터를 생성된 스레드 내에서 사용하기 위해서는 생성된 스레드의 클로저가 필요로 하는 값을 캡처해야한다. 다음 코드는 메인 스레드에서 백터를 생성하고 이를 새로운 스레드 내에서 사용하는 예제다. 

```rust
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(|| {
        println!("Here's a vector: {:?}", v);
    });

    handle.join().unwrap();
}
```

이 코드는 컴파일되지 않는다. 클로저는 `v`를 사용하므로, `v`는 캡처되어 클로저의 환경의 일부가 된다. `thread::spawn`이 이 클로저를 새로운 스레드 내에서 실행하므로, `v`는 새로운 스레드 내에서 접근이 가능해야한다.

```
error[E0373]: closure may outlive the current function, but it borrows `v`,
which is owned by the current function
 --> src/main.rs:6:32
  |
6 |     let handle = thread::spawn(|| {
  |                                ^^ may outlive borrowed value `v`
7 |         println!("Here's a vector: {:?}", v);
  |                                           - `v` is borrowed here
  |
help: to force the closure to take ownership of `v` (and any other referenced
variables), use the `move` keyword
  |
6 |     let handle = thread::spawn(move || {
  |                                ^^^^^^^
```

러스트는 `v`가 어떻게 캡처하는지 추론하고, `println!`이 `v`의 참조자만을 필요로 하기 때문에, 클로저는 `v`를 빌리길 시도한다. 그러나 여기서 러스트는 생성된 스레드가 얼마나 오랫동안 실행될지 보장할 수 없고, 이는 v에 대한 참조자가 항상 유효한 것인지를 알 수 없게 된다. 다음 예제는 이러한 상황을 잘 표현하는 시나리오다.

```rust
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(|| {
        println!("Here's a vector: {:?}", v);
    });

    drop(v); // oh no!

    handle.join().unwrap();
}
```

만약 이 코드를 실행할 수 있다면, 생성된 스레드가 전혀 실행되지 않고 즉시 백그라운드에 들어갈 가능성이 있다. 생성된 스레드는 내부에 `v`에 대한 참조자를 가지고 있지만, 메인 스레드는 `drop` 함수를 사용해 `v`를 즉시 드롭시킨다. 그러면 생성된 스레드가 실행되기 시작할 때 `v`가 더 이상 유효하지 않게 되며, 참조자 또한 유효하지 않게 된다.

```
help: to force the closure to take ownership of `v` (and any other referenced
variables), use the `move` keyword
  |
6 |     let handle = thread::spawn(move || {
  |                                ^^^^^^^
```

`move` 키워드를 클로저 앞에 추가함으로서, 러스트가 값을 빌려와야겠다! 라고 추론하는것이 아니라 사용하는 값의 소유권을 강제로 가지도록 할 수 있다. `move`키워드를 사용한 다음 코드는 의도한대로 실행이 된다.

```rust
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(move || {
        println!("Here's a vector: {:?}", v);
    });

    handle.join().unwrap();
}
```

---

러스트의 소유권 규칙이 이를 만들었다. [처음 예제](https://www.notion.so/61bfa4569eb547cd907f4189c8ca5004?pvs=21)에서 에러를 받은 이유는 러스트가 보수적이고 스레드를 위해 단지 `v`를 빌리려고만 했기 때문이었다. 이는 메인 스레드가 이론적으로 생성된 스레드의 참조자를 무효화할 수 있음을 의미한다. 러스트에게 `v`의 소유권을 생성된 스레드로 이동시키라 말해줌으로서, 러스트에게 메인 스레드가 `v`를 더 이상 이용하지 않음을 보장하고 있다. 

---

## 메세지 패싱을 사용해 스레드 간에 데이터 전송하기

안전한 동시성을 보장하는 인기 있는 접근법 중 하나는 *메세지 패싱 (message passing)*인데, 이는 스레드들 혹은 엑터들이 데이터를 담고 있는 메세지를 서로 주고받는 것이다. 러스트가 메세지 보내기 방식의 동시성을 달성하기 위해 갖춘 한가지 주요 도구는 *채널 (channel)* 인데, 이는 러스트의 표준 라이브러리가 구현체를 제공하는 프로그래밍 개념이다. 프로그래밍에서의 채널은 개울이나 강 같은 물의 통로와 비슷하다고 생각할 수 있다. 만일 우리가 고무 오리나 배 같은 것을 개울에 띄우면, 물길의 끝까지 하류로 흘러내릴 것이다.

프로그래밍에서의 채널은 둘로 나뉘어져 있다. 송신자와, 수신자다. 송신자는 강에 고무 오리를 띄우는 상류 위치고, 수신자 측은 하류에 고무 오리가 도달하는 곳이다. 작성한 코드 중 한 곳에서 보내고자 하는 데이터와 함께 송신자의 메소드를 호출하면, 다른 곳에서는 도달한 메세지에 대한 수신 종료를 검사한다. 송신자 혹은 수신자가 드롭되면 채널이 *닫혔다 (closed)* 라고 말한다.

이번엔, 값을 생성해 채널로 내려보내는 한 스레드와 값을 받아서 이를 출력하는 또 다른 스레드를 가지고 있는 프로그램을 만들어보자. 이 기능을 설명하기 위해 채널을 사용해 스레드 간에 단순한 값들을 보낼 것이다. 만약 이 기술에 익숙해지면, 채팅 시스템이나 다수의 스레드가 계산의 일부분을 수행하고, 이에 대한 결과들을 종합하는 하나의 스레드에 보내는 시스템을 구현하기 위해 채널을 이용할 수도 있다.

다음 코드는 채널을 만들지만 이것을 가지고 특별한 일을 하지는 않는다. 이 채널을 통해 어떤 타입의 값을 보내는지 정하지 않았기 때문에, 아직 컴파일 되지 않는다.

```rust
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel();
}
```

이 코드에서 `mspc::channel` 함수를 사용해 새로운 채널을 생성한다. `mpsc`는 *복수 생성자, 단수 소비자 (multiple producer, single consumer)*를 나타낸다. 짧게 줄이면, 러스트의 표준 라이브러리가 채널을 구현한 방법은 한 채널이 값을 생성하는 복수개의 송신 단말을 가질 순 있지만, 이 값을 소비하는 단 하나의 수신 단말을 가질 수 있음을 의미한다. 하나의 큰 강으로 함께 흐르는 여러 개울들을 생각하면 좋다. 개울 중 어떤 쪽에라도 흘려보낸 모든 것은 강 끝에서 모일 것이다. 지금은 단일 생성자를 가지고 시작하겠지만, 이후에 여러 생성자를 추가할 것이다. 

`mspc::channel` 함수는 튜플을 반환하는데, 첫번째 요소는 송신 단말이고 두번째 요소는 수신 단말이다. `tx`와 `rx`라는 약어는 많은 분야에서 각각 *송신자 (transmitter)*와 *수신자 (receiver)*를 위해 사용하므로, 각각의 단말을 가리키기 위해 그렇게 변수명을 지었다. 이 튜플을 해체하는 패턴과 함께 `let` 구문을 사용했다. `let` 구문 내에서의 패턴의 사용과 해체에 대해서는 이후에 다룬다. 이러한 방식으로 `let` 구문을 사용하는 것은 `mpsc::channel`이 반환하는 튜플의 조각들을 추출하는데 편리한 접근법이다.

다음 코드는 송신 단말을 생성된 스레드로 이동시키고 하나의 스트링을 전송해 생성된 스레드가 메인 스레드와 통신하도록 한 것이다. 이는 각 상류에 고무 오리를 띄우는 것, 혹은 한 스레드에서 다른 스레드로 채팅을 보내는 것과 비슷하다.

```rust
use std::thread;
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();
    });
}
```

`thread::spawn`을 이용해 새로운 스레드를 생성한 뒤 `move`를 사용해 `tx`를 클로저로 이동시켰다. 이렇게 생성된 스레드가 `tx`를 소유한다. 생성된 스레드는 채널을 통해 메세지를 보낼 수 있도록 하기 위해 채널의 송신 단말을 소유할 필요가 있다. 

송신 단말(`tx`)는 보내고자 하는 값을 취하는 `send` 메소드를 가진다. `send` 메소드는 `Result<T, E>` 타입을 반환한다. 만일 수신 단말(`rx`)가 이미 드롭되어 있고 값을 보내는 곳이 없다면, 송신 연산은 에러를 반환할 것이다. 이 예제에서는 에러가 나는 경우 패닉을 일으키기 위해 `unwrap`을 호출한다. 그러나 실제 어플리케이션에서는 이를 적절히 처리해야 할 것이다. 

다음 코드는 메인 스레드에 있는 채널의 수신 단말로부터 값을 받을 것이다. 

```rust
use std::thread;
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();
    });

    let received = rx.recv().unwrap();
    println!("Got: {}", received);
}
```

채널의 수신 단말(`rx`)는 두 개의(`recv`와 `try_recv`) 유용한 메소드를 가지고 있다. *수신 (receive)*의 줄임말인 `recv`를 사용하는 중인데, 이는 메인 스레드의 실행을 *블록 (Block)* 시키고, 채널로부터 값이 보내질 때까지 기다릴 것이다. 값이 일단 전달되면, `recv`는 `Result<T, E>` 형태로 이를 반환할 것이다. 채널의 송신 단말이 닫히면, `recv`는 더 이상 어떤 값도 오지 않을 것이란 신호를 하는 에러를 반환한다.

`try_recv` 메소드는 블록하지 않는 대신 즉시 `Result<T, E>`를 반환한다. 즉 전달 받은 메세지가 있다면 이를 담고 있는 `Ok` 값을, 이 시점에서 메세지가 없다면 `Err` 값을 반환한다. `try_recv`를 사용하는 것은 메세지를 기다리는 동안 해야 하는 다른 작업이 있을 때 유용하다. 즉 `try_recv`를 매번 호출하면서, 가능한 메세지가 있으면 이를 처리하고, 그렇지 않으면  다음번 검사때까지 잠시동안 다른 일을 하는 코드를 작성할 수 있다.

이 예제에서는 단순함을 위해 `recv`를 이용했다. 이 메인 스레드에서는 메세지를 기다리는 동안 해야 할 다른 일이 없으므로, 메인 스레드를 블록시키는 것이 적절하다.

---

### 채널의 소유권 전달

소유권 규칙은 안전하고 동시적은 코드를 작성하는 것을 돕기 때문에 메세지 보내기 방식 내에서 강건한 역할을 한다. 동시성 프로그래밍 내에서 에러를 방지하는 것은 러스트 프로그램 전체에 걸친 소유권에 대해 생각해 볼 수 있는 장점이 있다. 어떤 식으로 채널과 소유권이 문제를 방지하기 위해 함께 동작하는지 보기 위한 실험을 해보자. 만약 채널로 `val` 값을 내려보낸 이후에 생성된 스레드에서 이 값을 사용하는 시도를 해보자. 다음 코드를 컴파일 해보자.

```rust
use std::thread;
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();
        println!("val is {}", val);
    });

    let received = rx.recv().unwrap();
    println!("Got: {}", received);
}
```

여기서 `tx.send`를 통해 채널에 `val`를 보낸뒤에 이를 출력하려 시도한다. 이 코드는 허용되어서는 안된다. 일단 값이 다른 스레드로 보내지고 나면, 이 값을 다시 사용해보기 전에 보낸 스레드에서 수정 되었거나, 이미 버려졌을 수 있다. 잠재적으로 다른 스레드에서의 수정은 불일치하거나 존재하지 않는 데이터로 인한 에러를 일으킬 수 있는 것이다. 그러므로 다음과 같은 에러를 얻는다.

```
error[E0382]: use of moved value: `val`
  --> src/main.rs:10:31
   |
9  |         tx.send(val).unwrap();
   |                 --- value moved here
10 |         println!("val is {}", val);
   |                               ^^^ value used here after move
   |
   = note: move occurs because `val` has type `std::string::String`, which does
not implement the `Copy` trait
```

동시성에 관한 실수가 컴파일 타임 에러를 야기했다. `send` 함수가 그 파라미터의 소유권을 가져가고, 이 값이 이동될 때, 수신자가 이에 대한 소유권을 얻는다. 이는 값을 보낸 이후에 우발적으로 이 값을 다시 사용하는 것을 방지한다. 

---

### 복수의 값들을 보내고 수신자가 기다리는지 보기

[앞선 예제](https://www.notion.so/3673c5c7e6324b3faacf9ac7937e0003?pvs=21)는 컴파일도 되고 실행도 되지만, 두 개의 분리된 스레드가 채널을 통해 서로 대화하는지를 명확히 보여주지 않는다. 다음 코드는 해당 예제가 동시에 실행된다는 것을 입증해주는 수정본이다. 이제 생성된 스레드가 여러 메세지를 보내면서 각 메세지 사이에 1초씩 멈출 것이다.

```rust
use std::thread;
use std::sync::mpsc;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let vals = vec![
            String::from("hi"),
            String::from("from"),
            String::from("the"),
            String::from("thread"),
        ];

        for val in vals {
            tx.send(val).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    for received in rx {
        println!("Got: {}", received);
    }
}
```

이번에 생성된 스레드는 메인 스레드로 보내고 싶어하는 스트링의 벡터를 가지고 있다. 스트링마다 반복하며 각각의 값을 개별적으로 보내고, `Duration` 값에 `1`을 넣어서 `thread::sleep` 함수를 호출하는 것으로 각각의 사이에 멈춘다.

메인 스레드에서는 더이상 `recv`함수를 명시적으로 호출하고 있지 않다. 대신 `rx`를 반복자처럼 다루고 있다. 각각의 수신된 값에 대해서 이를 출력하고, 만약 채널이 닫힌다면 반복문이 종료된다. 메인 스레드의 `for` 루프 내에는 어떠한 지연 코드를 넣지 않았으므로, 메인 스레드가 생성된 스레드로부터 값을 전달받는 것을 기다리는 것이라고 알 수 있다.

---

### 송신자를 복제하여 여러 생성자 만들기

이전에 `mpsc`가 *복수 생성자 단일 소비자 (multiple producer, single consumer)*의 약어라는 것을 언급했다. 이를 이용해 모두 동일한 수신자로 값들을 보내는 여러 스레드들을 만들어보자. 

```rust
// --snip--

let (tx, rx) = mpsc::channel();

let tx1 = mpsc::Sender::clone(&tx);
thread::spawn(move || {
    let vals = vec![
        String::from("hi"),
        String::from("from"),
        String::from("the"),
        String::from("thread"),
    ];

    for val in vals {
        tx1.send(val).unwrap();
        thread::sleep(Duration::from_secs(1));
    }
});

thread::spawn(move || {
    let vals = vec![
        String::from("more"),
        String::from("messages"),
        String::from("for"),
        String::from("you"),
    ];

    for val in vals {
        tx.send(val).unwrap();
        thread::sleep(Duration::from_secs(1));
    }
});

for received in rx {
    println!("Got: {}", received);
}

// --snip--
```

이번에는 첫번째 스레드를 생성하기 전에 채널의 송신 단말(`tx`)에 대해 `clone`을 호출했다. 이렇게 만들어진 두 개의 송신 단말들을 각각 서로 다른 생성된 스레드에 제공했다. 

이 코드를 실행시키면 다음과 출력과 비슷할 것이다.

```
Got: hi
Got: more
Got: from
Got: messages
Got: for
Got: the
Got: thread
Got: you
```

값들의 순서는 시스템에 따라 다르게 보일 수도 있다. 이제 채널이 어떤 식으로 동작하는 보았으니, 다른 방법을 알아보자.

---

## 공유 상태 동시성

메세지 패싱은 동시성을 다루는 좋은 방법이지만, 유일한 수단은 아니다. Go 언어 문서에서는 슬로건의 일부에서 다음과 같이 말한다. “메모리를 공유함으로써 소통하세요.”

메모리를 공유하는 통신은 어떤 형태로 보일까? 어떤 면에서, 프로그래밍 언어의 채널들은 단일 소유권과 유사하다. 만약 채널로 값을 송신하면, 그 값을 더이상 쓸 수 없기 때문이다. 공유 메모리 동시성은 복수 소유권과 유사하다. 복수개의 스레드들이 동시에 동일한 메모리 위치로 접근할 수 있다. 스마트 포인터들이 복수 소유권을 가능하게 만드는 내용을 앞서 다루었듯이, 복수 소유권은 이 서로 다른 소유권자들의 관리가 필요하기 때문에 복잡성을 더할 수 있다. 러스트의 타입 시스템과 소유권 규칙은 이러한 관리를 잘 할수 있도록 유도한다. 예를 들면, 공유 메모리를 위한 더 일반적인 동시성의 기초 재료 중 하나인 뮤텍스 (mutex)를 살펴 보자.

### 뮤텍스를 사용하여 한번에 한 스레드에서의 데이터 접근을 허용하기

뮤텍스는 *상호 배제 (mutual exclusion)*의 줄임말로서, 내부에서 뮤텍스는 주어진 시간에 오직 하나의 스레드만 데이터 접근을 허용한다. 뮤텍스 내부의 데이터에 접근하기 위해서 스레드는 먼저 뮤텍스의 *락 (lock)*을 얻기 요청함으로써 접근을 원한다는 신호를 보내야 한다. 락은 누가 배타적으로 데이터에 접근하는지 추적하는 뮤텍스의 부분인 데이터 구조다. 그러므로, 뮤텍스는 잠금 시스템을 통해 가지고 있는 데이터를 보호하는 것으로 묘사된다. 

뮤텍스는 사용하기 어렵다는 평판을 가지고 있는데 이는 다음 두 가지 규칙을 기억해야 하기 때문이다.

- 데이터를 사용하기 전에 반드시 락을 얻는 시도를 해야한다.
- 만일 뮤텍스가 보호하는 데이터의 사용이 끝났다면, 다른 스레드들이 락을 얻을 수 있도록 반드시 락을 언락해야 한다.

뮤텍스의 관리는 매우 어려워질 수 있는데 이것이 바로 사람들이 채널을 더 선호하는 이유다. 하지만, 러스트의 타입 시스템과 소유권 규칙에 감사하게도, 잘못 락을 얻거나 언락 할 수가 없다. 

### Mutex<T>의 API

어떻게 뮤텍스를 이용하는지에 대한 예제로서, 다음과 같이 단일 스레드 맥락 내에서 뮤텍스를 사용하는 것으로 시작해보자.

```rust
use std::sync::Mutex;

fn main() {
    let m = Mutex::new(5);

    {
        let mut num = m.lock().unwrap();
        *num = 6;
    }

    println!("m = {:?}", m);
}
```

많은 타입들처럼 `Mutex<T>`는 연관함수 `new`를 사용해 만들어진다. 뮤텍스 내의 데이터에 접근하기 위해서는 `lock` 메소드를 사용해 락을 얻는다. 이 호출은 현재의 스레드를 블록할 것이고, 락을 얻는 차례가 될 때까지 아무런 작업도 할 수 없게 된다.

`lock`의 호출은 다른 스레드가 패닉 상태의 락을 가지고 있을 경우 실패할 수도 있다. 그런 경우 아무도 락을 얻을 수 없게 되므로, `unwrap`을 택해 그런 상황일 경우 이 스레드에 패닉을 일으킨다.

락을 얻고난 다음에는 그 반환값 (위의 경우에는 `num` 이라는 이름의 값) 을 내부의 데이터에 대한 가변 참조자처럼 다룰 수 있다. 타입 시스템은 `m` 내부의 값을 사용하기 전에 락을 얻는 것을 확실히 해준다. `Mutex<i32>`는 `i32`가 아니므로, *반드시* `i32` 값을 사용하기 위한 락을 얻어야 한다. 이 락은 잊어버릴 수 없다. 만약 잊어버린다면 타입 시스템이 내부의 `i32`에 접근할 수 없게 할 것이다.

예제에서 본 것처럼 `Mutex<T>`는 스마트 포인터다. 더 정확하게는, `lock`의 호출은 `MutexGurad` 라고 불리우는 스마트 포인터를 반환한다. 이 스마트 포인터는 해당 포인터의 내부 데이터를 가리키도록 `Deref`가 구현되어있다. 이 스마트 포인터는 또한 `MutexGuard`가 스코프 밖으로 벗어났을 때 자동으로 락을 해제하는 `Drop` 구현체를 가지고 있는데, 이것이 위 예제 코드 내부 스코프의 끝에서 일어나는 일이다. 결과적으로 락이 자동으로 해제되기 때문에, 락을 해제하는 것을 잊어버리고 다른 스레드에 의해 뮤텍스가 사용되는 것을 막는 위험을 짊어지지 않아도 된다.

### 여러 스레드 사이에서 Mutex<T> 공유하기

이제 `Mutex<T>`를 사용하여 여러 스레드들 사이에서 값을 공유해보자. 10개의 스레드를 돌리고 이들이 카운터 값을 1만큼 증가 시켜서, 카운터가 0에서 10으로 가도록 해보자.  다음 예제는 컴파일 에러가 날 것이고, 이 에러를 이용해 `Mutex<T>`를 사용하는 방법과 러스트가 이를 어떻게 돕는지 볼 수 있다.

```rust
use std::sync::Mutex;
use std::thread;

fn main() {
    let counter = Mutex::new(0);
    let mut handles = vec![];

    for _ in 0..10 {
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();

            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```

앞서 했던 것처럼 `Mutex<T>` 내부에 `i32`를 담는 `counter` 변수를 만든다. 그 다음 숫자 범위로 반복해 10개의 스레드를 만든다. 여기서 `thread::spawn`을 사용해 동일한 클로저를 모든 스레드에게 주었는데, 이 클로저는 스레드로 카운터를 이동시키고, `lock` 메소드를 호출함으로써 `Mutex<T>` 의 락을 얻은 다음, 뮤텍스 내의 값을 1만큼 증가시킨다. 스레드가 자신의 클로저 실행을 끝냈을 때, `num`은 스코프 밖으로 벗어나고 락이 해제되어 다른 스레드가 이를 얻을 수 있다. 

메인 스레드에서는 `JoinHandle`을 전부 모으고 각각 `join`을 호출해 모든 스레드가 종료되는 것을 보장한다. 그리고 결과를 출력하는 간단한 프로그램이다. 하지만 컴파일되지 않는데, 에러 로그를 살펴보자. 

```
error[E0382]: capture of moved value: `counter`
  --> src/main.rs:10:27
   |
9  |         let handle = thread::spawn(move || {
   |                                    ------- value moved (into closure) here
10 |             let mut num = counter.lock().unwrap();
   |                           ^^^^^^^ value captured here after move
   |
   = note: move occurs because `counter` has type `std::sync::Mutex<i32>`,
   which does not implement the `Copy` trait

error[E0382]: use of moved value: `counter`
  --> src/main.rs:21:29
   |
9  |         let handle = thread::spawn(move || {
   |                                    ------- value moved (into closure) here
...
21 |     println!("Result: {}", *counter.lock().unwrap());
   |                             ^^^^^^^ value used here after move
   |
   = note: move occurs because `counter` has type `std::sync::Mutex<i32>`,
   which does not implement the `Copy` trait

error: aborting due to 2 previous errors
```

이 에러 메세지는 `counter` 값이 클로저 내부로 이동되어서 `lock`을 호출할 때 캡처되었다고 말한다. 이 설명은 우리가 의도한 것처럼 보이지만, 허용되지 않는다.

프로그램을 단순화해서 이를 알아내보자. 10개의 `for` 루프 대신 루프 없이 두 개의 스레드만을 만들어서 살펴보자.

```rust
use std::sync::Mutex;
use std::thread;

fn main() {
    let counter = Mutex::new(0);
    let mut handles = vec![];

    let handle = thread::spawn(move || {
        let mut num = counter.lock().unwrap();

        *num += 1;
    });
    handles.push(handle);

    let handle2 = thread::spawn(move || {
        let mut num2 = counter.lock().unwrap();

        *num2 += 1;
    });
    handles.push(handle2);

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```

이렇게 두 개의 스레드를 만들고 두번째 스레드에서 사용되는 변수 이름을 `handle2`와 `num2`로 바꾸었다. 이제 코드를 실행하면, 컴파일러가 다음 에러 메세지를 보여준다.

```
error[E0382]: capture of moved value: `counter`
  --> src/main.rs:16:24
   |
8  |     let handle = thread::spawn(move || {
   |                                ------- value moved (into closure) here
...
16 |         let mut num2 = counter.lock().unwrap();
   |                        ^^^^^^^ value captured here after move
   |
   = note: move occurs because `counter` has type `std::sync::Mutex<i32>`,
   which does not implement the `Copy` trait

error[E0382]: use of moved value: `counter`
  --> src/main.rs:26:29
   |
8  |     let handle = thread::spawn(move || {
   |                                ------- value moved (into closure) here
...
26 |     println!("Result: {}", *counter.lock().unwrap());
   |                             ^^^^^^^ value used here after move
   |
   = note: move occurs because `counter` has type `std::sync::Mutex<i32>`,
   which does not implement the `Copy` trait

error: aborting due to 2 previous errors
```

첫번째 에러 메세지는 `counter`가 `handle`과 연관된 스레드에 대한 클로저 내부로 이동되었음을 알려준다. 이 이동이 두번째 스레드에서 `lock`의 호출을 시도하고 `num2`에 결과를 저장할 때 `counter`를 캡처하는 것을 막는다. 따라서 러스트는 `couter`의 소유권을 여러 스레드로 이동시킬 수 없음을 말하고 있다. 이는 더 일찍 발견하기 어려운데 그 이유는  스레드들이 루프 내에 있었고, 러스트는 루프의 다른 반복 회차 내의 다른 스레드를 지적할 수 없기 때문이다. 앞서 배웠던 복수 소유자 메소드를 이용해 이 컴파일 에러를 고쳐보자.

### 여러 스레드들과 함께하는 복수 소유권

앞서 참조 카운팅 값을 만들기 위해 스마트 포인터 `Rc<T>`를 사용함으로써 값에게 복수의 소유권자를 만들었다. 동일한 일을 여기서도 해보자. `Mutex<T>`를  `Rc<T>`로 감싸서 스레드로 소유권을 이동시키기 전에 `Rc<T>`를 복제했다. 다시 `for` 루프를 이용해 클로저와 함께 쓴 `move` 키워드를 유지해보자.

```rust
use std::rc::Rc;
use std::sync::Mutex;
use std::thread;

fn main() {
    let counter = Rc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Rc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();

            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```

다시 한번 컴파일 하면 다른 에러가 발생한다.

```
error[E0277]: the trait bound `std::rc::Rc<std::sync::Mutex<i32>>:
std::marker::Send` is not satisfied in `[closure@src/main.rs:11:36:
15:10 counter:std::rc::Rc<std::sync::Mutex<i32>>]`
  --> src/main.rs:11:22
   |
11 |         let handle = thread::spawn(move || {
   |                      ^^^^^^^^^^^^^ `std::rc::Rc<std::sync::Mutex<i32>>`
cannot be sent between threads safely
   |
   = help: within `[closure@src/main.rs:11:36: 15:10
counter:std::rc::Rc<std::sync::Mutex<i32>>]`, the trait `std::marker::Send` is
not implemented for `std::rc::Rc<std::sync::Mutex<i32>>`
   = note: required because it appears within the type
`[closure@src/main.rs:11:36: 15:10 counter:std::rc::Rc<std::sync::Mutex<i32>>]`
   = note: required by `std::thread::spawn`
```

첫번째 인라인 에러는 `“*std::rc::Rc<std::sync::Mutex<i32>>`는 스레드 사이에 안전하게 보내질 수 없다”`* 라고 말한다. 이에 대한 이유는 다음 중요한 부분인 에러 메세지 내에 있다. `*“트레잇 바운드 `Send`가 만족 되지 않았다."`* `Send`는 이후에 다룬다. 이것은 스레드와 함께 사용하는 타입들이 동시적 상황들 내에서 쓰이기 위한 것임을 확실히 하는 트레잇 중 하나다.

안타깝게도, `Rc<T>`는 스레드를 교차하면서 공유하기에는 안전하지 않다. `Rc<T>`가 참조 카운트를 관리할 때, 각각의 `clone` 호출마다 카운트에 더하고 각 클론이 버려질 때마다 카운트에서 제거된다. 하지만 이것은 다른 스레드에 의해 카운트를 변경하는 것을 방해할 수 없도록 확실히 하는 어떠한 동시성 기초 재료도 이용하지 않는다. 이는 잘못된 카운트를 야기할 수 있다. 결과적으로 메모리 누수를 발생시키거나 아직 다 쓰기 전에 값이 버려질 수 있는 교모한 버그를 낳을 수 있다. 지금 원하는 것은 정확히 `Rc<T>`와 비슷하지만 스레드-안전한 방식으로 참조 카운트를 바꾸는 녀석이다. 

### Atomic Reference Counting with Arc<T>

`Arc<T>`를 이용하는 아토믹 (atomic) 참조 카운팅

다행히도, `Arc<T>`가 동시적 상황에서 안전하게 사용할 수 있는 `Rc<T>`타입이다. *a*는 *아토믹(atomic)* 을 의미하는데, 즉 이것이 *원자적으로 참조자를 세는 (atomically reference counted)* 타입임을 의미한다. 아토믹은 여기서 자세히 다루지 않을 추가적인 동시성 기초 재료 종류다. 더 자세히 알고 싶으면 `std::sync::atomic`에 대한 표준 라이브러리 문서를 보자. 이 시점에서는 그냥 아토믹이 기초 타입처럼 동작하지만 스레드를 교차하며 공유해도 안전하다는 것만 알면 된다.

그렇다면 왜 모든 기초 타입이 아토믹하지 않은지, 그리고 표준 라이브러리 타입은 왜 기본적으로 `Arc<T>`를 구현에 이용하지 않는지를 궁금해 할 수도 있다. 그 이유는 스레드 안정성이란 것이 정말로 원할 때만 지불하고 싶을 성능 저하를 일으키기 때문이다. 만일 단일 스레드 내의 값에 대한 연산만 수행하는 중이라면, 아토믹이 제공하는 보장을 강제하지 않아도 된다면 코드는 더 빠르게 실행될 수 있다.

다시 예제를 보자. `Arc<T>`와 `Rc<T>`는 같은 API를 가지고 있으므로, `use`를 사용하는 라인과 `new` 호출, 그리고 `clone` 호출 부분을 바꾸는 것으로 프로그램을 수정하자.

```rust
use std::sync::{Mutex, Arc};
use std::thread;

fn main() {
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        let counter = Arc::clone(&counter);
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();

            *num += 1;
        });
        handles.push(handle);
    }

    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```

이 코드는 다음을 출력한다.

```
Result: 10
```

이제 0부터 10까지 세었고, 이는 그렇게 크게 인상적인 것 같지 않을지도 모르겠지만, `Mutex<T>`와 스레드 안정성에 대해 많은 것을 가르쳐 주었다. 또한 이 프로그램의 구조를 사용해 단지 카운터를 증가시키는 것 보다 더 복잡한 연산을 할 수도 있다. 이 전략을 사용해서 계산할 것을 독립적인 부분들로 나누고, 해당 부분들을 스레드로 쪼갠 다음, `Mutex<T>`를 사용해 각 스레드가 해당 부분의 최종 결과를 갱신하도록 할 수 있다.

### RefCell<T>/Rc<T>와 Mutex<T>/Arc<T> 간의 유사성

다시 생각해보면 `counter`가 불변이지만 이것 내부의 값에 대한 가변 참조자를 가지고 올 수 있음을 알 수 있다. 이는 `Mutex<T>`가 `Cell` 가족이 그러하듯 내부 가변성을 제공한다는 의미다. `Rc<T>`의 내용물을 변경할 수 있도록 하기 위해 `RefCell<T>`를 사용하는 것과 마찬가지로, `Arc<T>` 내부의 값을 변경하기 위해 `Mutex<T>`를 이용한다.

주목할만한 또 다른 세부 사항은 `Mutex<T>`를 사용할 때 러스트가 모든 종류의 논리적 에러로부터 보호해줄 수 없다는 것이다. `Rc<T>`를 사용하는 것은 두 `Rc<T>` 값들이 서로를 참조해 메모리 누수를 야기하는 순환 참조자를 만들 위험성이 따라오는 것이었음을 상기하자. 이와 유사하게, `Mutex<T>`는 *데드락 (deadlock)* 을 생성할 위험성이 따라온다. 어떤 연산이 두 개의 리소스에 대한 락을 얻을 필요가 있고 두 개의 스레드가 하나씩의 락을 얻는다면, 서로가 서로를 영원히 기다리는 식으로 발생된다.

---

## Sync와 Send 트레잇을 이용한 확장 가능한 동시성

흥미롭게도, 러스트 언어는 매우 적은 숫자의 동시성 기능을 갖고 있다. 여태 얘기해 온 거의 모든 동시성 기능들이 언어의 부분이 아니라 표준 라이브러리의 영역이었다. 동시성 제어를 위한 옵션들은 언어 혹은 표준 라이브러리에 제한되지 않는다. 스스로만의 동시성 기능을 작성하거나 다른 이들이 작성한 것을 이용할 수 있다.

그러나 두 개의 동시성 개념이 이 언어에 내재되어 있다. 바로 `std::marker` 트레잇인 `Sync`와 `Send`다.

### Send를 사용해 스레드 사이에 소유권 이전을 허용하기

`Send` 마커 트레잇은 `Send`가 구현된 타입의 소유권이 스레드 사이에서 이전될 수 있음을 나타낸다. 거의 대부분의 러스트 타입이 `Send`지만 몇 개의 예외가 있는데, 그 중 `Rc<T>`도 있다. 이것은 `Send`가 될 수 없는데 그 이유는 우리가 `Rc<T>` 값을 클론해 다른 스레드로 복제본의 소유권 전송을 시도한다면, 두 스레드 모두 동시에 참조 카운트 값을 갱신할지도 모르기 때문이다. 이러한 이유로, `Rc<T>`는 스레드-안정성 성능 저하를 지불하지 않아도 되는 단일 스레드의 경우에 사용되도록 구현되었다. 

따라서, 러스트의 타입 시스템과 트레잇 바운드는 결코 우연히라도 스레드 사이로 `Rc<T>` 값을 불안전하게 보낼 수 없도록 막아준다. 앞선 예제를 시도할 때, `the trait Send is not implemented for Rc<Mutex<i32>>`라는 에러를 얻었다. `Send`가 구현된 `Arc<T>` 로 바꿨을 때는 코드가 컴파일 되었다. 

전체적으로 `Send` 타입으로 구성된 어떤 타입은 또한 자동적으로 `Send`로 마킹된다. 로우 포인터 (raw pointer)를 빼고 거의 모든 기초 타입이 `Send`인데 이는 이후에 다룬다.

### Sync를 사용해 여러 스레드로부터의 접근을 허용하기

`Sync` 마커 트레잇은 `Sync`가 구현된 타입이 여러 스레드로부터 안전하게 참조 가능함을 나타낸다. 바꿔 말하면, 만일 `&T` (`T`의 참조자)가 `Send`면, 즉 참조자가 다른 스레드로 안전하게 보내질 수 있따면, `T`는 `Sync`하다. `Send`와 유사하게, 기초 타입들은 `Sync`하고, 또한 `Sync` 한 타입들로 전체가 구성된 타입 또한 `Sync`하다.

스마트 포인터 `Rc<T>`는 `Send`가 아닌 이유와 동일한 이유로 `Sync` 하지도 않다. `RefCell<T>` 타입과 연관된 `Cell<T>` 타입의 가족들도 `Sync` 하지 않는다. `RefCell<T>`가 런타임에 수행하는 빌림 검사 구현은 스레드-안전하지 않다. 스마트 포인터 `Mutex<T>`는 `Sync`하고 앞서 본 것처럼 여러 스레드에서 접근을 공유하는데 사용될 수 있다. 

### Send와 Sync를 손수 구현하는 것은 안전하지 않다

`Send`와 `Sync`트레잇들로 구성된 타입들이 자동적으로 `Send`될 수 있고 `Sync`하기 때문에, 이 트레잇들을 손수 구현치 않아도 된다. 마커 트레잇으로서, 이들은 심지어 구현할 어떠한 메소드도 없다. 이들은 그저 동시성과 관련된 불변성을 강제하는데 유용할 따름이다. 

이 트레잇들은 손수 구현하는 것은 안전하지 않은 러스트 코드 구현을 수반한다. 이후 안전하지 않은 러스트 코드에 대해서 이야기 하자. 지금 중요한 정보는 `Send`되고 `Sync`하지 않은 요소들로 구성된 새로운 동시적 타입을 만드는 것이 안정성 보장을 유지하기 위해 조심스러운 생각을 요구한다는 점이다. 

### 정리

일찍이 언급한 것처럼, 러스트가 동시성을 제어하는 방법이 언어의 매우 작은 부분이기 때문에, 많은 동시성 솔루션이 크레이트로 구현된다. 이들은 표준 라이브러리보다 더 빠르게 진화하므로, 멀티스레드 상황에서 사용하기 위해 현재 가장 최신 기술의 크레이트를 온라인으로 검색해보자. 

러스트 표준 라이브러리는 메세지 패싱을 위해 채널을 제공하고, 동시적 맥락에서 사용하기에 안전한 `Mutex<T>`와 `Arc<T>`같은 스마트 포인터 타입들을 제공한다. 타입 시스템과 빌림 검사기는 이러한 솔루션을 이용하는 코드가 데이터 레이스 혹은 유효하지 않은 참조자로 끝나지 않을 것을 보장한다. 만약 코드가 컴파일 된다면, 다른 언어에서는 흔한 추적하기 어려운 버그 종류들 없이 여러 스레드 상에서 문제없이 동작할 것이라고 자신감 있게 말할 수 있다. 동시성 프로그래밍은 더이상 두려워할 개념이 아니다.

다음은, 러스트 프로그램이 점차 커짐에 따라서 문제를 모델링하고 솔루션을 구조화하는 자연스러운 방법에 대해 말해보자. 더불어서 객체 지향 프로그램으로부터 친숙할지도 모를 개념들과 러스트의 관용구가 어떻게 연관되어 있는지 다루자.

---

# 러스트의 객체 지향 프로그래밍 기능들

객체 지향 프로그래밍(OOP)는 프로그램을 모델링 하는 방식이다. 객체는 1960년대 Simula에서 유래되었다. 이 객체들은 임의의 객체들이 서로에게 메세지를 전달하는 Alan Kay의 프로그래밍 아키텍처에 영향을 끼쳤다. 1967년 그는 객체 지향 프로그래밍이라는 용어를 이 아키텍처를 설명하기 위해 사용했다. 다수의 정의가 경쟁적으로 OOP가 무엇인지 설명한다. 그 중 일부는 Rust를 객체 지향이라고 분류하지만 다른 정의는 그렇지 않다. 이번 장에서 일반적인 객체 지향이 가진 특성들과 어떻게 이런 특성들이 러스트다운 표현들로 번역되었는지 알아보자. 그런 후에 객체 지향적 디자인 패턴을 Rust에서 어떻게 구현하는지 보여주고 이를 Rust가 가진 강점을 사용해 구현했을 경우의 기회비용에 대해 토의하자.

## 객체 지향 언어의 특성

객체 지향적인 언어가 반드시 갖춰야 할 기능에 대해 프로그래밍 커뮤니티들은 의견일치를 보지 못하고 있다. 러서트는 OOP도 포함하여 많은 프로그래밍 패러다임에 영향을 받았다. 예를 들면 앞서 살펴본 기능인 함수형 프로그래밍에서 온 기능들 처럼말이다. OOP 언어라면 거의 틀림없이 몇가지 공통적인 특성을 공유하는데, 객체, 캡슐화 및 상속이 있다. 이 특성들이 각각 뜻하는 것과 러스트가 이를 지원하는지에 대해 살펴보자.

### 객체는 데이터와 동작을 담는다.

흔히 The Gang of Four라고도 불리우는 Erich Gamma, Richard Helm, Ralph Johnson, 그리고 John Vlissides (Addison-Wesley Professional, 1994)의 책 *Design Patterns: Elements of Reusable Object-Oriented Software*은 객체 지향 디자인 패턴의 편람이다. 이 책에서는 OOP를 다음과 같이 정의한다.

> 객체-지향 프로그램은 객체로 구성된다. *객체*는 데이터 및 이 데이터를 활용하는 프로시저를 묶는다. 이 프로시저들은 보통 *메소드* 혹은 *연산 (Operation)*으로 불린다.
> 

이 정의에 따르면, 러스트는 객체 지향적이다. 구조체와 열거형은 데이터를 갖고, `impl` 블럭은 그 구조체와 열거형에 대한 메소드를 제공한다. 설령 메소드를 갖는 구조체와 열거형을 *객체*라고 호칭하지 않더라도, 그들은 동일한 기능을 수행하며, 이는 Gang of Four의 객체에 대한 정의를 따른다.

### 상세 구현을 은닉하는 캡슐화

일반적으로 OOP와 관련된 또다른 면은 캡슐화로, 그 의미는 객체를 이용하는 코드에서 그 객체의 상세 구현에 접근할 수 없게 한다는 것이다. 따라서, 유일하게 객체와 상호작용하는 방법은 이것의 공개 API를 통하는 것이다. 객체를 사용하는 코드는 직접 객체의 내부에 접근해 데이터나 동작을 변경해서는 안된다. 이는 프로그래머가 객체를 사용하는 코드의 변경없이 객체 내부를 변경하거나 리팩토링 할 수 있도록 해준다.

앞서 모듈을 배우면서 어떻게 캡슐화를 제어하는지에 대해 논의했다. `pub` 키워드를 사용하여 어떤 모듈들, 타입들, 함수들, 그리고 메소드들이 공개될 것인가를 결정할 수 있으며, 기본적으로 모든 것들은 비공개다. 예를 들면, `i32` 값의 벡터 항목을 가지고 있는 `AveragedCollection` 구조체를 정의할 수 있다. 또한 이 구조체는 벡터의 값에 대한 평균값을 담는 항목도 있는데, 이는 누구든 평균값이 필요한 순간마다 매번 이를 계산할 필요는 없음을 의미한다. 바꿔 말하면, `AveragedCollection`은 계산된 평균값을 캐쉬할 것이다. 다음은 `AveragedCollection` 구조체에 대한 정의다.

```rust
pub struct AveragedCollection {
	list: Vec<i32>,
	average: f64,
}
```

구조체가 `pub`로 표기되면 다른 코드가 이를 사용할 수 있게 되지만, 구조체 안에 존재하는 항목들은 여전히 비공개다. 이는 이번 사례에 매우 중요한데, 그 이유는 하나의 값이 리스트에서 더해지거나 제거될 때마다 평균 또한 갱신되는 것을 확신하길 원하기 때문이다. 이제 `add`, `remove`, `average` 메소드를 구조체에 구현해 이를 달성해보자.

```rust
impl AveragedCollection {
    pub fn add(&mut self, value: i32) {
        self.list.push(value);
        self.update_average();
    }

    pub fn remove(&mut self) -> Option<i32> {
        let result = self.list.pop();
        match result {
            Some(value) => {
                self.update_average();
                Some(value)
            },
            None => None,
        }
    }

    pub fn average(&self) -> f64 {
        self.average
    }

    fn update_average(&mut self) {
        let total: i32 = self.list.iter().sum();
        self.average = total as f64 / self.list.len() as f64;
    }
}
```

공개 메소드들 `add`, `revmove`, `average`는 `AveragedCollection`의 인스턴스를 수정하는 유일한 방법이다. 아이템이 `list`에 `add` 메소드를 통해 추가되거나 `remove` 메소드를 통해 제거될 때, 각각의 호출은 비공개 `updagte_average` 메소드를 호출하여 `average` 필드를 변경하도록 하는 역할 또한 수행한다. 

`list`와 `average` 필드를 비공개로 두었으므로 외부 코드가 list 필드에 직접 아이템들을 추가하거나 제거할 방법은 없다. 그렇지 않으면, `average` 필드는 `list`가 변경될 때 동기화되지 않을지도 모른다. `average` 메소드는 `average` 필드의 값을 반환하여, 외부 코드가 `average`를 읽을 수 있도록 하지만, 변경은 안된다.

`AveragedCollection`의 내부 구현을 캡슐화 했기 때문에, 차후에 데이터 구조 등을 쉽게 변경할 수 있다. 예를 들면, list 필드에 대해서 `Vec<i32>`가 아닌 `HashSet<i32>`를 사용할 수도 있다. `add`, `remove` 그리고 `average` 공개 메소드들의 선언이 그대로 유지되는 한, `AveragedCollection`를 사용하는 코드들은 변경될 필요가 없다. 만약 `list`를 공개했다면 그럴수 없었을 것이다. `HashSet<i32>`와 `Vec<i32>`는 아이템들을 추가하거나 제거하기 위한 메소드들이 다르므로, 만약 `list`에 직접 접근해 변경하는 방식의 외부 코드들이 있다면 모두 변경되어야 할 것이다.

만약 캡슐화가 객체 지향을 염두하는 언어를 위한 필요 요소라면, 러스트는 이를 만족한다. 코드의 서로 다른 부분들에 대해 `pub`을 사용하거나 사용하지 않는 옵션이 구현 세부 사항의 캡슐화를 가능케 한다.

### 타입 시스템과 코드 공유로서의 상속

*상속*은 어떤 객체가 다른 객체의 정의를 상속받아서, 이를 통해 부모 객체의 데이터와 동작들을 다시 정의하지 않고도 얻을 수 있게 해주는 메커니즘이다. 

만약 객체 지향 언어가 반드시 상속을 제공해야 한다면, 러스트는 그렇지 않은 쪽이다. 부모 구조체의 필드와 메소드 구현을 상속받은 구조체를 정의할 방법은 없다. 하지만 상속에 익숙하다면, 우선 이를 사용하고자 하는 이유에 따라 러스트의 다른 솔루션들을 이용할 수 있다.

보통 두 가지 중요한 이유에 의해 상속을 택한다. 하나는 코드를 재사용하는 것이다. 어떤 타입의 특정한 행위를 구현할 수 있고, 상속은 다른 타입을 위해 그 구현을 재사용할 수 있도록 만들어준다. 대신 기본 트레잇 메소드의 구현을 이용해 러스트 코드를 공유할 수 있는데, 이는 [앞에서](https://www.notion.so/Rust-0d7ead296adb4593aa99b2b8cb2e5307?pvs=21) `Summary` 트레잇에 `summarize` 메소드의 기본 구현을 추가할 때 보았던 것이다. `Summary` 트레잇을 구현하는 어떤 타입이든, `summarize` 메소드를 별도로 작성하지 않더라도 사용 가능하다. 이는 어떤 메소드의 구현체를 갖는 부모 클래스와 그를 상속받는 자식 클래스 또한 그 메소드의 해당 구현체를 갖는 것과 유사하다. 또한 `Summary` 트레잇을 구현할 때 `summarize`의 기본 구현을 오버라이딩 할 수 있고, 이는 자식 클래스가 부모 클래스에서 상속받는 메소드를 오버라이딩하는 것과 유사하다.

상속을 사용하는 다른 이유는 타입 시스템과 관련되어있다. 자식 타입을 같은 위치에서 부모 타입처럼 사용할 수 있게 하기 위함이다. 이를 또한 *다형성 (polymorphisim)* 이라고도 부르는데, 이는 여러 객체들이 일정한 특성을 공유한다면 이들을 런타임에 서로 바꿔 대입해 사용할 수 있음을 의미한다.

> 다형성
많은 사람들이 다형성을 상속과 동일시 한다. 하지만 다형성은 다수의 타입들의 데이터에 대해 동작 가능한 코드를 나타내는 더 범용적인 개념이다. 상속에서는 이런 타입들이 일반적으로 하위 클래스에 해당한다.
러스트는 대신 제네릭을 사용해 호환 가능한 타입을 추상화하고 트레잇 바운드를 이용해 해당 타입들이 반드시 제공해야 하는 제약사항을 부과한다. 이것을 종종 *범주내 매개변수형 다형성(bouynded parametric polymophism)*이라고 부른다.
> 

최근에는 상속이 많은 프로그래밍 언어에서 프로그래밍 디자인 솔루션으로서의 인기가 떨어지고 있는데, 그 이유는 필요한 것보다 더 많은 코드를 공유할 수 있는 위험이 있기 때문이다. 하위 클래스가 늘 그들의 부모 클래스의 모든 특성을 공유해서는 안되지만 상속한다면 그렇게 된다. 이는 프로그램의 유연성을 저하시킬 수 있다. 또한, 하위 클래스에서는 타당하지 않거나 적용될 수 없어서 에러를 유발하는 메소드들이 호출될 수 있는 가능성을 만든다. 게다가, 어떤 언어들은 하나의 클래스에 대한 상속만을 허용하기 때문에 프로그램 디자인의 유연성을 더욱 제한하게 된다.

이런 이유로, 러스트는 다른 방식을 취해 상속 대신에 트레잇 객체를 사용한다. 러스트에서 어떤 식으로 트레잇 객체가 다형성을 가능케 하는지 살펴보자.

---

## 트레잇 객체를 사용해 다른 타입 간의 값 허용하기

앞서 벡터에 대해서 설명할 때 벡터가 한 번에 하나의 타입만 보관할 수 있다는 제약사항이 있다고 언급했다. 그때, 정수, 부동소수점, 그리고 문자를 보관하기 위한 variant들을 가지고 있는 `SpreedsheetCell` 열거형을 정의했다. 이것은 각 칸마다 다른 타입의 데이터를 저장할 수 있으면서도 여전히 그 칸들의 한 묶음을 대표하는 벡터를 가질 수 있다는 것을 의미했다. 이는 교환 가능한 아이템들이 코드를 컴파일할 때 알 수 있는 정해진 몇 개의 타입인 경우 완벽한 해결책이다.

하지만, 우리는 라이브러리 사용자가 특정 상황에서 유효한 타입 묶음을 확장할 수 있도록 하길 원한다. 이를 이룰 수 있는지 확인하기 위해, 각 아이템들의 리스트에 걸쳐 각각에 대해 `draw` 메소드를 호출해 이를 화면에 그리는 그래픽 유저 인터페이스(GUI) 도구는 만들 것이다. 이제 만들 라이브러리 크레이트는 gui라고 호명되고 GUI 라이브러리 구조를 포괄한다. 이 크레이트는 사용자들이 사용할 수 있는 몇 가지 타입들, `Button`이나 `TextField` 들을 포함하게 될 것이다. 추가로 `gui` 사용자들은 그들 고유의 타입을 만들어 그리고자 할 것이다. 예시로, 어떤 프로그래머는 `Image`를 추가할지도 모르고 또다른 누군가는 `SelectBox`를 추가할수도 있다.

이번 예제에서 총체적인 GUI 라이브러리를 구현하지 않겠지만, 어떻게 이 조각들이 맞물려 함께 동작할 수 있는지 볼 수 있을것이다. 라이브러리를 작성하는 시점에서는 다른 프로그래머들이 만들고자 하는 모든 타입들을 알 수 없다. 하지만 알 수 있는 것은 `gui`가 다른 타입들의 다양한 값에 대해 계속해서 추적해야 하고, draw 메소드가 이 다양한 값들 각각에 호출되어야 한다는 것이다. `draw` 메소드를 호출했을 때 벌어지는 일에 대해서 정확히 알 필요는 없고, 그저 우리가 호출할 수 있는 해당 메소드를 그 값이 가지고 있음을 알면 된다.

상속이 있는 언어를 가지고 이 작업을 하기 위해서는 `draw`라는 이름의 메소드를 갖고 있는 `component` 라는 클래스를 정의할 수도 있다. 다른 클래스들, 이를테면 `Button`, `Image`, 그리고 `SelectBox` 같은 것들은 `Component`를 상속받고 따라서 `draw` 메소드를 물려받게 된다. 이들은 각각 `draw` 메소드를 오버라이딩하여 그들의 고유 동작을 정의할 수 있으나, 프레임워크는 모든 유형을 마치 `Componenet`인 것처럼 다룰 수 있고 `draw`를 호출할 수 있다. 하지만 러스트가 상속이 없기 때문에, `gui` 라이브러리를 구축하는 다른 방법을 찾아 사용자들이 새로운 타입을 정의하고 확장할 수 있도록 할 필요가 있다.

### 공통된 동작을 위한 트레잇 정의하기

`gui`가 원하는 동작을 구현하기 위해, `draw`라는 이름의 메소드를 하나 갖는 `Draw`라는 이름의 트레잇을 정의할 것이다. 그러면 *트레잇 객체 (trait object)*를 취하는 벡터를 정의할 수 있다. 트레잇 객체는 특정 트레잇을 구현한 타입의 인스턴스를 가리킨다. `&`참조자나 `Box<T>` 스마트 포인터 같은 포인터 종류로 명시함으로서 트레잇 객체를 만들고, 그런 다음 관련된 트레잇을 특정한다. (트레잇 객체에 포인터를 사용하는 이유에 이후에 다룬다) 제네릭 타입이나 구체 타입 대신 트레잇 객체를 사용할 수 있다. 트레잇 객체를 사용하는 곳이 어디든, 러스트의 타입 시스템은 컴파일 타임에 해당 문맥 안에 사용된 값이 트레잇 객체의 트레잇을 구현할 것을 보장한다. 다시 말하면, 컴파일 타임에 모든 가능한 타입을 알 필요가 없다. 

러스트에서는 구조체와 열거형을 다른 언어의 객체와 구분하기 위해 “객체”라고 부르는 것을 자제한다고 언급했었다. 구조체 또는 열거형에서는 구조체 필드의 데이터와 `impl` 블록의 동작이 분리되는 반면, 다른 언어에서는 데이터와 동작이 결합되어 객체로 명명된다. 그러나 트레잇 객체들은 데이터와 동작을 결합한다는 의미에서 다른 언어의 객체와 비슷하다. 하지만 트레잇 객체는 트레잇 객체에 데이터를 추가할 수 없다는 점에서 전통적인 객체들과 다르다. 트레잇 객체는 다른 언어들의 객체만큼 범용적으로 유용하지는 않다. 그들의 명확한 목적은 공통된 동작들에 걸친 추상화를 가능하도록 하는것이다. 

다음은 `draw`라는 이름의 메소드를 갖는 `Draw` 라는 트레잇을 정의하는 방법이다.

```rust
pub triat Draw {
	fn draw(&self);
}
```

다음은 새로운 문법이 등장한다. `components` 라는 벡터를 보유하고 있는 `Screen`이라는 구조체를 정의한다. `Box<Draw>` 타입의 벡터인데, 이것이 트레잇 객체다. 이것은 `Draw` 트레잇을 구현한 `Box`에 담긴 임의의 타입에 대한 대역이다.

```rust
pub struct Screen {
	pub components: Vec<Box<Draw>>,
}
```

`Screen` 구조체에서는 다음처럼 각 `components`마다 `draw` 메소드를 호출하는 `run` 메소드를 정의한다.

```rust
impl Screen {
    pub fn run(&self) {
        for component in self.components.iter() {
            component.draw();
        }
    }
}
```

이것은 트레잇 바운드와 함께 제네릭 타입 파라미터를 사용하는 구조체를 정의하는 것과는 다르게 작동한다. 제네릭 타입 파라미터는 한 번에 하나의 구체 타입으로만 대입될 수 있는 반면, 트레잇 객체를 사용하면 런타임에 여러 구체 타입을 트레잇 객체에 대해 채워넣을 수 있다. 예를 들면 다음처럼 제네릭 타입과 트레잇 바운드를 사용하여 `Screen` 구조체를 정의할 수도 있을 것이다.

```rust
pub struct Screen<T: Draw> {
    pub components: Vec<T>,
}

impl<T> Screen<T>
    where T: Draw {
    pub fn run(&self) {
        for component in self.components.iter() {
            component.draw();
        }
    }
}
```

이렇게 하면 전부 `Draw`를 트레잇을 구현한 단 하나의 `Button` 타입 혹은 `TextField` 타입인 컴포넌트 리스트를 가지는 `Screen` 인스턴스로 제한된다. 동일 유형의 콜렉션만 사용한다면 제네릭과 트레잇 바운드를 사용하는 것이 더 좋은데, 왜냐하면 그 정의들은 구체 타입을 사용하기 위해 컴파일 타임에 단형성화 (monomorphize)되기 때문이다. 

반면에 트레잇 객체를 사용하는 메소드를 이용할 때는 하나의 `Screen` 인스턴스가 `Box<Button>` 혹은 `Box<TextField>`도 담을 수 있는 `Vec<T>`를 보유할 수 있다. 이것이 어떻게 동작하는지 살펴보고 런타임 성능에 미치는 영향에 대해 설명하겠다.

### 트레잇 구현하기

이제 `Draw` 트레잇을 구현하는 몇가지 타입을 추가하려고 한다. `Button` 타입을 만들어보자. 다시금 말하지만, 실제 GUI 라이브러리를 구현하는 것은 목적에 벗어나므로, `draw`에는 별다른 구현을 하지 않을 것이다. 구현하려는 것을 상상해보자면, `Button` 구조체는 다음과 같이 `width`, `height`, `label` 항목을 가지게 될 것이다.

```rust
pub struct Button {
    pub width: u32,
    pub height: u32,
    pub label: String,
}

impl Draw for Button {
    fn draw(&self) {
        // code to actually draw a button
    }
}
```

`Button`의 `width`, `height` 및 `label` 필드는 다른 컴포넌트와는 차이가 있다. `TextField` 타입을 예로 들면, 이 필드들에 추가로 `placeholder` 필드를 소유할 것이다. 화면에 그리고자 하는 각각의 타입은 `Draw` 트레잇을 구현할테지만, 해당 타입을 그리는 방법을 정의하기 위해 `draw` 메소드 내에 서로 다른 코드를 사용하게 될 것이며, `Button`이 그러한 경우다. 예를 들어, `Button` 타입은 추가적인 `impl` 블록에 사용자가 버튼을 클릭했을 때 어떤 일이 벌어질지와 관련된 메소드들을 포함할 수 있다. 이런 종류의 메소드는 `TextField`와 같은 타입에는 적용할 수 없다.

이 라이브러리를 사용하는 누군가가 `width`, `height` 및 `options` 필드가 있는 `SelectBox` 구조체를 구현하기로 했다면, 다음과 같이 `SelectBox` 타입에도 `Draw` 트레잇을 구현한다.

```rust
extern crate gui;
use gui::Draw;

struct SelectBox {
    width: u32,
    height: u32,
    options: Vec<String>,
}

impl Draw for SelectBox {
    fn draw(&self) {
        // code to actually draw a select box
    }
}
```

이 라이브러리의 사용자는 이제 `Screen` 인스턴스를 만들기 위해 `main` 함수를 구현할 수 있다. `Screen` 인스턴스에는 `SelectBox`와 `Button`이 트레잇 객체가 되도록 하기 위해 `Box<T>` 안에 넣음으로서 이들을 추가할 수 있다.그러면 `Screen` 인스턴스 상의 `run` 메소드를 호출할 수 있는데, 이는 각 컴포넌트들에 대해 `draw`를 호출할 것이다.

```rust
use gui::{Screen, Button};

fn main() {
    let screen = Screen {
        components: vec![
            Box::new(SelectBox {
                width: 75,
                height: 10,
                options: vec![
                    String::from("Yes"),
                    String::from("Maybe"),
                    String::from("No")
                ],
            }),
            Box::new(Button {
                width: 50,
                height: 10,
                label: String::from("OK"),
            }),
        ],
    };

    screen.run();
}
```

이 라이브러리를 작성할 때는, 누군가 `SelectBox` 타입을 추가할 수도 있다는 것을 알 수 없었지만, `Screen` 구현체는 새로운 타입에 대해서도 동작하고 이를 그려낼 수 있는데, 그 이유는 `SelectBox`가 `Draw`타입을 구현했기 때문이고, 이는 `draw` 메소드가 구현되어 있음을 의미한다.

이러한 개념 *(값의 구체적인 타입이 아닌 값이 응답하는 메시지 만을 고려하는 개념)*은 동적 타입 언어들의 *오리 타이핑 (duck typing)* 이란 개념과 유사하다. 만약 오리처럼 뒤뚱거리고 오리처럼 꽥꽥거리면, 그것은 오리임이 틀림없다. 앞서 `Screen`에 구현된 `run`을 보면, `run`은 각 컴포넌트가 어떤 구체적 타입인지 알 필요가 없다. 이 함수는 컴포넌트가 `Button`의 인스턴스인지, 혹은 `selectBox`의 인스턴스인지 검사하지 않고 그저 각 컴포넌트의 `draw` 메소드를 호출할 뿐이다. `components` 벡터에 담기는 값의 타입을 `Box<Draw>`로 특정함으로서 `Screen`의 정의는 `draw` 메소드를 호출할 수 있는 값을 요구한다.

오리 타이핑을 사용하는 코드와 유사한 코드를 작성하기 위해서 트레잇 객체와 러스트의 타입 시스템을 사용하는 것의 장점은. 어떤 값이 특정한 메소드를 구현했는지를 검사해야 하거나 혹은 값이 메소드를 구현하지 않았는데도 해당 메소드를 호출한다면 생길 수 있는 에러에 대한 걱정을 전혀 할 필요가 없다는 것이다. 러스트는 트레잇 객체가 요구한는 트레잇을 해당 값이 구현하지 않았다면 컴파일 타임에 문제를 일으킬 것이다.

예를 들어, 다음 예제는 `String`을 컴포넌트로하는 `Screen`을 생성하는 시도를 하면 어떤 일이 벌어지는지 보여준다.

```rust
extern crate gui;
use gui::Screen;

fn main() {
    let screen = Screen {
        components: vec![
            Box::new(String::from("Hi")),
        ],
    };

    screen.run();
}
```

`String`이 `Draw`트레잇을 구현하지 않았기 때문에, 다음과 같은 에러를 발생시킨다.

```
error[E0277]: the trait bound `std::string::String: gui::Draw` is not satisfied
  --> src/main.rs:7:13
   |
 7 |             Box::new(String::from("Hi")),
   |             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ the trait gui::Draw is not
   implemented for `std::string::String`
   |
   = note: required for the cast to the object type `gui::Draw`
```

이 에러는 넘겨서는 안될 무언가를 `Screen`에게 넘기는 중이고, 이를 다른 타입으로 교체하거나, 혹은 `String`에 대한 `Draw`를 구현해 `Screen`이 이것에 대해 `draw` 메소드를 호출할 수 있도록 해야한다는 것이다.

### 트레잇 객체는 동적 디스패치를 수행한다.

엎서 “제네릭을 사용한 코드의 성능” 절에서  제네릭에 트레잇 바운드를 사용했을 때 컴파일러에 의해 이뤄지는 단형성화 프로세스의 실행에 대한 논의를 기억해보자. 컴파일러는 제네릭 타입 파라미터를 사용한 각각의 구체타입을 위한 함수와 메소드의 제네릭 없는 구현체를 생성한다. 단형성화로부터 야기된 코드는 *정적 디스패치 (static dispatch)*를 수행하는데, 이는 호출하고자 하는 메소드가 어떤 것인지 컴파일러가 컴파일 시점에 알고 있는 것이다. 이는 *동적 디스패치 (dynamic dispatch)*와 반대되는 개념으로, 동적 디스패치는 컴파일러가 호출하는 메소드를 컴파일 시에 알 수 없을 경우 수행된다. 동적 디스패치의 경우, 컴파일러는 런타임에 어떤 메소드가 호출되는지 알아내는 코드를 생성한다.

러스트는 트레잇 객체를 사용할 때, 동적 디스패치를 이용해야 한다. 컴파일러는 트레잇 객체를 사용중인 코드와 함께 사용될 수도 있는 모든 타입을 알 수 없기 때문에, 어떤 타입에 구현된 어떤 메소드를 호출할지 알지 못한다. 대신 런타임에서, 러스트는 트레잇 객체 내에 존재하는 포인터를 사용해 어떤 메소드가 호출될지 알아낸다. 정적 디스패치시에는 일어나지 않는 이러한 탐색이 발생할 때 런타임 비용이 존재한다. 동적 디스패치는 또한 컴파일러가 메소드의 코드를 인라인 (inline) 화하는 선택을 막아버리는데, 이것이 결과적으로 몇가지 최적화를 수행하지 못하게 한다. 하지만 여기에 대한 비용을 지불한 대가로 유연성을 얻어 [위와 같은 코드](https://www.notion.so/e404bcb9e80a44a7b0fecd9103a08e6b?pvs=21)들을 작성할 수 있으니 여기에는 고려할 기회비용이 있다.

### 트레잇 객체에 대해 객체 안전성이 요구된다.

우리는 *객체-안전 (object-safe)*한 트레잇만 트레잇 객체로 만들 수 있다. 트레잇 객체를 안전하게 만드는 모든 속성들을 관장하는 몇가지 복잡한 규칙이 있지만, 실전에서는 두 가지 규칙만 관련되어 있다. 어떤 트레잇 내의 모든 메소드들이 다음과 같은 속성들을 가지고 있다면 해당 트레잇은 객체 안전하다.

- 반환값의 타입이 `Self`가 아니다.
- 제네릭 타입 매개변수가 없다.

`Self` 키워드는 트레잇 혹은 메소드를 구현하고 있는 타입의 별칭이다. 트레잇 객체가 반드시 객체 안전해야 하는 이유는 일단 트레잇 객체를 사용하면, 러스트가 트레잇에 구현된 구체(concrete) 타입을 알 수 없기 때문이다. 만약 트레잇 메소드가 고정된 `Self` 타입을 반환하는데 트레잇 객체는 `Self`의 정확한 타입을 잊었다면, 메소드가 원래 구체 타입을 사용할 수 있는 방법이 없다. 트레잇을 사용할 때 구체 타입 파라미터로 채워지는 제네릭 타입 파리미터도 마찬가지다. 그 구체 타입들은 해당 트레잇을 구현하는 타입의 일부가 된다. 트레잇 객체를 사용을 통해 해당 타입을 잊게 되면, 제네릭 타입 파라미터를 채울 타입을 알 수 없다.

메소드가 객체 안전하지 않은 트레잇의 예는 표준 라이브러리의 `Clone` 트레잇이다. `Clone` 트레잇의 `clone` 메소드에 대한 시그니처는 다음과 같다.

```rust
pub trait Clone {
    fn clone(&self) -> Self;
}
```

`String` 타입은 `Clone` 트레잇을 구현하고, `String` 인스턴스에 대해 `clone` 메소드를 호출하면 우리는 `String`의 인스턴스를 반환받을 수 있다. 비슷하게, `Vec<T>`의 인스턴스 상의 `clone`을 호출하면, `Vec<T>`인스턴스를 얻을 수 있다. `clone` 선언은 `Self`에 어떤 타입이 사용되는지 알 필요가 있는데, 왜냐하면 그게 반환 타입이기 때문이다.

컴파일러는 우리가 트레잇 객체와 관련해 객체 안전성 규칙을 위반하는 무언가를 시도하려고 하면 알려준다. 예를 들어, 위에서 Screen 구조체가 `Draw` 트레잇 대신 `clone` 트레잇을 구현하는 타입을 보관하도록 아래처럼 구현해보자.

```rust
pub struct Screen {
    pub components: Vec<Box<Clone>>,
}
```

다음과 같은 에러를 얻게 된다.

```
error[E0038]: the trait `std::clone::Clone` cannot be made into an object
 --> src/lib.rs:2:5
  |
2 |     pub components: Vec<Box<Clone>>,
  |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ the trait `std::clone::Clone` cannot be
made into an object
  |
  = note: the trait cannot require that `Self : Sized`
```

이 에러가 의미하는 바는 이러한 방식으로 트레잇을 트레잇 객체로서 사용할 수 없다는 것이다.

---

## 객체 지향 디자인 패턴 구현하기

*상태 패턴 (state pattern)*은 객체 지향 디자인 패턴이다. 이 패턴의 핵심은 어떤 값이 *상태 객체들*의 집합으로 표현되는 일종의 내부 상태를 가지며, 이 값의 동작은 내부 상태에 기반하여 바뀐다는 것이다. 상태 객체들은 기능을 공유한다. 당연히 러스트에서는 객체와 상속보다는 구조체와 트레잇을 사용한다. 각 상태 객체는 자신의 동작 및 다른 상태로 변경되어야 할 때의 제어에 대한 책임이 있다. 상태 객체를 보유한 값은 상태들의 서로 다른 행동 혹은 상태 간의 전이가 이루어지는 때에 아무것도 알지 못한다.

상태 패턴을 사용한다는 것은 프로그램의 사업적 요구사항들이 변경될 때, 상태를 보유한 값의 코드 혹은 그 값을 사용하는 코드는 변경될 필요가 없음을 의미한다. 단지 상태 객체 중에 하나의 내부 코드를 갱신함으로서 그 규칙을 바꾸거나 혹은 상태 객체를 더 추가할 필요가 있을 따름이다. 상태 디자인 패턴 예제를 살펴보고 이를 러스트에서 사용하는 방법에 대해서 알아보자.

예시로, 점진적인 방식으로 블로그에 게시물을 올리는 작업 흐름을 구현해보자. 블로그의 최종적인 기능은 다음과 같다.

1. 블로그 게시물은 빈 초안으로 시작한다.
2. 초안이 완료되면 게시물의 검토가 요청된다.
3. 게시물이 승인되면 게시된다.
4. 오직 게시된 블로그 게시물만이 출력될 내용물을 반환하므로, 승인되지 않은 게시물은 실수로라도 게시될 수 없다.

이 외에 게시물에 시도되는 어떠한 변경사항도 영향을 미쳐서는 안된다. 예를 들어, 만약 검토를 요청하기도 전에 블로그 게시물 초안을 승인하려는 시도를 했다면, 그 게시물은 비공개 초안인 상태로 유지되어야 한다.

다음은 코드의 형태로 이 작업 흐름을 보여준다. 여기서 구현할 라이브러리 크레이트 `blog`의 API를 사용하는 예제다. 아직 컴파일되지 않는 이유는 `blog` 크레이트를 구현하지 않았기 때문이다.

```rust
extern crate blog;
use blog::Post;

fn main() {
    let mut post = Post::new();

    post.add_text("I ate a salad for lunch today");
    assert_eq!("", post.content());

    post.request_review();
    assert_eq!("", post.content());

    post.approve();
    assert_eq!("I ate a salad for lunch today", post.content());
}
```

사용자가 새로운 블로그 게시물의 초안을 `Post::new`를 통해 만들 수 있도록 허용하고 싶다. 이후에는 블로그 게시물에 초안인 상태로 글을 추가할 수 있도록 하고자 한다. 만약 게시물의 내용을 승인 전에 즉시 얻어오는 시도를 하면, 해당 게시물이 아직 초안이기 때문에 아무일도 일어나지 않아야 한다. 이를 보여주는 용도로 코드 내에 `assert_eq!`를 추가했다. 이를 위한 훌륭한 단위 테스트는 블로그 게시물 초안이 `content` 메소드에 대해 빈 문자열을 반환하는 것이겠지만, 이 예제를 위한 테스트를 구현하지는 않을 것이다.

다음으로, 게시물의 리뷰를 요청하고, 리뷰를 기다리는 동안에도 `content`가 빈 문자열을 반환하도록 하고 싶다. 게시물이 허가를 받은 시점에는 게시가 되어야 하는데, 이는 `content`의 호출되었을 때 게시물의 글이 반환될 것이다.

이 크레이트로부터 상호작용하고 있는 유일한 타입이 `Post` 타입임을 기억하자. 이 타입은 상태 패턴을 사용하며 게시물이 가질 수 있는 초안, 리뷰 대기중, 게시됨을 나타내는 세가지 상태 중 하나가 될 값을 보유할 것이다. 어떤 상태에서 다른 상태로 변경되는 것은 `Post` 타입 내에서 내부적으로 관리된다. 이 상태들은 라이브러리의 사용자가 `Post` 인스턴스 상에서 호출되는 메소드에 응답해 변경되거나, 상태 변화를 직접관리할 필요는 없다. 또한 사용자는 리뷰 전에 게시물이 게시되는 것 같은 상태와 관련된 실수를 할 수 없다.

### Post를 정의하고 초안 상태의 새 인스턴스 생성하기

라이브러리를 구현해보자. 어떤 내용물을 담고 있는 공개된 `Post` 구조체가 필요하다. 따라서 다음 코드와 같이 `Post`의 인스턴스를 만들기 위해서 구조체 및 관련된 공개 `new` 함수의 정의로 시작을 할 것이다. 비공개 `State` ㄷ트레잇 또한 만들어준다. 그 다음 `Post`는 비공개 필드 `state`에 `Option`으로 감싸진 `Box<State>` 형태의 트레잇 객체를 보유할 것이다. 이후 왜 `Option`이 필요한지 알게 될 것이다.

```rust
pub struct Post {
    state: Option<Box<State>>,
    content: String,
}

impl Post {
    pub fn new() -> Post {
        Post {
            state: Some(Box::new(Draft {})),
            content: String::new(),
        }
    }
}

trait State {}

struct Draft {}

impl State for Draft {}
```

`State` 트레잇은 게시물의 상태 변화에 따라 달라지는 동작을 정의하고, `Draft`, `PendingReview`, 그리고 `Published` 상태는 모두 `State` 트레잇을 구현하게 된다. 지금은 트레잇이 아무런 메소드도 갖지 않고, 그저 `Draft` 상태의 구현부터 시작하려고 하는데, 왜냐면 그것이 게시물이 최초로 갖는 상태가 될 것이다.

새로운 `Post`를 생성할 때, 이것의 `state` 필드에 `Box`를 보유한 `Some`값을 설정한다. 이 `Box`는 `Draft`구조체의 새 인스턴스를 가리킨다. 이는 언제 `Post`를 생성하던지, 초안으로 시작하는 것을 보장한다. `Post`의 `state` 필드가 비공개이기 때문에, 다른 상태로 `Post`를 생성할 방법은 없다. `Post::new` 함수에서는 `content` 필드를 새로운, 빈 String으로 설정한다.

### 게시물 콘텐츠의 글을 저장하기

[앞서 본 코드](https://www.notion.so/9aab88bdd3b54a56bdf3cb608dcce216?pvs=21)에서는 `add_text`로 명명된 메소드를 호출하고 여기에 `&str`을 넘겨 블로그 게시물의 콘텐츠 글에 추가할 수 있도록 했다. `content` 필드를 `pub`으로 노출시키는 것보다는 메소드로서 이를 구현할 것이다. 이는 `content` 필드의 데이터를 읽는 방식을 제어할 수 있는 메소드를 나중에 구현할 수 있음을 뜻한다. `add_text` 메소드는 매우 직관적이니까, `impl Post` 블록에 구현을 추가해보자.

```rust
impl Post {
    // --snip--
    pub fn add_text(&mut self, text: &str) {
        self.content.push_str(text);
    }
}
```

`add_text` 메소드는 가변 참조자 `self`를 취하는데, 그 이유는 우리가 `add_text`를 호출하고 있는 해당 `Post` 인스턴스를 변경하게 되기 때문이다. 그런 다음 `content`의 `String` 상에서 `push_str`을 호출하고 `text`를 인자로 전달해 저장된 `content`에 추가한다. 이 동작은 게시물의 상태와 무관하게 이뤄지므로, 상태 패턴의 일부가 아니다. `add_text` 메소드는 `state` 필드와 전혀 상호작용하지 않지만, 우리가 의도한 동작 요소다.

### 초안 게시물의 내용이 비어있음을 보장하기

`add_text`를 호출하고 게시물에 어떤 콘텐츠를 추가한 이후일지라도, 여전히 `content` 메소드가 빈 스트링 슬라이스를 반환하길 원한다. 그 이유는 [앞선 코드](https://www.notion.so/9aab88bdd3b54a56bdf3cb608dcce216?pvs=21)의 8번째 줄처럼 게시물이 여전히 초안 상태이기 때문이다. 당장은 이 요건을 만족할 가장 단순한 것으로 `content` 메소드를 구현해 놓으려고 한다. 언제나 빈 스트링 슬라이스를 반환하는 것이다. 게시물의 상태를 변경해 이것이 게시될 수 있도록 하는 기능을 구현하게 되면 그 후에 이 메소드를 변경하자. 그 때까지 게시물은 오직 초안 상태로만 존재하기에 게시물 컨텐츠는 항상 비어 있어야 한다

```rust
impl Post {
    // --snip--
    pub fn content(&self) -> &str {
        ""
    }
}
```

### 게시물에 대한 리뷰 요청이 그의 상태를 변경한다.

다음으로, 게시물의 리뷰를 요청하는 기능을 만들어야 하는데, 이는 게시물의 상태를 `Draft`에서 `PendingReview`로 변경해야 한다.

```rust
impl Post {
    // --snip--
    pub fn request_review(&mut self) {
        if let Some(s) = self.state.take() {
            self.state = Some(s.request_review())
        }
    }
}

trait State {
    fn request_review(self: Box<Self>) -> Box<State>;
}

struct Draft {}

impl State for Draft {
    fn request_review(self: Box<Self>) -> Box<State> {
        Box::new(PendingReview {})
    }
}

struct PendingReview {}

impl State for PendingReview {
    fn request_review(self: Box<Self>) -> Box<State> {
        self
    }
}
```

`Post`에게 `self`에 대한 가변 참조자를 취하려는 `request_review`란 이름의 공개 메소드를 만들었다. 그다음 `Post`의 현재 상태 상에서 내부 메소드 `request_review`를 호출하고, 이 두번째 `request_review` 메소드는 현재 상태를 소비하고 새로운 상태를 반환한다.

`State` 트레잇에 `request_review` 메소드를 추가했다. 트레잇을 구현하는 모든 타입은 이제 `request_review` 메소드를 구현할 필요가 있다. 주목할 점은 메소드의 첫 인자를 `self`, `&self`, 나 `&mut self`를 취하기 보다 `self: Box<Self>` 를 취한다는 것이다. 이 문법은 메소드가 오직 그 타입을 보유한 `Box` 상에서 호출될 경우에만 유효함을 뜻한다. 해당 문법은 `Box<Self>`의 소유권을 가져가는데, `Post`의 예전 상태를 무효화하여 새 상태로 변화하게 해준다. 

이전 상태를 소비하기 위해서 `request_review` 메소드는 상태 값의 소유권을 취할 필요가 있다. 이것이 `Post`의 `state` 필드 내 `Option`이 들어온 까닭이다. `take` 메소드를 호출해 `state` 필드 밖으로 `Some` 값을 빼내고 그 자리에 `None`을 남기는데, 왜냐면 러스트 구조체 내에 값이 없는 필드를 허용하지 않기 때문이다. 이는 `state` 값을 빌리기 보다는 게시물 밖으로 이동시키도록 만든다. 이후 게시물의 `state` 값을 이런 연산의 결과물로 설정하려 한다.

`state` 값의 소유권을 얻기 위해서 `self.state = self.state.request_review();` 처럼 직접 설정하는것 보다는 `state`를 임시로 `None`으로 설정할 필요가 있다. 이는 `Post`가 예전 `state` 값을 새 상태로 변경시킨 뒤에는 사용할 수 없음을 보장한다. 

`Draft`의 `request_review` 메소드는 새 박스로 포장된 `PendingReview` 구조체의 새 인스턴스를 반환해야 하며, 이는 게시물이 리뷰를 기다리고 있다는 상태를 표현한다. `PendingReview` 구조체 또한 `request_review` 메소드를 구현하지만 어떤 변경도 하지 않았다. 그보다 이 구조체는 자기 자신을 반환하는데, 그 이유는 이미 `PendingReview` 상태인 게시물에 대한 리뷰를 요청할 때는 `PendingReview` 상태를 그대로 유지해야 하기 때문이다.

이제 상태 패턴의 장점을 알아보기 시작할 수 있다. `Post`의 `request_review` 메소드는 그것의 `state`가 무엇이든 상관없다. 각 상태는 그 자신의 규칙에 따라 맡은 책임을 다할 것이다.

`Post`의 `content` 메소드가 여전히 빈 스트링 슬라이스를 반환하도록 그대로 놔두려고 한다. 현재 `Draft` 상태에 있는 `Post` 뿐만 아니라 `PendingReview` 상태에 있는 `Post`를 소유할 수 있지만, `PendingReview` 상태에서도 동일하게 동작하도록 원한다.

### content의 동작을 변경하는 approve 메소드 추가하기

`approve` 메소드는 `request_review` 메소드와 유사하다. 다음 코드와 같이 상태가 허가되었을 때 가져야 하는 값으로 `state`를 설정할 것이다.

```rust
impl Post {
    // --snip--
    pub fn approve(&mut self) {
        if let Some(s) = self.state.take() {
            self.state = Some(s.approve())
        }
    }
}

trait State {
    fn request_review(self: Box<Self>) -> Box<State>;
    fn approve(self: Box<Self>) -> Box<State>;
}

struct Draft {}

impl State for Draft {
    // --snip--
    fn approve(self: Box<Self>) -> Box<State> {
        self
    }
}

struct PendingReview {}

impl State for PendingReview {
    // --snip--
    fn approve(self: Box<Self>) -> Box<State> {
        Box::new(Published {})
    }
}

struct Published {}

impl State for Published {
    fn request_review(self: Box<Self>) -> Box<State> {
        self
    }

    fn approve(self: Box<Self>) -> Box<State> {
        self
    }
}
```

`apporve` 메소드를 `State` 트레잇에 추가했고 `State`를 구현하는 새 구조체 `Published` 상태도 추가했다. 

`request_review`와 유사하게, `Draft`의 `approve` 메소드를 호출하면, 이는 별 효과가 없는데 이유는 이때 반환되는 것이 `self`이기 때문이다. `PendingReview` 상에서 `approve`를 호출하면, 박스로 포장된 `Published` 구조체의 새 인스턴스가 반환된다. `Published` 구조체는 `State` 트레잇을 구현하고, `request_review`와 `approve` 메소드 양 족 모두에서 자기 자신을 반환하는데, 이러한 경우에는 그 게시물이 `Published` 상태를 유지해야 하기 때문이다.

이제 해야할 일은 `Post` 의 `content` 메소드를 갱신하는 것이다. 다음 코드처럼 만일 상태가 `Published`라면, `content` 필드의 값을 반환할 것이다. 그렇지 않다면 빈 스트링 슬라이스를 반환하고자 한다.

```rust
impl Post {
    // --snip--
    pub fn content(&self) -> &str {
        self.state.as_ref().unwrap().content(&self)
    }
    // --snip--
}
```

목표하는 바가 `State`를 구현하는 구조체들 내에서 이 모든 규칙을 유지하는 것이기 때문에, `state`의 값에 `content` 메소드를 호출하면서 게시물 인스턴스 (`self`)를 인자로 넘긴다. 그러면 `state`의 `content` 메소드를 사용해 반환되는 값을 받게 된다.

`Option`의 `as_ref` 메소드를 호출하는데 `Option` 내의 값에 대한 소유권을 가져오기 보다는 그에 대한 참조자를 원하기 때문이다. `state`는 `Option<Box<State>>`이므로, `as_ref`를 호출하면 `Option<&Box<State>>`가 반환된다. `as_ref`를 호출하지 않는다면, 해당 함수 파라미터의 빌려온 `&self`로부터 `state`를 이동시킬 수 없기 때문에 에러를 얻게 될 것이다.

그런 다음 `unwrap` 메소드를 호출하고 이것이 패닉을 발생싴키지 않을 것을 알고 있다. 그 이유는 `Post`의 메소드들은 이들이 실행 완료 되었을 때 `state`가 항상 `Some` 값을 담고 있을 것을 보장하기 때문이다.

이 지점에서 `&Box<State>`의 `content`를 호출할 때, 역참조 강제는 `&`와 `Box`에 영향을 줘서 `content` 메소드가 궁극적으로 `State` 트레잇을 구현하는 타입 상에서 호출되도록 한다. 이는 `State` 트레잇 정의에 `content`를 추가할 필요가 있음을 의미하고, 다음코드 처럼 가지고 있는 모든 상태에 따라 어떤 컨텐츠를 반환할지에 대한 로직을 삽입할 차례다.

```rust
trait State {
    // --snip--
    fn content<'a>(&self, post: &'a Post) -> &'a str {
        ""
    }
}

// --snip--
struct Published {}

impl State for Published {
    // --snip--
    fn content<'a>(&self, post: &'a Post) -> &'a str {
        &post.content
    }
}
```

빈 스트링 슬라이스를 반환하는 `content` 메소드의 기본 구현을 추가한다. 이는 `Draft`와 `PendingReview` 구조체에 대한 `content`는 따로 구현할 필요가 없다. `Published` 구조체는 `content` 메소드를 오버라이딩하고 `post.content` 값을 반환할 것이다.

앞서 배운 것처럼 이 메소드에 대한 라이프타임 명시가 필요함을 기억하자. `post`에 대한 참조자를 인자로 취하고 있고 해당 `post`의 일부에 대한 참조를 반환하는 중이므로, 반환되는 참조자의 라이프타임은 `post` 인자의 라이프타임과 관련이 있다. 

### 상태 패턴의 기회비용

게시물이 각 상태에 대해 가져야 하는 서로 다른 종류의 동작을 캡슐화하기 위해서 러스트로 객체 지향 상태 패턴을 충분히 구현할 수 있음을 보여주었다. `Post`의 각 메소드는 이런 다양한 동작에 대해서 알지 못한다. 코드를 구조화한 방식에 따라, 게시된 게시물이 취할 수 있는 서로 다른 방법을 알기 위해서는 단 한 곳, `Published`구조체의 `State` 트레잇에 구현된 내용만 보면 된다. 

만약 상태 패턴을 사용하지 않고 다른 방식으로 구현했다면, `Post` 혹은 `main` 코드에서 `match` 표현식을 대신 사용해 게시물의 상태를 검사하고 이에 따라 해야 할 행동을 변경해야 할수도 있다. 이는 게시된 상태의 게시물의 모든 결과들에 대해 이해하기 위해서 여러 곳을 봐야 한다는 것을 의미한다. 이는 상태를 추가하는 것을 더 늘게 할 뿐이다. 또한 각각의 `match` 표현식은 또 다른 갈래를 필요로 할 것이다.

상태 패턴을 이용하면 `Post` 메소드들과 `Post`를 사용하는 곳에서는 `match` 표현식을 사용할 필요가 없고, 새로운 상태를 추가하려면, 그저 새로운 구조체와 구조체에 대한 트레잇 메소드들을 구현하면 된다.

상태 패턴을 사용하면 추가 기능을 구현하기 쉽다. 상태 패턴을 사용하는 코드를 유지하는 것의 단순성을 체험해보려면 다음을 시도해보자.

- `reject` 메소드를 추가해 게시물의 상태를 `PendingReview`에서 `Draft`로 변경하기
- `Published`로 상태 변경이 가능해지기 전에 `approve`가 두 번 호출되도록 요구하기
- 게시물이 `Draft` 상태일 때는 사용자들에게 글 내용의 추가만 허용하기. (상태 객체가 내용에 관한 변경에는 역할을 하지만 `Post`를 수정하기 위한 역할은 하지 않도록 하기)

상태 패턴의 단점 중에 하나는, 상태가 상태 간의 전환을 구현하기 때문에, 몇몇 상태들이 서로 묶이게 된다는 점이다. 만약 `PendingReview`와 `Published` 사이에 `Scheduled`와 같은 상태를 추가하면, `PendingReview`에서 `Scheduled`로 전환되도록 코드를 변경해야 한다. 새로운 상태의 추가와 함께 PendingReview가 변경될 필요가 없었다면 좀 더 작은 작업이 될 테지만, 이는 다른 디자인 패턴으로의 전환을 의미할 것이다.

또 다른 단점은 몇몇 로직을 중복시킨다는 점이다. 중복의 일부를 제거하려면, `State` 트레잇의 `request_review`와 `approve` 메소드가 `self`를 반환하도록 기본 구현을 만드는 시도를 할 수 있다. 하지만, 이는 객체 안정성을 위배할 수 있는데, 그 이유는 해당 트레잇이 어떤 구체적인 `self`가 될 것인지 알지 못하기 때문이다. `State`를 트레잇 객체로서 사용하길 원하기에, 이것의 메소드들은 객체 안정성을 지킬 필요가 있다.

`Post`에 메소드 `request_review`와 `approve`처럼 유사한 구현들도 그 밖의 중복에 포함된다. 두 메소드 모두 `Option`의 `state` 필드 값에 대해 동일한 메소드의 구현을 대행하며, 그 결과값을 `state` 필드의 새 값으로 설정한다. 이 패턴을 따르는 `Post`의 메소드를 많이 갖게 되면, 이러한 반복을 제거하기 위해 매크로의 정의를 고려할 수도 있다. (매크로에 대한 자세한 내용은 이후에 다룬다)

객체 지향 언어에서의 정의하는 것과 동일하게 상태 패턴을 구현함으로써, 러스트의 강점을 모두 이용하지 못하고 있다. 유효하지 않은 상태 및 전환이 컴파일 타임 에러가 될 수 있도록 하기 위해 할수 있는 `blog` crate에 대한 변경 및가지를 살펴보자.

### 상태와 동작을 타입처럼 인코딩하기

이 방법은 다른 기회 비용을 얻기 위해 상태 패턴을 재고하는 방법을 보여준다. 오히려 상태와 전환을 완전히 캡슐화하여 외부의 코드들이 이를 알지 못하는 것보다는, 상태를 다른 타입들로 인코딩하려 한다. 결론적으로, 러스트의 타입 검사 시스템은 게시된 게시물만 허용되는 곳에서 게시물 초안을 사용하려는 시도에 대해 컴파일 에러를 발생시킴으로서 방지할 것이다. 

```rust
fn main() {
    let mut post = Post::new();

    post.add_text("I ate a salad for lunch today");
    assert_eq!("", post.content());
}
```

`Post::new`를 사용해 초안 상태의 새 게시물을 생성할 수 있도록 하며 게시물의 내용에 새 글을 추가할 수 있는 기능을 작성한다. 하지만 빈 문자열을 반환하는 초안 게시물의 `content` 메소드 대신, 초안 게시물이 `content` 메소드를 갖지 않도록 만들려고 한다. 이렇게 하면, 초안 게시물의 내용을 얻는 시도를 할 경우 해당 메소드가 존재하지 않는다는 컴파일 에러를 얻게 될 것이다. 결과적으로, 의도치 않게 제작 중인 초안 게시물의 내용을 얻게 되는 일이 불가능하게 된다. 다음 코드는 `Post` 구조체와 `DraftPost` 구조체의 정의와 각각의 메소드를 보여준다.

```rust
pub struct Post {
    content: String,
}

pub struct DraftPost {
    content: String,
}

impl Post {
    pub fn new() -> DraftPost {
        DraftPost {
            content: String::new(),
        }
    }

    pub fn content(&self) -> &str {
        &self.content
    }
}

impl DraftPost {
    pub fn add_text(&mut self, text: &str) {
        self.content.push_str(text);
    }
}
```

`Post`와 `DraftPost` 구조체 모두 비공개인 `content` 필드를 가지고 블로그 게시물의 글을 보관한다. 이 구조체들이 더 이상 `state` 필드를 갖지 않는 이유는 상태의 인코딩을 구조체 타입으로 이동시켰기 때문이다. `Post` 구조체는 공개된 게시물을 나타낼 것이고, 그의 `content` 메소드는 `content`를 반환할 것이다.

여전히 `Post::new` 함수를 유지하지만, `Post`의 인스턴스를 반환하는 대신 `DraftPost`를 반환한다. `content`는 비공개이고 `Post`를 반환할 어떤 함수도 존재하지 않기 때문에, 당장 `Post`의 인스턴스를 생성하는 것은 불가능하다.

`DraftPost` 구조체는 `add_text` 메소드를 가지고 있으므로, 전처럼 `content`에 글을 추가할 수 있지만, `DraftPost`는 정의된 `content` 메소드가 없음을 주의하자. 따라서 이제 프로그램은 모든 게시물이 초안 게시물로 시작되고, 초안 게시물들은 그들의 내용을 출력할 능력이 없음을 보장환다. 이 제약사항을 벗어나는 어떤 시도라도 컴파일 에러로 끝나게 될 것이다.

### 다른 타입으로 변환하는 것처럼 전환 구현하기

그렇다면 어떻게 게시된 게시물을 얻을 수 있을까? 초안 게시물이 공개되기 전에 리뷰와 승인을 받도록 강제하고 싶다. 리뷰를 기다리는 상태인 게시물은 여전히 어떤 내용도 보여줘서는 안된다. 다음 코드처럼 새 구조체 `PendingReviewPost`를 추가하고, `DraftPost`에 `PendingReviewPost`를 반환하는 `request_review` 메소드를 정의하고, `PendingReviewPost`에 `Post`를 반환하는 `approve` 메소드를 정의해 위의 제약사항들을 구현해보자.

```rust
impl DraftPost {
    // --snip--

    pub fn request_review(self) -> PendingReviewPost {
        PendingReviewPost {
            content: self.content,
        }
    }
}

pub struct PendingReviewPost {
    content: String,
}

impl PendingReviewPost {
    pub fn approve(self) -> Post {
        Post {
            content: self.content,
        }
    }
}
```

`request_review`와 `approve` 메소드는 `self` 소유권을 취하므로, `DraftPost`와 `PendingReviewPost`의 인스턴스를 소비해 이들을 각각 `PendingReviewPost`와 `Post`로 변환시킨다. 이 방식으로 `request_review`를 호출한 후 등등에는 `DraftPost` 인스턴스를 질질 끌지 않게될 것이다. `PendingReviewPost` 구조체는 `content` 메소드정의를 갖지 않기 때문에, 그의 내용물을 읽으려는 시도는 `DraftPost`와 마찬가지로 컴파일 에러를 발생시킨다. `content` 메소드를 정의하고 있는 게시된 `Post` 인스턴스를 얻을 수 있는 유일한 방법은 `PendingReviewPost`의 `approve`를 호출하는 것이고, `PendingReviewPost`를 얻을 수 있는 유일한 방법은 `DraftPost`의 `request_review`를 호출하는 것이기에, 이제 블로그 게시물의 작업 흐름을 타입 시스템으로 인코딩했다.

그뿐 아니라 `main`에 약간의 변화를 줘야 한다. `request_review`와 `approve` 메소드는 호출되고 있는 구조체를 변경하기 보다는 새 인스턴스를 반환하기 때문에, 반환되는 인스턴스를 보관하기 위해 더 많은 `let post =` 를 추가할 필요가 있다. 또한 초안과 리뷰 중인 게시물의 내용이 빈 문자열이라고 단언할 수도 없고, 단언할 필요도 없다. 

```rust
extern crate blog;
use blog::Post;

fn main() {
    let mut post = Post::new();

    post.add_text("I ate a salad for lunch today");

    let post = post.request_review();

    let post = post.approve();

    assert_eq!("I ate a salad for lunch today", post.content());
}
```

`main`에서 `post`의 다시 대입하기 위해 필요한 이 변경사항은 즉 이 구현이 더이상 객체 지향 상태 패턴을 잘 따르지 않는다는 것을 의미한다. 상태간의 변환은 더 이상 `Post`의 구현체 내에 모두 캡슐화 되지 않는다. 하지만, 얻은 것은 타입 시스템과 컴파일 타임에 일어나는 타입 검사 때문에 유효하지 않은 상태가 이제 불가능해진다는 것이다. 이는 게시되지 않은 게시물의 내용이 보여진다거나 하는 특정 버그들이  제품화가 되기 전에 발견될 것임을 보장한다.

러스트가 객체 지향 디자인 패턴을 사용할 수 있을지라도, 상태를 타입 시스템으로 인코딩하는 다른 패턴 또한 러스트 내에서 가능함을 봤다. 이 패턴들은 서로 다른 기회비용을 가지고 있다. 객체 지향 패턴에 매우 익숙할런지 몰라도, 몇몇 버그를 컴파일 타임에 방지하는 등 러스트의 기능들이 제공할 수 있는 이점들을 이용하기 위해서는 문제를 다시 생각해보자. 객체 지향 패턴은 러스트 내에서 제공하는 소유권과 같이 객체 지향 언어에서는 갖지 못한 기능들 때문에 늘 최고의 해결책이 되지는 못한다.

## 정리

러스트가 객체 지향 언어라고 생각하든 아니든, 이제 트레잇 객체를 사용해 몇가지 객체 지향 기능을 러스트 내에서 사용할 수 있다는 것을 알게 되었다. 동적 디스패치는 약간의 실행 성능과 맞바꿔 코드에 유연성을 줄 수 있다. 이 유연성을 사용해 코드 관리를 도와줄 수 있는 객체 지향 패턴을 구현할 수 있다. 러스트는 또한 소유권과 같은 객체 지향 언어들에는 없는 기능들도 갖고 있다. 객체 지향 패턴이 항상 러스트의 강점을 이용하는 최고의 방법은 아니겠지만 선택 가능한 옵션이다.

---

# 패턴과 매칭

패턴은 단순하거나 복잡한 타입의 구조에 값들을 비교하기 위한 러스트의 특별한 문법이다. 패턴을 `match` 표현 및 다른 구문들과 함께 사용하면 프로그램 흐름을 더 많이 제어할 수 있다. 패턴은 다음의 조합으로 이루어진다.

- 리터럴 값(Literals)
- 분해한 비열(Array), 열거형(Enum), 구조체(Struct), 튜플(Tuple)
- 변수(Variable)
- 와일드카드(Wildcard)
- 임시 값(Placeholders)

이들은 프로그램이 처리 할 자료들의 형태를 나타낸다. 자료들을 이 구조들에 대응시키고, 대응 시킨 자료를 값들과 비교해 특정 구간의 코드가 실행 될 수 있는지 판단할 수 있게 된다.

패턴을 이용하기 위해서는 그 패턴을 어떠한 값에 비교해야 한다. 만일 패턴이 값에 대응된다면 그 값에 해당되는 부분을 코드상에서 이용하게 된다. 앞서 자주 사용한 `match` 표현 예제들을 보면 값이 패턴의 형태에 들어맞는다면 패턴이 정한 이름들로 값들을 이용할 수 있다. 형태가 다르다면 해당 패턴과 관련된 코드는 실행되지 않는다.

이번엔 패턴을 사용할 수 있는지와, *반증 기능 패턴(refutable patterns)*과 *반중 불가 패턴(irrefutable patterns)*의 차이, 접해볼 수 있는 다양한 종류의 패턴 문법에 대해서 다룬다.  

---

## 패턴이 사용될 수 있는 모든 곳

### match 갈래

앞서 다루었듯, 패턴은 `match` 표현의 각 갈래에 사용된다. `match` 표현은 `match` 키워드, 대응 시킬 값, 갈래들로 이루어지고, 각 갈래는 갈래를 나타내는 패턴, 해당 패턴에 값이 대응 될때 실행할 표현을 구성된다. 결국 다음과 같은 구조를 가진다.

```rust
match 값 {
    패턴 => 표현,
    패턴 => 표현,
    패턴 => 표현,
}
```

`match` 표현을 사용하기 위해 지켜야할 사항이 있다. `match` 표현에 대응시킬 값이 가질 수 있는 모든 경우의 수를 빠짐 없이 표현해야 한다. 이 조건을 보장하는 하나의 방법은 `match`의 마지막 갈래에 모든 경우에 대응되는 패턴을 이용하는 것이다. 예를 들자면 아무 값에나 대응될 변수명 하나를 놓는 패턴이다. 이 경우 주어진 그 어떠한 값도 해당 변수에 묶이게 되어 실패할 수 없게 되고, 즉 가능한 모든 경우를 표현하게 된다.

`_` 라는 특별한 패턴은 아무 값에나 대응되지만 그 어떠한 변수에도 묶이지 않는다. 그래서 `match`의 마지막 갈래에 종종 쓰인다. `_` 패턴은 명시 하지 않은 값들을 무시할 때 유용하다. 이 특별한 패턴은 이후 뒤에서 “패턴에서 값을 무시하기”에서 자세히 다룬다.

### if let 조건 표현

주로 갈래가 하나 밖에 없는 `match` 표현을 더 짧게 표현하는 방법으로 사용돼었다. 추가적으로 `if let`은 `else`절을 가질 수 있다. 

다음 코드는 `if let`, `else if`, `else if let` 표현을 섞어서 사용하였다. 이들을 이용하면 여러 패턴에 하나의 값밖에 대응시키지 못하는 `match` 표현보다 더 유연하게 표현 가능하다. 또 연속된 `if let`, `else if`, `else if let`의 각 조건이 꼭 연관될 필요도 없다.

이 코드는 배경 색이 어떤 색이 될지 정하기 위해 여러개의 조건을 연이어 체크하는 것을 보여준다. 유저가 좋아하는 색을 지정할 경우 그 색이 배경 색이 된다. 만일 화요일이라면 배경은 녹색으로, 만약 나이를 스트링으로 입력했고, 이를 성공적으로 숫자로 파싱해낼 수 있다면 숫자 값에 따라 보라나 주황으로 배경색이 정해진다. 그 어떤 조건에도 만족하지 않는다면 파란색이 된다.

```rust
fn main() {
    let favorite_color: Option<&str> = None;
    let is_tuesday = false;
    let age: Result<u8, _> = "34".parse();

    if let Some(color) = favorite_color {
        println!("Using your favorite color, {}, as the background", color);
    } else if is_tuesday {
        println!("Tuesday is green day!");
    } else if let Ok(age) = age {
        if age > 30 {
            println!("Using purple as the background color");
        } else {
            println!("Using orange as the background color");
        }
    } else {
        println!("Using blue as the background color");
    }
}
```

보다시피 `if let` 표현은 `match`의 각 갈래가 그랬듯 변수를 가리는 변수를 새로이 등장 시킬 수 있다. `if let Ok(age) = age`는 `Ok`에서 값을 추출한 새로운 `age` 변수가 전의 `age`를 가리게 된다. 이는 `if age > 30` 을 조건절 밑의 새로운 스코프에서 사용해야 함을 의미한다. 새로운 `age`는 새로운 중괄호가 나타나 새로운 스코프가 시작하기 전까지 유효하지 않으므로, 조건절 밑의 스코프 안에서 사용해야 한다. `if let Ok(age) = age && age > 30` 처럼 두 조건을 합쳐서 하나의 절에서 사용 할 수 없다.

`if let` 표현의 단점은 `match` 표현과 다르게 컴파일러가 해당 구문이 모든 경우를 다뤘는지 판단하지 않는다는 점이다. 마지막에 `else` 절을 빼먹어서 처리 되지 않는 경우가 생기더라도 컴파일러가 이를 찾아내기 않고 이에 따라 발생할 수 있는 논리 버그를 사전에 경고해주지 않는다.

### while let 조건 루프

`if let` 과 구조적으로 비슷하게 생긴 `while let` 조건 루프는 주어진 값이 패턴에 계속 대응되는한 실행되는 `while` 루프다. 다음 코드는 `vector`를 스택처럼 이용해 `push`된 역순으로 값을 출력하는 `while let` 조건 루프다.

```rust
let mut stack = Vec::new();

stack.push(1);
stack.push(2);
stack.push(3);

while let Some(top) = stack.pop() {
    println!("{}", top);
}
```

이 예제는 3, 2, 1을 출력할 것이다. `pop` 메소드는 `vector`의 마지막 값을 꺼내서 `Some(value)`를 반환한다. 만일 `vector`가 비었을 경우 `None`을 반환한다. 코드의 `whlie` 루프는 `pop`이 `Some`을 반환 할 동안 실행된다. `None`이 반환되는 경우에 더 이상 패턴에 맞지 않으므로 멈춘다. `while let` 표현으로 스택의 모든 값들을 간편하게 꺼낼 수 있다.

### for 루프

`for` 루프의 패턴은 `for` 키워드 바로 다음에 오는 값이다. 즉 `for x in y` 에서 `x`가 패턴이다.

다음 코드는 `for`에서 패턴을 이용해 튜플을 분해해서 튜플을 `for` 루프의 일부로서 사용하는 것을 보여준다.

```rust
let v = vec!['a', 'b', 'c'];

for (index, value) in v.iter().enumerate() {
    println!("{} is at index {}", value, index);
}
```

`enumerate` 메소드는 해당 반복자를 자신의 값과 값의 `index`를 포함한 튜플들을 순회하도록 바꿔준다. 그래서 `enumerate`의 첫 줄은 `(0, ‘a’)`를 반환한다. 이 반환된 튜플은 `(index, value)` 패턴에 대응되면 `index`는 `0`, `value`는 `‘a’` 값을 갖는다.

### let 구문

여태것 `match`나 `if let` 처럼 직접적으로 패턴의 사용을 언급했다. 하지만 이미 여러 곳에서 패턴을 사용하고 있고 대표적으로 `let` 구문이 있다. `let` 을 이용한 변수 대입을 예로 들어보자.

```rust
let x = 5;
```

이미 많이 보았을 `let` 이지만 사실 이 구문에는 패턴이 사용되고 있다. `let` 구문을 더 형식에 맞춰 나타내면 다음과 같다.

```
let PATTERN = EXPTRESSION;
```

`let x = 5;` 처럼 `PATTERN` 자리에 변수명이 들어가는 경우, 그 변수명 자체가 하나의 무지하게 단순한 패턴의 한 형태다. 러스트는 `EXPRESSION`을 `PATTERN`에 비교하고 거기서 발견한 이름들에 그 `EXPRESSION`을 대입한다. `let x= 5;` 예제에서 `x`라는 패턴은 “이 패턴에 대응 되는 값을 변수 x에 대입하는” 패턴이다. 이 경우 `x`가 패턴의 전부이므로 “아무 값을 통으로 `x`에 대입한다”는 의미를 갖게 된다.

`let` 의 좀더 “패턴 매칭”스러운 면모를 보기 위해 다음을 보자. 이 예제는 튜플을 분해하는 패턴을 사용하고 있다.

```rust
let (x, y, z) = (1, 2, 3);
```

이 코드는 튜플을 하나의 패턴에 대응시키고 있다. 러스트는 튜플 `(1, 2, 3)`을 `(x, y, z)`에 비교하고 튜플이 해당 패턴에 대응된다는 것을 알아 낸다. 그래서 `1`을 `x`, `2`를 `y`, `3`을 `z`에 각각 묶는다. 이 튜플 예제를 3개의 변수 대입 패턴을 하나의 패턴으로 묶은 것을 생각해도 좋다.

만약 패턴의 원소의 수가 주어진 튜플의 원소의 수와 다를 경우, 둘의 타입이 서로 달라 대응에 실패하고 컴파일 에러가 발생하게 된다.

```rust
let (x, y) = (1, 2, 3);
```

튜플의 원소중 하나, 혹은 여럿을 무시하고 싶을 경우, 이후에 다룰 `_`나 `..`를 사용할 수 있다. 반대로 문제가 패턴 쪽의 변수가 너무 많아서 생겼다면 패턴의 변수 일부를 없에는 것으로 튜플의 원소의 수와 패턴의 변수 원소의 수를 맞추어 둘의 타입을 맞춰주면 된다.

### 함수의 매개변수

함수의 매개변수들도 물론 패턴이다. 다음은 `i32` 타입의 매개변수 `x`를 가지는 함수 `foo`를 정의하는 코드다.

```rust
fn foo(x: i32) {
	// code goes here
}
```

당연하게도 `x`는 패턴이다. `let`에서 그랬듯, 함수에 인자를 넘기는 과정에서 튜플을 패턴에 대응시킬 수 있다. 다음코드는 함수에 튜플을 넘기면서 그 값들을 분해해서 사용하는 코드다.

```rust
fn print_coordinates(&(x, y): &(i32, i32)) {
	println!("Current location: ({}, {})", x, y);
}

fn main() {
	let point = (3, 5);
	print_coordinates(&point);
}
```

패턴은 함수의 매개변수에서 사용한 방법 그대로 클로저의 매개변수에서도 사용할 수 있다. 

패턴의 다양한 사용법을 보았지만 패턴은 사용하는 장소마다 다른 방식으로 작동한다. 몇몇 에서의 패턴은 *반증 불가(irrefutable)* 해야 하고 이는 곧 패턴이 어떠한 값이든 대응해야 된다는 뜻이다. 그외의 경우엔 *반증 가능(refutable)*해도 된다. 이 둘의 차이를 다뤄보자.

---

## 반증 가능성(Refutability): 패턴이 매칭에 실패할지의 여부

패턴은 2가지 형태가 존재한다. 반증 가능 패턴과 반증 불가 패턴. 주어진 어떠한 값에도 대응되는 패턴을 *반증 불가(irrefutable)* 패턴이라 한다. 예를 들면 `let x = 5;`의 `x`가 있다. `x`에 어떠한 값이 오건 대응하기 때문에 실패할 수 없고 이는 곧 반증 불가하다고 한다. 주어진 값에 대응이 실패할 수 있는 패턴을 *반증 가능(refutable)* 패턴이라 한다. `if let Some(x) = a_value;` 의 `Some(x)`가 그 예시다. `a_value`의 값이 `None`인 경우가 있다면 `Some(X)`에 대응하지 못하고 실패하게 된다. 즉 이는 반증 가능하다.

함수의 매개변수, `let` 구문, `for` 루프들은 반증 불가한패턴만을 허용한다. 이 표현들의 경우 값을 패턴에 대응하는데 실패할 경우 프로그램이 할 수 있는 행동이 없기 때문이다. 반대로 `if let` 과 `while let` 표현은 반증 가능 패턴만을 허용한다. 이 표현들은 성공 여부에 따라 다른 행동을 하도록 설계 됐기 때문에 실패의 여지가 있는 패턴이 올 것을 가정한다.

일반적으로 반증 가능 패턴과 반증 부가 패턴의 차이에 대해서는 걱정할 필요가 없다. 다만 관련된 에러메세지를 보고 코드를 고치기 위해서는 이 반증 가능성이라는 개념을 숙지 해야할 필요가 있다. 만일 관련된 에러가 생길 경우 원래 의한 기능에 맞춰 패턴을 고치거나, 패턴을 이용하는 구문을 고쳐야 한다.

반증 불가능 패턴이 필요한 곳에서 반증 가능 패턴을 쓰는 경우와 그 반대의 경우를 살펴보자. 다음 코드는 `let` 구문에서 반증 가능 패턴 `Some(x)`를 쓰고 있다. 당연히 에러가 발생한다.

```rust
 let Some(x) = some_option_value;
```

`some_option_value`가 `None`일 경우 `Some(x)`에 대응하는데 실패한다. 즉 이는 반증 가능 패턴이다. 하지만 `let` 구문은 반증 불가한 패턴만을 허용하기에 `None`이 왔을 경우 할 수 있는 일이 없다. 따라서 컴파일시에 러스트는 에러를 발생시킬 것이다.

```
error[E0005]: refutable pattern in local binding: `None` not covered
 -->
  |
3 | let Some(x) = some_option_value;
  |     ^^^^^^^ pattern `None` not covered
```

이런 문제를 해결하기 위해 패턴을 이용하는 구문을 바꾸는 방법이 있다. `let`을 쓰는 대신 `if let`을 사용하는 것이다. 이 경우 패턴에 값을 대응하는데 실패하면 중괄호 안의 코드를 건너뛰면 된다. 

```rust
if let Some(x) = some_option_value {
    println!("{}", x);
}
```

이번엔 `if let` 에서 반증 불가 패턴을 사용할 경우 에러를 받는다.

```rust
if let x = 5 {
    println!("{}", x);
};
```

틀릴 수도 있는 `if let` 구문에 틀릴리 없는 패턴을 쓰는 것은 말이 되지않기 때문에 에러가 발생한다.

```
error[E0162]: irrefutable if-let pattern
 --> <anon>:2:8
  |
2 | if let x = 5 {
  |        ^ irrefutable pattern
```

이 대문에 `match`의 갈래는 마지막 갈래를 제외하고는 반증 가능한 패턴을 써야한다. 마지막 갈래는 빠짐 없이 대응해야하는 `match` 표현의 특성상 반증 불가한 패턴을 사용해야 한다. 러스트에서 `match`를 반증 불가 패턴을 가진 하나의 갈래만으로 구성하는 것은 가능하다만, `let` 구문 하나로 대체될 수 있기에 딱히 득 볼 것이 없다.

---

## 패턴 문법의 모든 것

### 리터럴 매칭

앞에서 여러번 본 것처럼 패턴과 리터럴을 직접 매칭할 수 있다.

```rust
let x = 1;

match x {
    1 => println!("one"),
    2 => println!("two"),
    3 => println!("three"),
    _ => println!("anything"),
}
```

`x`의 값이 `1`이기 때문에 이 코드는 `one`을 출력한다.

### 명명 변수 매칭

명명 변수는 어떠한 값에도 매칭되는 반증 불가능한 패턴이며, 여태 여러번 사용해왔다. `match` 표현에서 명명 변수를 사용할때 문제가 있다. 바로 `match`는 새로운 스코프를 만들기 때문에 `match` 표현 내에서 패턴의 일부로서 선언된 변수는 `match` 구조 외부의 동일한 이름을 가진 변수를 가려버린다는 점이다. 다음 코드에서 `Some(5)` 값으로 `x` 변수를 선언하고, `10` 값으로 `y`를 선언했다. 

```rust
fn main() {
    let x = Some(5);
    let y = 10;

    match x {
        Some(50) => println!("Got 50"),
        Some(y) => println!("Matched, y = {:?}", y),
        _ => println!("Default case, x = {:?}", x),
    }

    println!("at the end: x = {:?}, y = {:?}", x, y);
}
```

일단 첫번째 갈래는 정의된 변수 `x`와 매칭되지 않으니 해당 코드는 실행되지 않고 넘어간다.

두번째 갈래 패턴에서는 `Some` 값 안에 있는 값에 대응될 새 변수 `y`가 등장한다. 지금 당장은 `match` 표현 내의 새로운 스코프에 위치해 있기 때문에, 이 `y`는 처음에 `10`의 값을 갖는 변수가 아니라 아예 새로운 변수다. 이 새로운 `y`는 `x`로 가지고 있는 `Some` 내부의 어떠한 값으로 바인딩 될 것이다. 따라서, 이 새 `y`는 `x`의 `Some` 내부 값인 `5`로 바인딩되고, 해당 갈래의 표현이 실행되어 `Matched, y = 5`가 출력된다.

만약 `x`가 `Some(5)`가 아니라 `None` 값을 가지고 있었다면 첫번재와 두번째 갈래는 매치되지 않고 언더스코어와 매칭되었을 것이다. 언더스코어 갈래에서는 `x` 변수를 새로 만들지 않았기에 `x`는 가려지지 않은 상태로 여전히 바깥의 `x` 변수를 나타내고, 만일 코드를 실행한다면 `match`는 `Default case, x = None`을 출력할 것이다.

`match` 표현이 끝나면 안쪽의 `y`를 갖던 스코프도 끝난다. 그리고 마지막 `println!`은 `at the end: x = Some(5), y = 10` 을 출력한다.

기존 변수를 가리는 변수를 만들지 않고 외부의 `x`와 `y`의 값을 비교하는 `match` 표현을 만들기 위해선 조건부(conditional) 매치 가드(match guard)를 사용해야 하는데, 매치 가드에 대해선 이후에 다룬다.

### 다중 패턴

`match` 표현 내에서 *or*을 뜻하는 `|` 구문을 이용해 여러개의 패턴과 매치시킬 수 있다. 예를 들어 다음 코드는 `x` 값을 매치 갈래와 매치 시키는데, 첫번재 갈래에서 *or* 옵션을 사용하고 있다. 이럴 경우 갈래 내의 값 중 일치하는 값이 있으면, 즉 `x` 가 `1`이나 `2`일 경우 해당 갈래의 코드가 실행된다.

```rust
let x = 1;

match x {
    1 | 2 => println!("one or two"),
    3 => println!("three"),
    _ => println!("anything"),
}
```

### …를 이용한 값의 범위 매칭

`…` 구문을 이용해 값의 범위 내에 매치시킬 수 있다. 다음 코드에선 패턴이 범위 내 값에 매칭될 경우 해당 갈래가 실행된다.

```rust
let x = 5;

match x {
    1 ... 5 => println!("one through five"),
    _ => println!("something else"),
}
```

만일 `x`가 1, 2, 3, 4, 5 중 하나라면 첫번재 갈래와 매치된다. `|`를 이용해 작성하는 것보다 `1 … 5`로 작성하는 것이 더 짧고 유용할 것이다.

이 값의 범위를 이용한 매칭 방식은 숫자 값이나 `char` 값에만 사용할 수 있다. 컴파일러가 컴파일 타임에 해당 범위가 비어있지 않은지 검사하는데, 러스트가 해당 범위가 비어있는지 알아낼 수 있는 타입의 종류가 정수 값과 `char` 뿐이기 때문이다.

다음은 `char` 값의 범위를 이용하는 예시다.

```rust
let x = 'c';

match x {
    'a' ... 'j' => println!("early ASCII letter"),
    'k' ... 'z' => println!("late ASCII letter"),
    _ => println!("something else"),
}
```

`c`가 첫번재 패턴 안에 속한다는 것을 알아낼 수 있고 , `early ASCII letter`를 출력할 것이다.

## 값을 해체해 분리하기

패턴을 이용해 구조체, 열거형, 튜플, 참조자 등의 값들을 해체(destructuring) 할 수도 있다. 

### 구조체 해체하기

다음은 `x`와 `y` 두개의 필드를 가진 `Point` 구조체를 나타낸다. 이는 `let` 구문과 패턴을 이용해 이를 해체할 수 있다.

```rust
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p = Point { x: 0, y: 7 };

    let Point { x: a, y: b } = p;
    assert_eq!(0, a);
    assert_eq!(7, b);
}
```

이 코드는 `p` 변수의 필드인 `x`와 `y`에 각각 대응되는 `a`와 `b` 변수를 생성한다. 이 예시를 보면 패턴 내 변수의 이름이 꼭 구조체의 필드명과 일치할 필요는 없다는 것을 알 수 있다. 하지만 해당 변수가 어떤 필드를 나타내는지 기억하기 쉽도록 필드명과 일치하도록 작성하는게 일반적이다.

다만 일치하도록 작성할 때 `let Point {x: x, y: y}. = p;`는 `x`와 `y`가 중복된다. 이때, 즉 패턴이 구조체 필드명과 일치할 때 약칭 구문을 사용할 수 있다. 구조체 필드명을 나열하는 것만으로 해당 필드의 값을 가진 변수를 만들어낼 수 있다. 다음 코드는 위 코드를 `let` 패턴내의 `a`와 `b`대신 `x`와 `y` 변수를 사용하도록 변경한 예시다.

```rust
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p = Point { x: 0, y: 7 };

    let Point { x, y } = p;
    assert_eq!(0, x);
    assert_eq!(7, y);
}
```

이 코드는 `p` 변수의 `x`와 `y` 필드에 대응되는 `x`와 `y` 변수를 만들어낸다. 결과는 `x`와 `y` 변수가 `p` 구조체 내의 값을 갖는 것으로 나온다.

또한 모든 필드에 대응하는 변수를 만들지 않고 구조체 패턴의 일부에 리터럴 값을 이용해 해체할 수도 있다. 이렇게 함으로써 어떤 필드가 특정 값에 해당하는지를 검사하면서 나머지 필드를 해체한 변수를 만들 수 있다.

다음은 `Point` 값을 3가지로 나눈 `match` 표현을 나타낸다.

```rust
fn main() {
    let p = Point { x: 0, y: 7 };

    match p {
        Point { x, y: 0 } => println!("On the x axis at {}", x),
        Point { x: 0, y } => println!("On the y axis at {}", y),
        Point { x, y } => println!("On neither axis: ({}, {})", x, y),
    }
}
```

이 예제에서 `p` 값은 `x`가 `0`이기 때문에 두 번째 갈래에 매치된다.

### 열거형 해체

이미 열거형 해체를 여러번 해보았다. 다만 한 가지 다루지 않은 내용이 있는데, 열거형을 해체하기 위한 패턴은 해당 열거형에 내장된 데이터의 정의 방식과 일치해야 한다는 점이다. 다음 코드는 앞서 사용했던 `Message` 열거형을 이용하고 각 내부 값을 해체하기 위한 `match` 패턴을 작성한 것이다.

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

fn main() {
    let msg = Message::ChangeColor(0, 160, 255);

    match msg {
        Message::Quit => {
            println!("The Quit variant has no data to destructure.")
        },
        Message::Move { x, y } => {
            println!(
                "Move in the x direction {} and in the y direction {}",
                x,
                y
            );
        }
        Message::Write(text) => println!("Text message: {}", text),
        Message::ChangeColor(r, g, b) => {
            println!(
                "Change the color to red {}, green {}, and blue {}",
                r,
                g,
                b
            )
        }
    }
}
```

이 코드는 마지막 갈래에 매치된다.

아무 값도 갖지 않는 `Message::Quit` 같은 variant는 값을 해체할 수 없다. 따라서 리터럴 `Message::Quit` 값만 매치시킬 수 있으며, 패턴 내의 변수는 없다.

`Message::Move` 등의 유사 구조체 variant의 경우는 구조체를 매칭시킬때의 패턴과 유사한 패턴을 사용한다. variant 명 뒤에 중괄호를 작성하고, 해당 갈래에서 사용할 변수를 나타내는 필드들을 나열한다. 

하나의 요소를 가진 `Message::Write` 튜플과 세 개의 요소를 가진 `Message::ChangeColor` 튜플 등의 유사 튜플 variant에 사용하는 패턴은 튜플을 매치시킬때 지정한 패턴과 유사하다. 이때 패턴 내 변수의 개수는 매칭하려는 variant 내 요소의 개수와 일치해야 한다.

### 참조자 해체

패턴과 매칭하려는 값이 참조자를 포함하고 있을 땐 패턴 내에서 `&`를 사용해 값으로부터 참조자를 해체해야 한다. 이렇게 하면 참조자를 갖는 변수를 가져오는 대신 참조자가 가리키는 값을 갖는 변수를 가져올 수 있다. 이는 참조자들을 반복하는 반복잗에서 클로저를 사용할 때, 해당 클로저 내에서 참조자가 아닌 참조자가 가리키는 값을 사용하길 원할 경우에 유용하다.

다음예시는 `vector` 내의 `Point` 객체들을 가리키는 참조자들을 반복하며 구조체 참조자를 해체하는 것으로 간단하게 `x`와 `y`값을 계산할 수 있다.

```rust
let points = vec![
    Point { x: 0, y: 0 },
    Point { x: 1, y: 5 },
    Point { x: 10, y: -3 },
];

let sum_of_squares: i32 = points
    .iter()
    .map(|&Point { x, y }| x * x + y * y)
    .sum();
```

이 코드의 `sum_of_squares` 변수는 135의 값을 갖는다.

만약 `&Point { x, y }` 에서 &를 뺀다면 타입 불일치 (type mismatch) 오류가 발생한다. `iter`은 벡터 내 요소들의 실제 값이 아닌 참조자이기 때문이다.

```
error[E0308]: mismatched types
  -->
   |
14 |         .map(|Point { x, y }| x * x + y * y)
   |               ^^^^^^^^^^^^ expected &Point, found struct `Point`
   |
   = note: expected type `&Point`
              found type `Point`
```

### 구조체와 튜플 해체

패턴 해체를 더 복잡한 방법으로 섞고, 비교하고, 중첩시킬 수 있따. 다음 예제는 튜플 내에 구조체와 튜플을 갖는, 즉 중첩된 구조에서 본래 값을 얻는 복잡한 해체를 보여준다.

```rust
let ((feet, inches), Point {x, y}) = ((3, 10), Point { x: 3, y: -10 });
```

이 코드는 복잡한 타입의 컴포넌트를 분리하고 각각의 값을 사용할 수 있게 한다.

패턴을 이용한 해체는 구조체의 각 필드값과 같은 일부의 값을 서로 별도로 사용하는 편리한 방법이다.

### 패턴 내에서 값 무시하기

패턴 내에서 값들을 무시하는게 유용하다는 것을 종종 보았을 것이다. 예를 들면 `match`의 마지막 갈래에 될 수 있지만 아무것도 하지 않는 모든 나머지 값들을 매칭시킬 때처럼 말이다. 전체 혹은 일부 값을 무시하는 방법은 몇 가지 있다. `_` 패턴을 사용하거나, 다른 패턴 내에서 `_` 패턴을 사용하거나, 언더스코어(`_`)로 시작하는 이름을 사용하거나, 값의 나머지 부분을 무시하기 위해 `..` 를 사용하는 것이다. 

1. _ 를 이용해 전체 값 무시하기

`_` 언더스코어 와일드카드 패턴은 어떤 값과도 매치되지만 값으로 바인드(bind) 하지는 않는 것으로 사용되어 왔다. `_` 언더스코어 패턴은 `match`의 마지막 갤래 표현에서 특히 유용하지만, 다음 코드처럼 매개변수를 포함한 모든 패턴에서 사용할 수 있다.

```rust
fn foo(_: i32, y: i32) {
    println!("This code only uses the y parameter: {}", y);
}

fn main() {
    foo(3, 4);
}
```

이 코드의 첫번째 인자로 전달된 값인 `3`을 무시한다.

대부분의 경우 특정 함수의 매개변수가 더 이상 필요하지 않는다면, 해당 함수의 시그니처를 더 이상 사용되지 않는 매개변수가 포함되지 않도록 변경해야 한다. 다만 몇몇 경우에는 함수의 매개변수를 무시하는 것이 유용할 때도 있다. 예를 들어 트레잇을 구현할때 특정 타입의 시그니처가 필요한데 함수 본문에선 매개변수중 하나가 필요하지 않은 경우다. 컴파일러는 이때 사용되지 않은 매개 변수에 관해서 경고하지 않는다. 단 언더스코어가 아닌 이름을 사용할 경우엔 경고한다.

1. 중첩된 _ 를 이용해 값의 일부 무시하기

`_` 를 다른 패턴 내에 사용해서 값의 일부를 무시할 수도 있다. 값의 일부를 테스트 하려는데 해당 코드에서 그 외의 나머지 부분은 필요하지 않을 때 이 기능을 사용할 수 있다. 다음 코드는 설정 값을 관리하는 코드다. 비지니스 요구 사항은 사용자가 기존 커스텀을 덮어쓰진 않아야 하지만 기존 설정을 해제할 수 있으며 해제된 상태에선 값을 설정하는 것이 가능해야 한다는 것이다.

```rust
let mut setting_value = Some(5);
let new_setting_value = Some(10);

match (setting_value, new_setting_value) {
    (Some(_), Some(_)) => {
        println!("Can't overwrite an existing customized value");
    }
    _ => {
        setting_value = new_setting_value;
    }
}

println!("setting is {:?}", setting_value);
```

이 코드는 `Can't overwrite an existing customized value` 와 `setting is Some(5)` 를 출력한다. 첫 번째 매치 갈래에서 두 `Some` variant 모두 내부 값을 매치시키거나 사용할 필요가 없고, 그저 `setting_value` 와 `new_setting_value` 가 모두 `Some` variant 인지만 확인하면 된다. 조건을 만족하면, 왜 `setting_value` 를 변경하지 않는지 이유를 출력하고 값을 변경하지 않는다.

나머지 모든 경우는 (`setting_value` 나 `new_setting_value` 둘 중 하나가 `None` 인 경우) 두 번째 갈래에서 `_` 로 표현되고, `setting_value` 는 `new_setting_value` 로 변경된다.

특정 값들을 무시하기 위해 한 패턴 내에서 언더스코어를 여러번 사용할 수도 있다. 다음 코드는 5개의 요소를 가진 튜플에서 두 번째와 네 번째 요소만 무시하는 예제다.

```rust
let numbers = (2, 4, 8, 16, 32);

match numbers {
    (first, _, third, _, fifth) => {
        println!("Some numbers: {}, {}, {}", first, third, fifth)
    },
}
```

1. 언더스코어로 시작하는 이름을 이용해 쓰이지 않는 변수 무시하기

만약 변수를 생성했는데 아무 곳에서도 사용하지 않는다면, 보통 러스트가 이를 버그가 될 수 있다고 경고한다. 하지만 프로토타입을 만드는 중이거나 프로젝트를 막 시작했을 때와 같이 아직 사용하진 않아도 미리 변수를 만들어 두는 것이 유용할 때도 있다. 이럴 경우 해당 변수명응ㄹ 언더스코어로 시작하도록 만들면 러스트는 해당 미사용 변수에 대해 경고를 생성하지 않는다.

```rust
fn main() {
    let _x = 5;
    let y = 10;
}
```

`y` 변수를 사용하지 않았다는 경고가 나타나지만, 언더스코어로 시작하는 `x` 변수는 경고가 나타나지 않는다.

`_` 하나만 사용하는 것과 언더스코어로 시작하는 변수명의 차이점을 기억해야 한다. `_` 는 어떠한 값도 바인드되지 않지만, `_x`는 여전히 값이 바인드 된다. 다음 코드는 이 미묘한 차이가 중요한 포인트가 되는 예시다.

```rust
let s = Some(String::from("Hello!"));

if let Some(_s) = s {
    println!("found a string");
}

println!("{:?}", s);
```

이 코드는 에러를 발생한다. `s` 값이 `_s`로 이동되었기 때문에, `s`를 다시 사용할 수 없다는 오류가 발생한다. 반면 언더스코어로만 사용하는 경우는 값이 바인드되지 않는다. 따라서 `s`는 `_`로 이동하지 않고 다음 코드는 에러가 발생하지 않는다.

```rust
let s = Some(String::from("Hello!"));

if let Some(_) = s {
    println!("found a string");
}

println!("{:?}", s);
```

1. .. 를 이용해 값의 나머지 부분 무시하기

여러 요소를 갖는 값을 다룰 때, 값의 일부만 사용하고 나머지는 무시하기 위해 `..` 구문을 사용할 수 있다. 이 구문은 무시할 각 값에 언더스코어 하나하나를 작성해야 하는 사고를 막아주기도 한다. `..` 패턴은 패턴에서 명시하지 않은 값의 나머지 부분을 모두 무시한다. 다음은 3차원 공간의 좌표를 갖는 `Point` 구조체를 갖는다. 이 `match` 표현에서는 `x`좌표만 사용하고 `y`와 `z` 필드의 값은 무시한다.

```rust
struct Point {
    x: i32,
    y: i32,
    z: i32,
}

let origin = Point { x: 0, y: 0, z: 0 };

match origin {
    Point { x, .. } => println!("x is {}", x),
}
```

`x`를 나열하고 `..` 패턴을 포함한다. 이는 `y: _` 와 `z: _` 를 나열하는 것보다 간결하고, 이보다 더 많은 필드를 갖는 구조체에서 한 두개의 필드만 필요한 상황에서 더 간결하다.

`..` 구문은 필요한 만큼 많은 값으로 확대될 수 있다. 다음은 `..`를 튜플과 사용하는 법이다.

```rust
fn main() {
    let numbers = (2, 4, 8, 16, 32);

    match numbers {
        (first, .., last) => {
            println!("Some numbers: {}, {}", first, last);
        },
    }
}
```

이 코드에서는 첫번째와 마지막 값이 각각 `first`와 `last`에 매치되고, `..` 는 중간의 모든 값과 매치된다.

다만 `..` 를 사용할 땐 모호하지 않아야(unambiguous) 한다. 만약 어떤 값이 매치되고 어떤 값이 무시되어야 하는지 명확하지 않다면 에러가 발생한다. 다음은 `..`을 모호하게 사용해 컴파일 되지 않는 경우다.

```rust
fn main() {
    let numbers = (2, 4, 8, 16, 32);

    match numbers {
        (.., second, ..) => {
            println!("Some numbers: {}", second)
        },
    }
}
```

### ref와 ref mut를 이용해 패턴 내에서 참조자 생성하기

`ref` 를 사용해 참조자를 만들어서 패턴 내 변수로 값의 소유권이 이동하지 않도록 하는법을 알아보자. 보통 패턴과 매치시킬 경우 패턴에 나타난 변수에 값이 바인드 된다. 러스트의 소유권 규칙에 따르면 값은 `match` 내부 혹은 패턴을 사용하는 모든 곳으로 이동된다. 다음은 `match`의 패턴에서 변수로 받고, 값을 `match` 이후 `println!` 구문에서 사용하는 예시다. 이 코드에서는 `robot_name` 값의 소유권이 `match`의 첫 번째 갈래에서 `name` 변수로 이전되었기 때문에 컴파일 시 오류가 발생한다.

```rust
let robot_name = Some(String::from("Bors"));

match robot_name {
    Some(name) => println!("Found a name: {}", name),
    None => (),
}

println!("robot_name is: {:?}", robot_name);
```

`robot_name`의 소유권이 `name`으로 이동했기 때문에 `robot_name`은 더 이상 소유권을 갖지 않고, 따라서 `match` 이후 `println!` 에서 `robot_name`을 사용할 수 없다.

이 코드를 고치기 위해선 `Some(name)` 에서 `robot_name` 의 소유권을 가져가는 것이 아닌 *빌려야(borrow)* 한다. 패턴을 벗어나서, 값을 빌리는 방법은 &를 이용해 참조자를 생성하는 것이 일반적이다. 그렇기에 `Some(name)`을 `Some(&name)` 으로 변경하는 것이 해결책이라 생각할 수 있다.

하지만 “값을 해체하여 분리하기”에서 본 것처럼 패턴 내에서의 `&` 구문은 참조자를 생성하는 것이 아닌, 이미 존재하는 참조자를 값으로 매치한다. `&`는 이미 패턴 내에서 다른 뜻을 갖기 때문에, 패턴 내에서 참조자를 생성하기 위해 `&`를 사용할 수 없다.

대신 다음 코드처럼 새 변수 앞에 `ref` 키워드를 사용해 패턴 내에서 참조자를 생성할 수 있다.

```rust
let robot_name = Some(String::from("Bors"));

match robot_name {
    Some(ref name) => println!("Found a name: {}", name),
    None => (),
}

println!("robot_name is: {:?}", robot_name);
```

`robot_name`의 `Some` 내 variant 값이 `match`로 이동하지 않기 때문에 이 예제는 성공적으로 컴파일된다. `match`는 `robot_name`의 데이터를 이동시키는 대신 참조자만 갖는다.

매치된 패턴 내에서 값을 변경하기 위해 가변 참조자를 생성하려면 `&mut` 대신 `ref mut`를 사용해야 한다. 

```rust
let mut robot_name = Some(String::from("Bors"));

match robot_name {
    Some(ref mut name) => *name = String::from("Another name"),
    None => (),
}

println!("robot_name is: {:?}", robot_name);
```

`name`은 가변 참조자이기 때문에 매치 갈래 코드 내에서 값을 변경하기 위해선 `*` 연산자를 이용해 역참조 해야한다.

### 매치 가드를 이용한 추가 조건

*매치 가드(match guard)*는 `match` 갈래 뒤에 추가로 붙는 `if` 조건으로, 이것이 있을 경우 패턴 매칭과 해당 조건이 모두 만족되어야 해당 갈래가 선택된다. 매치 가드는 패턴만 사용하는 것보다 복잡한 아이디어를 표현하는데 유용하다.

조건은 패턴 내에서 생성된 변수를 사용할 수 있다. 다음 코드는 `match`의 첫 번째 갈래가 `Some(x)` 패턴과 `if x < 5` 매치 가드를 갖는다.

```rust
let num = Some(4);

match num {
    Some(x) if x < 5 => println!("less than five: {}", x),
    Some(x) => println!("{}", x),
    None => (),
}
```

이 예제는 `less than five: 4` 를 출력한다. `num`이 첫 번째 갈래에서 비교될 때, `Some(4)`는 `Some(5)`에 매치된다. 그리고 매치 가드는 `x`가 `5`보다 작은지 검사한다. 이 경우 참이므로, 첫 번째 갈래가 선택된다.

만약 `num`이 `Some(10)` 이었다면, `10`은 `5`보다 크기 때문에 첫 번째 갈래에서 선택되지 않는다. 러스트는 두 번째 갈래로 이동하고, 두 번째 갈래는 매치 가드를 갖지 않으니 모든 `Some` variant에 매치된다. 따라서 두 번째 갈래와 매치된다.

`if x < 5` 조건문을 패턴 내부에서 표현할 방법은 없지만, 매치 가드는 이 로직을 표현할 수 있는 능력을 부여해 준다.

이전에 `match` 표현의 패턴 내에서 새로 생성된 변수가 기존에 있던 `match` 바깥의 변수를 가려버려서 기존의 변수를 테스트할 수 없다는 문제가 있었다. 다음 코드는 매치 가드를 사용해 이 문제를 해결하는 방법을 보여준다.

```rust
fn main() {
    let x = Some(5);
    let y = 10;

    match x {
        Some(50) => println!("Got 50"),
        Some(n) if n == y => println!("Matched, n = {:?}", n),
        _ => println!("Default case, x = {:?}", x),
    }

    println!("at the end: x = {:?}, y = {:?}", x, y);
}
```

현재 이 코드는 마지막 갈래와 매치된다. 두 번째 매치 갈래는 새 변수 `y`를 생성하는 것이 아니기 때문에 바깥의 `y`를 가리지 않는다. 즉 매치 가드에서 바깥의 `y`를 사용할 수 있다. 바깥의 `y`를 가리게 될 `Some(y)` 패턴을 지정하는 대신 `Some(n)`을 사용했다. 이는 새 변수 `n`을 생성하지만, `match` 밖에 `n` 변수는 존재하지 않기 때문에 아무것도 가리지 않는다.

`if n == y` 매치 가드는 패턴이 아니므로 새 변수를 생성하지 않는다. 여기서 `y`는 `y`를 가린 새 변수가 아닌 바깥의 `y`다. 그리고 `n`을 바깥의 `y`와 비교해 같은 값인지 판별한다.

*or* 연산자인 `|` 를 사용해 다중 패턴을 지정한 것에도 매치 가드를 사용할 수 있다. 이때 매치 가드 조건은 모든 패턴에 적용된다. 다음 코드는 매치 가드 앞에 `|` 를 이용한 다중 패턴을 결합한 모습이다. 여기서 중요한 점은 i`f y` 매치 가드가 `6`에만 적용되는 것처럼 보여도 사실 `4, 5, 6` 모두에 적용된다는 점이다.

```rust
let x = 4;
let y = false;

match x {
    4 | 5 | 6 if y => println!("yes"),
    _ => println!("no"),
}
```

갈래의 매치 조건 상태는 `x`가 `4, 5, 6` 중 하나이고 `y`가 `true` 여야 한다. 이 코드를 실행하면 `x`가 `4`이기 대문에 첫 번째 갈래의 패턴에 매치되지만, `if y` 매치 가드가 거짓이 되기 때문에 첫 번째 갈래는 선택되지 않는다. 코드는 두 번째 갈래로 이동하고 매치되며, 프로그램은 `no` 를 출력한다. 이렇게 되는 이유는 `if` 조건이 마지막 값인 `6`에만 적용되는 것이 아닌 `4 | 5 | 6` 패턴 전체에 적용되기 때문이다. 즉 매치 가드와 앞의 패턴과의 관계는 다음과 같다.

```
(4 | 5 | 6) if y => ...
```

다음은 틀린 관계다.

```
4 | 5 | (6 if y) => ...
```

### @ 바인딩

*at* 연산지인 `@`는 해당 값이 패턴과 매치되는지 확인하는 동시에 해당 값을 갖는 변수를 생성할 수 있도록 해준다. 다음 코드는 `Message::Hello`의 `id` 필드가 `3…7` 범위 내에 있는지 테스트하는 예시다. 지금 그 값을 `id_variable` 값에 바인드하고, 그 갈래의 코드에서 사용하길 원한다면 이 변수를 필드명과 똑같이 `id` 라는 이름으로 만들 수도 있지만 이번 예제에서는 다른 이름을 사용한다.

```rust
enum Message {
    Hello { id: i32 },
}

let msg = Message::Hello { id: 5 };

match msg {
    Message::Hello { id: id_variable @ 3...7 } => {
        println!("Found an id in range: {}", id_variable)
    },
    Message::Hello { id: 10...12 } => {
        println!("Found an id in another range")
    },
    Message::Hello { id } => {
        println!("Found some other id: {}", id)
    },
}
```

이 예제는 `Found an id in range: 5` 를 출력할 것이다. `3…7` 범위 앞에 `id_variable @`를 지정하는 것으로 값이 범위 패턴과 매치되는지 테스트하면서 해당 값을 캡쳐(capturing)할 수 있다.

두 번째 갈래엔 범위 패턴만 존재한다. 이 갈래의 코드에서 id 필드 값을 담는 변수가 없다. `id` 필드의 값은 10, 11, 12 중 하나가 될 수 있지만, 코드는 값이 몇인지 알지 못하고, `id` 값을 변수로 저장하지 않았기 때문에 `id` 필드의 값을 사용할 수도 없다.

마지막 갈래에서는 범위를 명시하지 않았고. `id` 라는 이름의 변수를 해당 갈래의 코드 안에서 사용할 수 있다. 이유는 구조체 필드 약칭 구문을 사용했기 때문이다. 하지만 첫 두 갈래와는 달리 `id` 필드에 대해 어떠한 테스트도 적용하지 않았다. 모든 값이 이 패턴과 매치될 것이다.

`@` 를 사용하면 값을 테스트하고 변수로 저장하는 일을 한 패턴 내에서 할 수 있다.

## 정리

러스트의 패턴은 다른 종류의 데이터를 구별하는데 굉장히 유용하다. `match` 표현 내에서 패턴을 사용하면 러스트가 패턴이 모든 가능한 값을 커버할 수 있다는 것을 보장한다. 아닐 경우에는 프로그램은 컴파일 되지 않을 것이다. `let` 구문과 함수 매개 변수에서의 패턴은 그것들을 더 유용하게 구성할 수 있게 해주고, 변수의 할당과 동시에 작은 부분의 값들로 해체하는 것을 가능하게 해준다.

---

[](https://www.notion.so/5bf67452b5774750aea232c0f1de2c20?pvs=21)