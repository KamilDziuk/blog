# Debugging and Fixing Errors
2026-01-22

**Tags:** `debugging` 

## Introduction

Debugging is one of the most important (and often frustrating) skills for a JavaScript developer. Whether you're working on a small script or a large web application, bugs are inevitable. The key is not avoiding them, but **finding and fixing them efficiently**.

This article covers **best practices for debugging and fixing errors in JavaScript**, including tools, techniques, and prevention strategies.

---

## Types of Errors in JavaScript

### 1. Syntax Errors

Occur when the code violates JavaScript grammar rules.

```js
if (x > 5 {
  console.log(x);
}
```

Usually caught immediately by the interpreter or editor.

---

### 2. Runtime Errors

Happen while the program is running.

```js
let user = null;
console.log(user.name);
```

The code is valid but crashes during execution.

---

### 3. Logical Errors

The hardest to detect – the app runs but behaves incorrectly.

```js
function isAdult(age) {
  return age > 18; // logical issue
}
```

---

## Basic Debugging Techniques

### console.log()

The simplest and still very effective method.

```js
console.log('Value:', value);
```

**Best practices:**

* Log context, not just values
* Remove logs before production

---

### console.table(), console.warn(), console.error()

```js
console.table(users);
console.warn('Deprecated function');
console.error('Something went wrong');
```

More readable and semantic logs.

---

## Debugger – a Powerful Tool

### debugger keyword

```js
function calculate(a, b) {
  debugger;
  return a / b;
}
```

Pauses execution in DevTools.

---

### Chrome / Firefox DevTools

Key features:

* Breakpoints
* Step over / step into
* Watch expressions
* Call stack
* Scope inspection

Mastering DevTools is a **must-have** skill.

---

## Error Handling

### try...catch

```js
try {
  JSON.parse(data);
} catch (error) {
  console.error(error.message);
}
```

**Best practices:**

* Don’t silently swallow errors
* Always add context

---

### Custom Errors

```js
throw new Error('Invalid user input');
```

Makes debugging faster and clearer.

---

## Debugging Asynchronous Code

### Promises

```js
fetch(url)
  .then(res => res.json())
  .catch(err => console.error(err));
```

### async / await

```js
try {
  const res = await fetch(url);
} catch (e) {
  console.error(e);
}
```

async/await greatly improves readability and debugging.

---

## Testing as a Debugging Strategy

### Unit Tests

* Jest
* Vitest
* Mocha

Tests catch bugs **before** users do.

---

## Best Practices to Prevent Bugs

* Clear variable names
* Small, focused functions
* Linter (ESLint)
* Formatter (Prettier)
* TypeScript
* Code reviews

---

## Conclusion

Debugging is a process, not a one-time task. The better your tools and habits, the faster you’ll identify issues and build stable, predictable JavaScript applications.



## Sources

* MDN Web Docs – Debugging JavaScript
  [https://developer.mozilla.org/en-US/docs/Learn/JavaScript/First_steps/What_went_wrong](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/First_steps/What_went_wrong)

* MDN Web Docs – Error Handling
  [https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Control_flow_and_error_handling]


