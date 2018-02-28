## Shinjuku.rb #58 Rust
- --
## C++er が Rust をやってみた

---

## 今年は Ruby 25 周年！！

---

## 以上で Ruby の話は終了

---


## 今日話す内容
## C++er が Rust をやってみた

---

## 自己紹介
- - -

* なまえ  : おしょー
* Twitter : [@pink_bangbi](https://twitter.com/pink_bangbi)
* github  : [osyo-manga](https://github.com/osyo-manga)
* ブログ  : [Secret Garden(Instrumental)](http://secret-garden.hatenablog.com)
* C++ 歴  : 10年以上、C++ 出来ない      <!-- .element: class="fragment" -->
  * C++ は今年で35周年！      <!-- .element: class="fragment" -->
* Ruby 歴： 3年ぐらい、ﾁｮｯﾄﾃﾞｷﾙ      <!-- .element: class="fragment" -->
* Rust 歴： 1〜2日      <!-- .element: class="fragment" -->

---

# C++ といえば
# なんでしょうか!!!

---

# そう
# コンパイル時処理!!!

---

#### C++ では constexpr というキーワードを使用して
#### それが『コンパイル時に処理される』という事を
#### 明示化することが出来る

```cpp
// この関数をコンパイル時に実行する事が出来る
constexpr int
factorial(int n){
	return n == 1 ? 1 : n * factorial(n - 1);
}

// コンパイル時に処理される assert
static_assert(factorial(5) == 120);
```

---

## 今回はこんな感じの事を
## Rust <del>でもやってみました</del>
## やろうとしました


---

## Rust でのコンパイル時処理
- - -

* マクロ      <!-- .element: class="fragment" -->
  * C/C++ のマクロっぽいやつだけどめっちゃ強力
* const 定数      <!-- .element: class="fragment" -->
  * いわゆるコンパイル時定数
* コンパイラプラグイン      <!-- .element: class="fragment" -->
  * 言語構文なんかを拡張できるらしい

---

## constexpr ないじゃん！！！
# なんで！！！

---

## const 定数
- - -

```rust
// const では型を明示化する必要がある
const PI: f32 = 3.14;

fn main() {
	const PI2: f32 = PI + PI;

	println!("{}", PI);
	println!("{}", PI2);
}
/*
output:
3.14
6.28
*/
```

---

## これを使えば Rust でも
## コンパイル時に演算できそうじゃない？

---

# やってみた

---

#### 値の定義
- - -

```rust
// VALUE 定数を定義する関連型
trait HasVALUE {
	const VALUE: i32;
}

// HasVALUE に対して任意の値を定義
struct _1;
impl HasVALUE for _1 {
	const VALUE: i32 = 1;
}

struct _2;
impl HasVALUE for _2 {
	const VALUE: i32 = 2;
}

struct _5;
impl HasVALUE for _5 {
	const VALUE: i32 = 5;
}
```

---

#### 演算
- - -

```rust
// HasVALUE に対して演算を実装
struct Plus<T, U>(T, U);
impl<T: HasVALUE, U: HasVALUE> HasVALUE for Plus<T, U> {
	const VALUE: i32 = T::VALUE + U::VALUE;
}

struct Minus<T, U>(T, U);
impl<T: HasVALUE, U: HasVALUE> HasVALUE for Minus<T, U> {
	const VALUE: i32 = T::VALUE - U::VALUE;
}

struct Times<T, U>(T, U);
impl<T: HasVALUE, U: HasVALUE> HasVALUE for Times<T, U> {
	const VALUE: i32 = T::VALUE * U::VALUE;
}

struct Divides<T, U>(T, U);
impl<T: HasVALUE, U: HasVALUE> HasVALUE for Divides<T, U> {
	const VALUE: i32 = T::VALUE / U::VALUE;
}
```

>>>

```rust
struct Twice<T>(T);
impl<T: HasVALUE> HasVALUE for Twice<T> {
	const VALUE: i32 = Plus::<T, T>::VALUE;
}

debug_assert!(Plus::<_1, _2>::VALUE  == 3);
debug_assert!(Minus::<_1, _2>::VALUE == -1);
debug_assert!(Plus::<_1, Times<_2, _2>>::VALUE == 5);
debug_assert!(Times::<Times<_2, _2>, _2>::VALUE == 8);
debug_assert!(Twice::<_2>::VALUE == 4);
```

---

#### 階乗
- - -

```rust
struct Fact<T>(T);
impl<T: HasVALUE> HasVALUE for Fact<T> {
	const VALUE: i32 = if T::VALUE == 0 {
		0
	}
	else {
		T::VALUE + Fact::<Times<T, _1>>::VALUE
	};
}
```

---

#### おや…
- - -

```
error[E0019]: constant contains unimplemented expression type
  --> rust.rs
|
|    const VALUE: i32 = if T::VALUE == 0 {
|  _____________________^
| |   0
| |  }
| |  else {
| |   T::VALUE + Fact::<Times<T, _1>>::VALUE
| |  };
| |__^
```

---


### まとめ
- - -

* コンパイル時の if 式はサポートされていない      <!-- .element: class="fragment" -->
* constexpr みたいにコンパイル時に計算するのはちょっと厳しそう…      <!-- .element: class="fragment" -->
* その代わりマクロやコンパイラプラグインでオレオレ構文みたいなのがつくれるようなのでそっちのほうが面白そう      <!-- .element: class="fragment" -->

---

## ちなみに…

---

開発版では [const_fn](https://doc.rust-lang.org/beta/unstable-book/language-features/const-fn.html) がある

```rust
#![feature(const_fn)]

const fn double(x: i32) -> i32 {
    x * 2
}

const FIVE: i32 = 5;
const TEN: i32 = double(FIVE);

fn main() {
    assert_eq!(5, FIVE);
    assert_eq!(10, TEN);
}
```

---

# 最初から
# これがほしかった

---


## ご清聴
## ありがとうございました
