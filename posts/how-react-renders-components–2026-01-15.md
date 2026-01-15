# How React renders components – reconciliation, Fiber and batching
2026-01-15

**Tags:**  `react` 


### 1. Introduction

React does not update the DOM on every state change.
Instead, it uses **reconciliation**, an internal data structure called **Fiber**, and **update batching** to minimize DOM mutations.

Understanding these internals helps you:

* avoid unnecessary re-renders
* write predictable components
* optimize performance intentionally

---

## 2. Reconciliation explained

Reconciliation compares:

* previous React element tree
* new element tree from render

The goal is to compute the **minimal set of DOM changes**.

### Core assumptions:

1. Different element types → full remount
2. Same type → update props
3. `key` defines identity

```jsx
{items.map(item => (
  <li key={item.id}>{item.name}</li>
))}
```

---

## 3. Fiber architecture

A **Fiber** is:

* a unit of work
* a tree node
* a component representation

Each component maps to one Fiber.

Fiber enables:

* interruptible rendering
* priority-based updates
* concurrent rendering

---

## 4. Render phase vs Commit phase

### Render phase

* pure
* interruptible
* no DOM access

### Commit phase

* DOM mutations
* `useLayoutEffect`
* refs attached

---

## 5. Automatic batching (React 18)

```jsx
setTimeout(() => {
  setCount(c => c + 1);
  setCount(c => c + 1);
}, 1000);
```

single render

---

## 6. Memoization tools

* `useCallback` → stable function reference
* `useMemo` → cached computation
* `React.memo` → component bailout

---

## 7. Scheduling and transitions

```jsx
startTransition(() => {
  setResults(data);
});
```

Low-priority updates keep UI responsive.

---

## 8. Summary

React rendering is:

* declarative
* Fiber-based
* batched
* prioritized
* interruptible

Mastering this unlocks real performance gains.

---

##  Sources

 [react.dev/learn/render-and-commit](https://react.dev/learn/render-and-commit)
 
 [overreacted.io/react-as-a-ui-runtime/](https://overreacted.io/react-as-a-ui-runtime/)
