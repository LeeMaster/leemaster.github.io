---
title: Rust 类型和字符串转换那点事
date: 2020-03-07 22:59:04
categories: [Rust]
tags: [ToString, FromStr]
---

今天用Rust写了几个leetcode上的回文串问题，发现所有的基础类型看起来可以转换为String，因为工作在写Java，所以习惯了toString，那么在Rust中如何把一个类型转换为String又如何从一个String转换为类型呢？

<!--more-->

# ToString Trait

首先看一下实现了这个trait就可以把类型转换为String的Trait。

```rust
pub trait ToString {
    fn to_string(&self) -> String;
}
```

这个Trait内部只有一个*to_string*方法，只要我们的类型实现了这个Trait就可以完成从类型到String的转换，举个例子。

```rust
struct Point {
  x: i32,
  y: i32,
}

impl ToString for Point{
  fn to_string(&self) -> String{
    format!("({},{})", self.x, self.y)
  }
}
```

这样的话就可以把一个*Point*类型实例转换为一个字符串*(x,y)*，那么所有的基本类型是怎么进行转换的呢？源码如下

```rust
impl<T: fmt::Display + ?Sized> ToString for T {
    #[inline]
    default fn to_string(&self) -> String {
        use fmt::Write;
        let mut buf = String::new();
        buf.write_fmt(format_args!("{}", self))
           .expect("a Display implementation returned an error unexpectedly");
        buf.shrink_to_fit();
        buf
    }
}
```

这样一看就比较清楚了，如果一个类型在没有实现*ToString*的时候如果实现了*Display*那么直接利用*fmt*的能力，将类型转换为*String*，这个实现是一个默认的实现，泛化了转换算法。然后再看源码发现了这样一段：

```rust
#[stable(feature = "cow_str_to_string_specialization", since = "1.17.0")]
impl ToString for Cow<'_, str> {
    #[inline]
    fn to_string(&self) -> String {
        self[..].to_owned()
    }
}

#[stable(feature = "string_to_string_specialization", since = "1.17.0")]
impl ToString for String {
    #[inline]
    fn to_string(&self) -> String {
        self.to_owned()
    }
}
```

使用了*Cow*指针，用来在写入的时候进行可变转换，因为返回的是一个对象不是一个引用，所以呢，直接移动出去就好了。

# FromStr Trait

刚才定义的类型*Point*如何把一个字符串转换为这个类型呢？

```rust
use std::str::FromStr;
use std::num::ParseIntError;

impl FromStr for Point {
    type Err = ParseIntError;

    fn from_str(s: &str) -> Result<Self, Self::Err> {
        let coords: Vec<&str> = s.trim_matches(|p| p == '(' || p == ')' )
                                 .split(',')
                                 .collect();

        let x_fromstr = coords[0].parse::<i32>()?;
        let y_fromstr = coords[1].parse::<i32>()?;

        Ok(Point { x: x_fromstr, y: y_fromstr })
    }
}
```

利用上面的代码片段就可以将一个*\(x,y\)* 转换为 *Point*了，所以这个东西是怎么工作的呢？

```rust
pub trait FromStr: Sized {
    type Err;
    fn from_str(s: &str) -> Result<Self, Self::Err>;
}
```

在源码里面*FromStr*是这样被定义的，其中包含了*from_str*方法，可以将一个字符串转换为我们需要的类型。

先看一个简单的转换，Rust转换字符串到bool的转换:

```rust
impl FromStr for bool {
    type Err = ParseBoolError;
    #[inline]
    fn from_str(s: &str) -> Result<bool, ParseBoolError> {
        match s {
            "true"  => Ok(true),
            "false" => Ok(false),
            _       => Err(ParseBoolError { _priv: () }),
        }
    }
}
```

就是这么简单，这样就开始好奇了，rust是如何把一个数字的字符串转换为数字类型呢？这里我就看了一下关于浮点数的转换

```rust
from_str_float_impl!(f32);
from_str_float_impl!(f64);
```

是的，浮点数的转换居然是通过宏实现的，那这个宏是怎么做的呢？

```rust
macro_rules! from_str_float_impl {
    ($t:ty) => {
        
        impl FromStr for $t {
            type Err = ParseFloatError;
            #[inline]
            fn from_str(src: &str) -> Result<Self, ParseFloatError> {
                dec2flt(src)
            }
        }
    };
}
```

看起来这个宏没有啥特别的东西，直接交给了这*dec2flt*函数实现转换逻辑，那就看一下这个*dec2flt*函数吧

```rust
fn dec2flt<T: RawFloat>(s: &str) -> Result<T, ParseFloatError> {
    if s.is_empty() {
        return Err(pfe_empty());
    }
    let (sign, s) = extract_sign(s);
    let flt = match parse_decimal(s) {
        ParseResult::Valid(decimal) => convert(decimal)?,
        ParseResult::ShortcutToInf => T::INFINITY,
        ParseResult::ShortcutToZero => T::ZERO,
        ParseResult::Invalid => match s {
            "inf" => T::INFINITY,
            "NaN" => T::NAN,
            _ => {
                return Err(pfe_invalid());
            }
        },
    };

    match sign {
        Sign::Positive => Ok(flt),
        Sign::Negative => Ok(-flt),
    }
}
```

看起来主要的工作是在 *core::num::parse::parse_decimal*这个函数里面做的了，这里就不分析这个函数了，但是*EBNF*给出来自己也是可以写的哦。

```
Float  ::= Sign? ( 'inf' | 'NaN' | Number )
Number ::= ( Digit+ |
             Digit+ '.' Digit* |
             Digit* '.' Digit+ ) Exp?
Exp    ::= [eE] Sign? Digit+
Sign   ::= [+-]
Digit  ::= [0-9]
```
