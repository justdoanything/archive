---
Order: 7
Area: Markdown
Description: Markdown 사용법
Subject: Markdown
Progress: 100
---

---

Big Header (H1)
===
Small Header (H2)
---
# H1
## H2
### H3
###### H6

---

> Code block Depth1
>> Code Block Depth2
>>> Code Block Depth3

---

```java
public static void main(String[] args){

}
```

---

*single asterisks*  
_single underscores_  
**double asterisks**  
__double underscores__  
~~cancelline~~

---

- [ ] checkbox
- [x] checkedbox

---

| 값          |              의미              |      기본값 |
|------------|:----------------------------:|---------:|
| `static`   |      유형(기준) 없음 / 배치 불가능      | `static` |
| `relative` |      요소 **자신**을 기준으로 배치      |          |
| `absolute` | 위치 상 **_부모_(조상)요소**를 기준으로 배치 |          |
| `fixed`    |     **브라우저 창**을 기준으로 배치      |          |

---

<details>
<summary>click to collapse</summary>
this one starts expanded because of the "open"
</details>

---

```mermaid
graph TD;
  A-->B;
  A-->C;
  B-->D;
  C-->D;
```
---

:smile:

---

This is footnotes[^1].

[^1]: Here is the *text* of the **footnote**.

---

Inline maht: $ax^2 + bx + c = 0$

Block math: $$x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}$$