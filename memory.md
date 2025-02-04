# JavaScript Memory Management

## Primitive Data Types

In JavaScript, primitive data types are the most basic types of data. They are immutable (cannot be changed) and stored by value, not by reference. JavaScript has seven primitive data types: String, Number, Boolean, BigInt, Symbol, Undefined, and Null.

## Stack and Heap

- **Stack**: In the stack, primitive data types are stored, which are immutable. If we try to assign another variable through it, they will be copied.
- **Heap**: In the heap, non-primitive data types are stored, which are mutable. We directly pass the reference of the object or array if they are re-assigned.

### Spread Operator and Nested Objects in JavaScript

- The spread operator (`...`) creates a shallow copy of an object.
- Only top-level properties are copied by value.
- Nested objects are copied by reference, meaning:

  - Changes to a nested object in the copied object will also affect the original.
  - Example:

    ```js
    const obj1 = { a: 1, b: { c: 2 } };
    const obj2 = { ...obj1 };

    obj2.b.c = 100; // Modifies original object too
    console.log(obj1.b.c); // 100 (same reference)
    ```

- To fully separate nested objects, use a deep copy method like:
  - `structuredClone(obj)`
  - `JSON.parse(JSON.stringify(obj))` (with limitations)

## Memory Management in JavaScript - Questions & Answers

### 1️⃣ Stack vs. Heap

**Q:** What is the difference between stack and heap memory?
**A:**

- Stack stores primitives and copies values.
- Heap stores objects and passes references.

---

### 2️⃣ Reference vs. Copying (Code Output)

**Q:** What will be the output of this code? Why?

```js
let a = "hello";
let b = a;
b = "world";

let obj1 = { name: "Alice" };
let obj2 = obj1;
obj2.name = "Bob";

console.log(a, b); // ?
console.log(obj1.name); // ?
console.log(obj2.name); // ?
```

**A:**

```js
console.log(a, b); // hello world (Primitive copies value)
console.log(obj1.name); // Bob (Objects pass reference)
console.log(obj2.name); // Bob
```

---

### 3️⃣ How to Create a True Copy of an Object?

**Q:** How can you create a true copy of an object instead of just referencing it?
**A:**

- Spread operator (`{ ...obj }`) → Shallow copy
- `Object.assign({}, obj)` → Shallow copy
- `structuredClone(obj)` → Deep copy (best method!)
- `JSON.parse(JSON.stringify(obj))` → Deep copy but loses functions

---

### 4️⃣ Shallow vs. Deep Copy

**Q:** What is the difference between shallow copy and deep copy?
**A:**

- Shallow copy → Only top-level properties are copied; nested objects still share reference.
- Deep copy → Everything, including nested objects, gets copied.

---

### 5️⃣ Garbage Collection

**Q:** What is garbage collection, and how does JavaScript handle it?
**A:**

- JavaScript’s engine (like V8) automatically frees memory when an object has no references left.
- It runs periodically, so memory isn’t freed immediately.

---

### 6️⃣ Will this object be garbage collected?

**Q:**

```js
let user = { name: "John" };
user = null;
```

**A:** Correct! It will be garbage collected when JavaScript runs garbage collection.

---

### 7️⃣ Why is `WeakMap` useful for preventing memory leaks?

**Q:** What is the role of WeakMap in preventing memory leaks?
**A:** Unlike `Map`, a `WeakMap`:

- Holds "weak" references to objects.
- If no other references exist, the object gets garbage collected.

Example:

```js
let weakMap = new WeakMap();
let obj = { name: "Alice" };

weakMap.set(obj, "data");
obj = null; // Memory is freed! Unlike Map, WeakMap won’t hold obj forever.
```

Use WeakMap for caching or tracking objects without causing memory leaks!

---

### 8️⃣ Memory Leak in Event Listener

**Q:** Identify the memory leak in this code and fix it:

```js
document.getElementById("btn").addEventListener("click", function () {
  console.log("Clicked!");
});
```

**A:**

- Problem: The event listener stays in memory even if `#btn` is removed.
- Fix: Remove the event when it’s no longer needed:

```js
const button = document.getElementById("btn");
function handleClick() {
  console.log("Clicked!");
}
button.addEventListener("click", handleClick);

// Remove when not needed
button.removeEventListener("click", handleClick);
```

---

### 9️⃣ `setInterval` Memory Leak

**Q:** What is a common problem with `setInterval` in terms of memory leaks? How do you prevent it?
**A:**

- Problem: If not cleared, `setInterval` keeps running forever.
- Fix: Use `clearInterval(intervalID)`.

```js
let interval = setInterval(() => console.log("Running..."), 1000);

setTimeout(() => clearInterval(interval), 5000); // Stops after 5s
```

---

### 🔟 Circular Reference Memory Leak

**Q:** Why does this cause a memory leak, and how can we fix it?

```js
let obj1 = {};
let obj2 = {};
obj1.ref = obj2;
obj2.ref = obj1;
```

**A:**

- Since both reference each other, garbage collection cannot remove them, causing a memory leak.
  Fix: Break the reference manually:

```js
obj1.ref = null;
obj2.ref = null; // Now garbage collection can clean them up
```

---

## 🚀 Final Score & Should You Move On?

✅ 8/10 Correct – Great Job! 🎉
🔹 Just review WeakMap & circular references before moving on.
🔹 Try rewriting the fixes for WeakMap & circular references to make sure you understand.

Once you feel confident, you're ready for the next topic! 🚀🔥
