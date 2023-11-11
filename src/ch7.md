---
marp: true
class: invert
transition: slide
---

# Module System 구성요소

- Packages: Crates 빌드, 테스트, 공유를 위한 기능
- Crates: 라이브러리 또는 실행파일의 트리
- Modules and use: organization, scope, and privacy of paths 제어
- Paths:struct, function, or module 네이밍 방법

---

# Crate

- Binary Crate (main 있음)
- Library Crate (main 없음)
- Cargo.toml에 진입점 명시하지 않음
    - `src/main.rs` 또는 `src/lib.rs` 존재 여부로 Binary/Library 여부 판단

---

# Module 동작 방식

- Crate Root(`src/main.rs` 또는 `src/lib.rs`)로 진입
- Crate Root에 모듈 선언 (`mod garden;`)
- 모듈(ex. garden)에 하위 모듈 선언 가능 (`mod vegetables;`)
- 코드 경로 : `crate::garden::vegetables::Asparagus`
- Privacy 설정 `pub mod` vs `mod`
- Use 키워드 `use crate::garden::vegetables::Asparagus` 이후엔 `Asparagus`로 호출

```
backyard
├── Cargo.lock
├── Cargo.toml
└── src
    ├── garden
    │   └── vegetables.rs
    ├── garden.rs
    └── main.rs
```

--- 

# Module 동작 방식

```rs
use crate::garden::vegetables::Asparagus;

pub mod garden; # 컴파일러에게 garden 모듈에서 찾은 코드를 포함하도록 지시

fn main() {
    let plant = Asparagus {};
    println!("I'm growing {:?}!", plant);
}
```

---

# Crates

- 모듈을 활용해 코드 그룹핑 가능
```rs
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
}
```

---

# Module Tree

```
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

---

# Module Tree Referencing

- 절대경로는 `crate` 키워드로 시작
- 상대경로는 `self`/`super`과 같은 모듈 식별자 사용 가능
```rs
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```
- 위 코드는 컴파일 에러 남

---

# Exposing Paths with the pub Keyword

```rs
mod front_of_house {
    pub mod hosting {   # 여기에 pub 키워드 추가
        pub fn add_to_waitlist() {} # 여기도 pub 키워드 추가
    }
}

pub fn eat_at_restaurant() {
    // Absolute path
    crate::front_of_house::hosting::add_to_waitlist();

    // Relative path
    front_of_house::hosting::add_to_waitlist();
}
```

---

# Making Structs and Enums Public

```rs
mod back_of_house {
    pub struct Breakfast {
        // 필드별로 공개 여부를 설정 가능
        pub toast: String,
        seasonal_fruit: String,
    }

    impl Breakfast {
        pub fn summer(toast: &str) -> Breakfast {
            Breakfast {
                toast: String::from(toast),
                seasonal_fruit: String::from("peaches"),
            }
        }
    }
}

pub fn eat_at_restaurant() {
    let mut meal = back_of_house::Breakfast::summer("Rye");
    meal.toast = String::from("Wheat");
    println!("I'd like {} toast please", meal.toast);

    // 아래 코드는 작동 안됨
    // meal.seasonal_fruit = String::from("blueberries");
}
```

---

# Shorten

```rs
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;

mod customer {
    pub fn eat_at_restaurant() {
        hosting::add_to_waitlist();
    }
}

// 또는

use crate::front_of_house::hosting::add_to_waitlist;

pub fn eat_at_restaurant() {
    add_to_waitlist();
}
```

---

# use Scope

```rs
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

use crate::front_of_house::hosting;

mod customer {
    pub fn eat_at_restaurant() {
        hosting::add_to_waitlist(); // 에러 발생
    }
}
```

---

# Creating Idiomatic use Paths

- structs, enums 등을 가져올 때는 use전체 경로를 지정하는 것이 관용적
- `std::fmt::Result`와 `use std::io::Result` 중 어느 것을 가져올지 모호한 상황이 종종 생김
```rs
use std::fmt;
use std::io;

fn function1() -> fmt::Result {
    // --snip--
}

fn function2() -> io::Result<()> {
    // --snip--
}
```

---

# Re-export
```rs
mod front_of_house {
    pub mod hosting {
        pub fn add_to_waitlist() {}
    }
}

pub use crate::front_of_house::hosting; // 

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}
```

---

# Module 파일 분리

```rs
// src/lib.rs
mod front_of_house;

pub use crate::front_of_house::hosting;

pub fn eat_at_restaurant() {
    hosting::add_to_waitlist();
}
```

```rs
// src/front_of_house.rs
pub mod hosting {
    pub fn add_to_waitlist() {}
}
```

--- 