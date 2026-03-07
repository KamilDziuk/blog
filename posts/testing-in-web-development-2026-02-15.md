# Testing in Web Development (Jest + ES Modules)
2026-02-15

**Tags:**  `jest` `unit-tests` `node.js` `es-modules`   


## Introduction

Testing is a key element of professional web development. It ensures:

* stability and security of the code,
* predictable application development,
* easier refactoring and deployment of new features.

Lack of tests leads to:

* more production errors,
* slower development,
* growing technical debt.

In this article, I explain types of tests and show a practical example of setting up unit tests using **Jest** and **ES Modules** in Node.js.

---

## Types of Tests

### 1. Unit Tests

* Test individual functions or modules in isolation.
* Fast and independent of database or network.
* The foundation of a testing strategy.
```js

function add(a, b) {
  return a + b;
}

test("adds two numbers", () => {
  expect(add(2, 3)).toBe(5);
});

```

### 2. Integration Tests

```js

test("creates a user in the database", async () => {
  const res = await request(app)
    .post("/users")
    .send({ name: "Jan" });

  expect(res.statusCode).toBe(201);
});

```

* Verify how modules work together, e.g.:

  * controller + service,
  * API + database,
  * middleware + router.

### 3. End-to-End (E2E) Tests

* Simulate user behavior in the browser.
* Test the entire application flow — from the interface to the database.

---

## Getting Started

```bash
npm install -D vitest
```

---

#### Add scripts in  package.json

```json
  "scripts": {
    "test": "vitest"
  }
```

## Project Setup (Jest + ES Modules)

### package.json

```json
{
  "name": "firstTest",
  "version": "1.0.0",
  "type": "module",
  "main": "src/sum.js",
  "scripts": {
    "test": "node --experimental-vm-modules node_modules/jest/bin/jest.js"
  },
  "devDependencies": {
    "jest": "^30.2.0"
  }
}
```

**Explanation:**

* `"type": "module"` enables ES Modules.
* `--experimental-vm-modules` allows Jest to work with ESM.
* Tests are in `devDependencies`, so they do not go to production.

### jest.config.js

```js
export default {
  testEnvironment: "node"
};
```

* Sets Node environment.
* Tells Jest that `.js` files are ES modules.

### Project Structure

```
project/
├─ src/
│  ├─ sum.js
│  └─ __tests__/
│     └─ sum.test.js
├─ jest.config.js
├─ package.json
```

* The `__tests__` folder is a convention that allows Jest to automatically detect test files.

---

## Example Function to Test

```js
export function add(a, b) {
  if (typeof a !== "number" || typeof b !== "number") return NaN;
  if (a < 0 || b < 0) return 0;
  return a + b;
}
```

## Example Tests

```js
import { add } from "../sum.js";

describe("add()", () => {
  test("adds numbers", () => {
    expect(add(5, 5)).toBe(10);
  });

  test("numbers must be positive", () => {
    expect(add(1, -22)).toBe(0);
  });

  test("values must be numbers", () => {
    expect(add(1, "a")).toBeNaN();
  });
});
```

**Elements:**

* `describe()` – groups tests,
* `test()` – defines a test case,
* `expect()` – performs an assertion.

---

## Running Tests

```bash
npm install
npm test
```

---

## Best Practices

* Test behavior, not implementation.
* Each test should have a single purpose.
* Test names should describe the scenario.
* Tests must be repeatable.
* Integrate tests with CI/CD.

---

## Summary

Testing in web development is an investment in project quality and stability. Even simple unit tests significantly reduce the risk of errors and speed up application development. Tools like **Jest** + **ES Modules** allow me to build a modern, scalable testing environment.

---

**Sources:**

* [Jest](https://jestjs.io/docs/getting-started)

---

