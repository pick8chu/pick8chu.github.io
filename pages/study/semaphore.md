---
title: semaphore
last_updated: Jan 21, 2021
keywords: study
sidebar: mydoc_sidebar
comments: true
permalink: semaphore.html

---

## What is Semaphore?

semaphore is a concurrency control mechanism that limits the number of concurrent operations that can be performed at the same time. Semaphores are used to manage access to shared resources, preventing issues that can arise when multiple tasks or threads try to access or modify the same resource simultaneously.


A semaphore typically consists of:

	1. A counter representing the number of available “permits” or “slots.”
	2. A mechanism to acquire and release permits:
	- When an operation wants to proceed, it “acquires” a permit by decrementing the counter.
	- When the operation finishes, it “releases” a permit by incrementing the counter.

If there are no permits left (the counter is 0), the next task must wait until a permit is released by a previous task.


## How Semaphores Work in JavaScript?

JavaScript is single-threaded (in terms of the event loop), but with the use of asynchronous tasks (e.g., promises, async/await), it’s common to have multiple concurrent operations that need to be managed. A semaphore in JavaScript controls how many asynchronous tasks can execute simultaneously, especially useful in scenarios like limiting API requests or accessing a resource that can handle a limited number of connections.


## Example:

```js
class Semaphore {
  constructor(maxConcurrency) {
    this.maxConcurrency = maxConcurrency; // Maximum concurrent tasks allowed
    this.currentConcurrency = 0;          // Current number of tasks
    this.queue = [];                      // Queue to hold waiting tasks
  }

  async acquire() {
    // If concurrency limit is reached, wait for a slot to open
    if (this.currentConcurrency >= this.maxConcurrency) {
      await new Promise(resolve => this.queue.push(resolve));
    }
    this.currentConcurrency++; // Increment the counter when a slot is acquired
  }

  release() {
    this.currentConcurrency--; // Decrement the counter when the task is done
    if (this.queue.length > 0) {
      const resolve = this.queue.shift(); // Allow the next waiting task to proceed
      resolve();
    }
  }

  async run(fn) {
    await this.acquire(); // Acquire a permit
    try {
      return await fn(); // Run the given task
    } finally {
      this.release(); // Release the permit when the task is done
    }
  }
}
```

Another example:
```js
const semaphore = new Semaphore(3); // Maximum 3 concurrent tasks

const apiCall = async (id) => {
  console.log(`Starting API call ${id}`);
  await new Promise(resolve => setTimeout(resolve, 1000)); // Simulate API delay
  console.log(`Finished API call ${id}`);
};

// Run 10 API calls, but only allow 3 to run concurrently
(async () => {
  const tasks = [];
  for (let i = 1; i <= 10; i++) {
    tasks.push(semaphore.run(() => apiCall(i)));
  }
  await Promise.all(tasks);
})();
```


## When to Use a Semaphore in JavaScript

	1. Limiting Concurrent API Requests:
When interacting with an external API that has a rate limit (e.g., allows only n requests per second), a semaphore can ensure that only a limited number of requests are sent concurrently, preventing overload or rate-limit violations.
	2. Access to Shared Resources:
When you need to control access to shared resources like databases, files, or network connections, semaphores can help ensure that only a specific number of tasks access the resource simultaneously.
	3. Batch Processing:
If you’re processing a large number of items (e.g., a list of files or data), a semaphore can help control how many items are processed concurrently, which can improve efficiency and avoid overwhelming system resources.
	4. Throttle Long-Running Tasks:
When performing tasks that are resource-intensive or long-running, a semaphore can ensure you’re not starting too many tasks at once, keeping the system responsive.

## Benefits of Semaphores:

	- Concurrency Control: A semaphore allows you to manage concurrency efficiently without overwhelming resources like the CPU, memory, or external services.
	- Avoiding Rate Limits: When dealing with APIs or services that impose rate limits, semaphores prevent too many concurrent requests from being sent.
	- Ensuring Resource Integrity: When accessing shared resources, semaphores ensure that resources aren’t accessed by too many tasks simultaneously, preventing race conditions or data corruption.

## When Not to Use a Semaphore

	- Single Task or Simple Async Workflows: If your application only handles a single task at a time or doesn’t need concurrency control, a semaphore may not be necessary.
	- Tasks Without Resource Constraints: If your operations don’t require limiting (for example, they can be performed in parallel without issue), using a semaphore might add unnecessary complexity.
