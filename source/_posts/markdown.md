---
title: markdown使用
date: 2020-01-03 11:30
tags: [markdown]
---

## Headers

```markdown
# Header 1
## Header 2
### Header 3
```
# Header 1
## Header 2
### Header 3

## Unordered Lists

```
- Bulleted
- List
```
- Bulleted
- List

## Ordered Lists

```
1. Numbered
2. List
```
1. Numbered
2. List

## Fonts

```
**Bold**
```
**Bold**

```
_Italic_
```
_Italic_

```
`Code`
```
`Code`

## Links

```
[Link](url)
```
[Link Name](https://github.com/barnett617)

```
![LinkName](https://avatars.githubusercontent.com/u/23159565?v=4)
```
![LinkName](https://avatars.githubusercontent.com/u/23159565?v=4)

## Line

```
para1
***
para2
```
para1
***
para2

## Remove

```
~~Remove this line~~
```

~~Remove this line~~

## Table

```
:--- 代表左对齐
:--: 代表居中对齐
---: 代表右对齐
```

| left | center | right | default |
| :--- | :--: | ---: | --- |
| 代表左对齐 | 代表居中对齐 | 代表右对齐 | 默认 |
| a | b | c | d |

```
| left | center | right | default |
| :--- | :--: | ---: | --- |
| 代表左对齐 | 代表居中对齐 | 代表右对齐 | 默认 |
| a | b | c | d |
```

## Detail

```
<details>
<summary>View More</summary>

Hello, I'm here! :stuck_out_tongue_closed_eyes:

</details>
```

<details>
<summary>View More</summary>

Hello, I'm here! :stuck_out_tongue_closed_eyes:

</details>

## Comment(hide content)

```
[//]:#something
```

[//]:#something

```
[^_^]:#something
```

[^_^]:#something


## Fault Use

1. Nested Use

> Don't use nested markdown symbols or you won't get your expected effect, because maybe github or something else will add extra actions to your markdown symbols like `###`, for example there will comes to a link symbol motion when you hover around the header following:

e.g.

```
- ### Something
```

- ### Something

## Advanced Use

1. Todo list

```
- [ ] Mercury
```
- [ ] Mercury