---
title: Generator function
keywords: study
sidebar: mydoc_sidebar
permalink: generator-function.html
last_updated: Oct 8, 2026
comments: true

---


# JavaScript Generator Functions

## 1. Simple explanation

A generator is a function that can:

1. run
2. pause
3. return control to the caller
4. resume later from the same place

Syntax:

```js
function* generator() {
  yield 1;
  yield 2;
}
```

Use `.next()` to continue:

```js
const gen = generator();

gen.next(); // { value: 1, done: false }
gen.next(); // { value: 2, done: false }
gen.next(); // { value: undefined, done: true }
```

### `yield`

Think of:

```js
const x = yield "hello";
```

roughly as:

```js
sendToCaller("hello");
pause();

x = valuePassedToNext();
```

Example:

```js
function* conversation() {
  const name = yield "Your name?";
  return `Hello ${name}`;
}

const gen = conversation();

gen.next();
// { value: "Your name?", done: false }

gen.next("Ethan");
// { value: "Hello Ethan", done: true }
```

So:

```text
yield X   -> sends X out and pauses
next(Y)   -> resumes and makes yield return Y
```

The main feature is not just iteration.

It is:

> A function can pause while preserving its local variables and execution position.

---

# 2. Why generators are useful

## A. Maintaining state

```js
function* ids() {
  let id = 1;

  while (true) {
    yield id++;
  }
}

const gen = ids();

gen.next().value; // 1
gen.next().value; // 2
gen.next().value; // 3
```

The generator remembers `id` automatically.

---

## B. Batch fetching, one item at a time

Suppose an API returns 100 products per request.

```js
async function* products() {
  let offset = 0;

  while (true) {
    const batch = await fetchProducts({
      offset,
      limit: 100,
    });

    if (batch.length === 0) return;

    for (const product of batch) {
      yield transform(product);
    }

    offset += 100;
  }
}
```

Consumer:

```js
for await (const product of products()) {
  await processProduct(product);
}
```

Flow:

```text
fetch 100
   ↓
yield product 1
yield product 2
...
yield product 100
   ↓
fetch next 100
```

The consumer does not need to know about pagination.

---

## C. Streaming large files

Instead of loading a whole file:

```js
const entireFile = await readWholeFile();
```

you can process chunks:

```js
async function* readChunks(file) {
  while (hasMoreData(file)) {
    const chunk = await readNextChunk(file);

    yield chunk;
  }
}
```

Consumer:

```js
for await (const chunk of readChunks(file)) {
  process(chunk);
}
```

Useful when the file is too large to keep entirely in memory.

---

## D. Processing pipelines

Generators can compose naturally:

```js
function* activeProducts(products) {
  for (const product of products) {
    if (product.active) {
      yield product;
    }
  }
}
```

Then:

```js
for (const product of activeProducts(products)) {
  process(product);
}
```

Values are produced only when requested.

---

# Generator vs async generator

Normal generator:

```js
function* values() {
  yield 1;
}
```

Used when producing values synchronously.

Async generator:

```js
async function* values() {
  const value = await fetchSomething();
  yield value;
}
```

Used when producing values involves async work.

Consume with:

```js
for await (const value of values()) {
}
```

---

# Generator vs Semaphore

They solve different problems.

### Generator

Controls:

> What is the next value?

```text
produce
pause
produce
pause
```

### Semaphore

Controls:

> How many operations may run concurrently?

Example:

```text
Maximum 5 requests running at once.
```

They can be used together:

```text
API -> fetch 100 products
Generator -> expose them one by one
Semaphore -> process max 10 concurrently
```

---

# Mental model

Think of a generator as a function with a built-in bookmark:

```text
start
 ↓
code
 ↓
yield ← bookmark
 ↓
pause
 ↓
next()
 ↓
continue from bookmark
```

The most important concept is:

> `function*` creates a resumable function whose execution state is preserved between calls.