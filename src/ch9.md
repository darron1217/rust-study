---
marp: true
class: invert
transition: slide
---

# Unrecoverable Error

- 복구할 수 없는 오류에 대해선 `panic!` 키워드로 패닉 발생 가능
- panic이 발생하면 기본적으론 스택을 따라가며 자원을 해제하는 unwinding이 발생하지만, 때에 따라 abort로 설정도 가능
```
# Cargo.toml
[profile.release]
panic = 'abort'
```

---

# Recoverable Error

- 복구 가능한 오류에 대해선 Enum으로 유형 정의
```rs
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

---

# Recoverable Error

```rs
use std::fs::File;

fn main() {
    let greeting_file_result = File::open("hello.txt");

    let greeting_file = match greeting_file_result {
        Ok(file) => file,
        Err(error) => panic!("Problem opening the file: {:?}", error),
    };
}
```
