---
title: rust-learning-04
date: 2023-08-31 22:41:00
tags: [rust]
---

使用 if let 管理简约控制流

<!--more-->


```rs
let config_max = Some(3u8);
match config_max {
    Some(max) => println!("The maximum is configured to be {}", max),
    _ => (),
}
```

对于以上这种不想对 None 值做任何处理的情况，可以使用 if let

```rs
let config_max = Some(3u8);
if let Some(max) = config_max {
    println!("The maximum is configured to be {}", max);
}
```

if let 可以看作是 match 的语法糖，用于只关心匹配某种情况而不需要处理其他情况的场景

如果想处理其他场景，也可以使用 else

```rs
let mut count = 0;
match coin {
    Coin:Quarter(state) => println!("State quarter from {:?}", state),
    _ => count += 1,
}
```

```rs
let mut count = 0;
if let Coin:Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
```
