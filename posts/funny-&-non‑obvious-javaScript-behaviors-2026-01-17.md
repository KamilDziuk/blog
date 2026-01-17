
# Funny & Non‑Obvious JavaScript Behaviors
2026-01-17

**Tags:**  `javascript` `specification` 

JavaScript is powerful, flexible… and sometimes **absolutely unhinged**.
Below are **non‑obvious, weird, and funny JavaScript behaviors** that can surprise even experienced developers. Each example includes **real JS code** and a short explanation.

---

## [] + [] → ""

```js
[] + []
```

**Result:**

```js
""
```

### Why?

* Arrays are converted to strings
* `[].toString()` → `""`
* Empty string + empty string = empty string

JavaScript quietly whispers: *"Nothing + nothing = nothing."*

---

## [] + {}` vs `{}` + `[]

```js
[] + {}
```

Result:

```js
"[object Object]"
```

```js
{} + []
```

Result:

```js
0
```

### Explanation

* `[] + {}` → string concatenation
* `{}` alone is parsed as an empty block
* Then `+[]` → `+0`

Same symbols. Different universe.

---

## true + false → 1

```js
true + false
```

Result:

```js
1
```

### Why?

* `true` → `1`
* `false` → `0`

JavaScript does **math with logic**.

---

## The Banana Problem

```js
"b" + "a" + +"a" + "a"
```

Result:

```js
"banana"
```

### Breakdown

```js
+"a" // NaN
```

So:

```js
"b" + "a" + "NaN" + "a"
```

Congratulations. You just summoned a banana.

---

## typeof null → "object"

```js
typeof null
```

Result:

```js
"object"
```

### Why?

This is a **25‑year‑old bug** kept for backward compatibility.

JavaScript: *"I made a mistake but now it's tradition."*

---

## NaN !== NaN

```js
NaN === NaN
```

Result:

```js
false
```

### Correct check

```js
Number.isNaN(NaN) // true
```

NaN literally means **"I don't know"**, so it doesn’t equal itself.

---

## 0.1 + 0.2 !== 0.3

```js
0.1 + 0.2 === 0.3
```

Result:

```js
false
```

### Reason

Floating‑point precision (IEEE 754).

```js
0.1 + 0.2 // 0.30000000000000004
```

Math is hard. Computers agree.

---

## parseInt("123abc")

```js
parseInt("123abc")
```

Result:

```js
123
```

But:

```js
parseInt("abc123") // NaN
```

JavaScript reads **until panic**.

---

##`[] == ![]`

```js
[] == ![]
```

Result:

```js
true
```

### Explanation (simplified)

* `![]` → `false`
* `[] == false`
* `[]` → `0`
* `false` → `0`

Equality in JS is a social construct.

---

## Functions Are Objects

```js
function hello() {}
hello.age = 25

hello.age
```

Result:

```js
25
```
Functions can have **properties**, because… why not?

---

## It's a fail

You would not believe, but…

```js
(![] + [])[+[]] +
  (![] + [])[+!+[]] +
  ([![]] + [][[]])[+!+[] + [+[]]] +
  (![] + [])[!+[] + !+[]];

```

Result:

```js
'fail'
```

Upon breaking the symbols down into smaller pieces, we can see that a certain pattern emerges often:

```js
![] + []; // -> 'false'
![]; // -> false
```

By breaking that mass of symbols into pieces, we notice that the following pattern occurs often:

```js
![] + [].toString(); // 'false'
```

Thinking of a string as an array we can access its first character via [0]:

```js
"false"[0]; // -> 'f'
```

Everything else is obvious, but the i is a bit tricky. The i in fail comes from creating the string falseundefined and taking the character at index 10.

```js
+![]          // -> 0
+!![]         // -> 1
!![]          // -> true
![]           // -> false
[][[]]        // -> undefined
+!![] / +![]  // -> Infinity
[] + {}       // -> "[object Object]"
+{}           // -> NaN
```

---

## Final Thoughts

JavaScript is weird — but **consistently weird**. Once you understand its rules, the madness becomes predictable.

Or at least… fun.

##  Sources

[denysdovhan](https://github.com/denysdovhan/wtfjs)

[dev](https://dev.to/thecodeliner/the-10-most-surprising-javascript-behaviors-explained-4ohe)

[javascriptquiz](https://javascriptquiz.com/)

[jsisweird](https://jsisweird.com/)
