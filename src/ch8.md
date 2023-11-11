---
marp: true
class: invert
transition: slide
---

# Common Collections

- Vector : 여러 값을 나란히 저장
- String : char 컬렉션
- Hash Map : key-value 형태로 저장

---

# Vector

- 벡터는 0부터 시작하여 숫자로 인덱싱

- Create
```rs
let v: Vec<i32> = Vec::new();
let v = vec![1, 2, 3];
```

- Update
```rs
let mut v = Vec::new();

v.push(5);
v.push(6);
v.push(7);
v.push(8);
```

---

# Vector read

```rs
let v = vec![1, 2, 3, 4, 5];

let third: &i32 = &v[2];
println!("The third element is {third}");

let third: Option<&i32> = v.get(2);
match third {
    Some(third) => println!("The third element is {third}"),
    None => println!("There is no third element."),
}
```

---

# Vector safe read

```rs
    let v = vec![1, 2, 3, 4, 5];

    let does_not_exist = &v[100];    // panic
    let does_not_exist = v.get(100); // return None
```

---

# Vector loop

```rs
    // 1
    let v = vec![100, 32, 57];
    for i in &v {
        println!("{i}");
    }

    // 2
    let mut v = vec![100, 32, 57];
    for i in &mut v {
        *i += 50;
    }
```

---

# Vector with multiple types

- Enum을 활용하여 여러 타입 사용 가능
```rs
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
```

---

# Dropping a Vector Drops Its Elements

```rs
    {
        let v = vec![1, 2, 3, 4];

        // do stuff with v
    } // <- v goes out of scope and is freed here
```

---

# String

```rs
let mut s = String::new();
```

---

# Update a String

```rs
let s1 = String::from("Hello, ");
let s2 = String::from("world!");
let s3 = s1 + &s2; // note s1 has been moved here and can no longer be used
```

---

# Hash map

- 새 Hash map 생성
```rs
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
```

---

# Hash map read

```rs
let team_name = String::from("Blue");
let score = scores.get(&team_name).copied().unwrap_or(0);
// loop
for (key, value) in &scores {
    println!("{key}: {value}");
}
```

---

# Hash map 소유권

- Hash map에 값을 넣으면 소유권이 이동됨 (단, 참조를 넣으면 이동되지 않음)
```rs
use std::collections::HashMap;

let field_name = String::from("Favorite color");
let field_value = String::from("Blue");

let mut map = HashMap::new();
map.insert(field_name, field_value);
// field_name and field_value are invalid at this point, try using them and
// see what compiler error you get!
```

---

# Hash map 업데이트

```rs
    use std::collections::HashMap;

    let mut scores = HashMap::new();
    scores.insert(String::from("Blue"), 10);

    scores.entry(String::from("Yellow")).or_insert(50);
    scores.entry(String::from("Blue")).or_insert(50); // 덮어써짐

    println!("{:?}", scores);
```

---

# Hash map 업데이트2

- 이전 값 기반 업데이트
```rs
    use std::collections::HashMap;

    let text = "hello world wonderful world";

    let mut map = HashMap::new();

    for word in text.split_whitespace() {
        let count = map.entry(word).or_insert(0);
        *count += 1;
    }

    println!("{:?}", map);
```