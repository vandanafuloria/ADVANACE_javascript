# JavaScript Promises & Async/Await - Interview Guide 📚

[![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-yellow.svg)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![Interview Prep](https://img.shields.io/badge/Interview-Ready-green.svg)](#)
[![Async Programming](https://img.shields.io/badge/Async-Programming-blue.svg)](#)

> Complete guide to JavaScript Promises and async/await for technical interviews

## 📋 Table of Contents

- [🎯 Overview](#-overview)
- [📝 Promises](#-promises)
- [⚡ Async/Await](#-asyncawait)
- [❓ Common Interview Questions](#-common-interview-questions--concepts)
- [💡 Interview Tips](#-interview-tips)
- [🛠 Practical Examples](#-practical-examples)
- [📚 Additional Resources](#-additional-resources)

## 🎯 Overview

This guide covers everything you need to know about JavaScript's asynchronous programming for technical interviews. Master these concepts to handle any async-related questions with confidence.

## 📝 Promises

### 🤔 What is a Promise?

A Promise is an object representing the eventual completion or failure of an asynchronous operation. It's a way to handle asynchronous code without falling into callback hell.

### 🔄 Promise States

- **🟡 Pending**: Initial state, neither fulfilled nor rejected
- **✅ Fulfilled**: Operation completed successfully
- **❌ Rejected**: Operation failed

### 🏗 Creating Promises

```javascript
const myPromise = new Promise((resolve, reject) => {
  // Asynchronous operation
  setTimeout(() => {
    const success = Math.random() > 0.5;
    if (success) {
      resolve("Operation successful!");
    } else {
      reject(new Error("Operation failed!"));
    }
  }, 1000);
});
```

### 🔧 Promise Methods

```javascript
// Basic usage
myPromise
  .then((result) => {
    console.log(result); // Handle success
    return result.toUpperCase();
  })
  .then((upperResult) => {
    console.log(upperResult); // Chain another operation
  })
  .catch((error) => {
    console.error(error); // Handle errors
  })
  .finally(() => {
    console.log("Cleanup operations"); // Always executes
  });
```

### 🏛 Static Methods

#### ✅ Promise.resolve() & ❌ Promise.reject()

```javascript
// Create immediately resolved/rejected promises
const resolvedPromise = Promise.resolve("Success!");
const rejectedPromise = Promise.reject(new Error("Failed!"));
```

#### 🤝 Promise.all()

Waits for all promises to resolve. Fails fast if any promise rejects.

> ⚠️ **Important**: If ANY promise rejects, the entire Promise.all() rejects immediately

```javascript
const promise1 = fetch("/api/data1");
const promise2 = fetch("/api/data2");
const promise3 = fetch("/api/data3");

Promise.all([promise1, promise2, promise3])
  .then((responses) => {
    // All requests completed successfully
    console.log("All requests completed");
  })
  .catch((error) => {
    // If any request fails
    console.error("One or more requests failed:", error);
  });
```

#### 🛡 Promise.allSettled()

Waits for all promises to settle (resolve or reject).

> ✅ **Use case**: When you want results from all promises regardless of success/failure

```javascript
Promise.allSettled([promise1, promise2, promise3]).then((results) => {
  results.forEach((result, index) => {
    if (result.status === "fulfilled") {
      console.log(`Promise ${index} succeeded:`, result.value);
    } else {
      console.log(`Promise ${index} failed:`, result.reason);
    }
  });
});
```

#### 🏃‍♂️ Promise.race()

Resolves with the first settled promise (fulfilled or rejected).

> ⚡ **Use case**: Implementing timeouts, getting fastest response

```javascript
const slowPromise = new Promise((resolve) =>
  setTimeout(() => resolve("slow"), 2000)
);
const fastPromise = new Promise((resolve) =>
  setTimeout(() => resolve("fast"), 1000)
);

Promise.race([slowPromise, fastPromise]).then((result) => console.log(result)); // "fast"
```

#### 🥇 Promise.any()

Resolves with the first fulfilled promise. Rejects only if all promises reject.

> 🎯 **Use case**: When you need at least one successful result

```javascript
Promise.any([rejectedPromise1, rejectedPromise2, resolvedPromise])
  .then((result) => console.log(result)) // Will log the resolved promise result
  .catch((error) => console.log("All promises rejected"));
```

## ⚡ Async/Await

### 🤔 What is async/await?

Syntactic sugar over Promises that makes asynchronous code look and behave more like synchronous code.

### 📝 Basic Syntax

```javascript
async function fetchUserData() {
  try {
    const response = await fetch("/api/user");
    const userData = await response.json();
    return userData;
  } catch (error) {
    console.error("Error fetching user data:", error);
    throw error; // Re-throw if needed
  }
}
```

### 🔑 Key Points

- ✅ `async` functions always return a Promise
- ⏸ `await` can only be used inside `async` functions (or top-level in modules)
- ⏳ `await` pauses execution until the Promise resolves
- 🛡 Use try/catch for error handling
- 📏 Multiple awaits execute sequentially unless explicitly made parallel

### 🚨 Error Handling

```javascript
async function handleMultipleOperations() {
  try {
    const user = await fetchUser();
    const posts = await fetchUserPosts(user.id);
    const comments = await fetchUserComments(user.id);

    return { user, posts, comments };
  } catch (error) {
    // Catches any error from the awaited operations
    console.error("Operation failed:", error);
    return null;
  }
}
```

## ❓ Common Interview Questions & Concepts

### 1️⃣ Promise Chaining vs Async/Await

**Promise Chaining:**

```javascript
function getUserPosts() {
  return fetch("/api/user")
    .then((response) => response.json())
    .then((user) => fetch(`/api/posts/${user.id}`))
    .then((response) => response.json())
    .then((posts) => {
      console.log("User posts:", posts);
      return posts;
    })
    .catch((error) => {
      console.error("Error:", error);
      throw error;
    });
}
```

**Async/Await:**

```javascript
async function getUserPosts() {
  try {
    const userResponse = await fetch("/api/user");
    const user = await userResponse.json();

    const postsResponse = await fetch(`/api/posts/${user.id}`);
    const posts = await postsResponse.json();

    console.log("User posts:", posts);
    return posts;
  } catch (error) {
    console.error("Error:", error);
    throw error;
  }
}
```

### 2️⃣ Sequential vs Parallel Execution

**🐌 Sequential (slower - operations wait for each other):**

```javascript
async function sequentialExecution() {
  console.time("Sequential");

  const result1 = await operation1(); // Wait 1 second
  const result2 = await operation2(); // Wait another 1 second
  const result3 = await operation3(); // Wait another 1 second

  console.timeEnd("Sequential"); // ~3 seconds total
  return [result1, result2, result3];
}
```

**🚀 Parallel (faster - operations run simultaneously):**

```javascript
async function parallelExecution() {
  console.time("Parallel");

  // Start all operations simultaneously
  const [result1, result2, result3] = await Promise.all([
    operation1(), // All three start at the same time
    operation2(),
    operation3(),
  ]);

  console.timeEnd("Parallel"); // ~1 second total
  return [result1, result2, result3];
}
```

**🔀 Mixed approach:**

```javascript
async function mixedExecution() {
  // First get user data
  const user = await fetchUser();

  // Then fetch user-related data in parallel
  const [posts, comments, friends] = await Promise.all([
    fetchUserPosts(user.id),
    fetchUserComments(user.id),
    fetchUserFriends(user.id),
  ]);

  return { user, posts, comments, friends };
}
```

### 3️⃣ Common Gotchas and Mistakes

**❌ Wrong - forEach doesn't wait for async operations:**

```javascript
const urls = ["/api/data1", "/api/data2", "/api/data3"];

// This won't work as expected
urls.forEach(async (url) => {
  const response = await fetch(url);
  console.log(response); // Order not guaranteed, doesn't wait
});
console.log("Done"); // This runs immediately
```

**✅ Correct - Sequential processing:**

```javascript
async function processUrlsSequentially() {
  for (const url of urls) {
    const response = await fetch(url);
    const data = await response.json();
    console.log(data); // Processes in order, waits for each
  }
  console.log("All done"); // Runs after all are complete
}
```

**✅ Correct - Parallel processing:**

```javascript
async function processUrlsInParallel() {
  const promises = urls.map((url) => fetch(url).then((r) => r.json()));
  const results = await Promise.all(promises);

  results.forEach((data) => console.log(data));
  console.log("All done");
}
```

### 4️⃣ Converting Callbacks to Promises

**📞 Callback-based code:**

```javascript
function readFileCallback(filename, callback) {
  fs.readFile(filename, "utf8", (err, data) => {
    if (err) {
      callback(err, null);
    } else {
      callback(null, data);
    }
  });
}
```

**🎁 Promise-based equivalent:**

```javascript
function readFilePromise(filename) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, "utf8", (err, data) => {
      if (err) {
        reject(err);
      } else {
        resolve(data);
      }
    });
  });
}

// Usage
async function processFile() {
  try {
    const data = await readFilePromise("file.txt");
    console.log(data);
  } catch (error) {
    console.error("Error reading file:", error);
  }
}
```

## 💡 Interview Tips

### 🗣 Frequently Asked Questions

1. **❓ What's the difference between callbacks, Promises, and async/await?**

   - 📞 Callbacks: Function passed as argument, can lead to callback hell
   - 🎁 Promises: Object representing future value, chainable with .then()
   - ⚡ Async/await: Syntactic sugar over promises, makes async code look synchronous

2. **🚨 How do you handle errors in async/await?**

   - Use try/catch blocks around await statements
   - Errors in awaited promises become exceptions in try/catch

3. **❓ What happens if you don't await a Promise?**

   - The function continues executing without waiting for the promise to resolve
   - You get a Promise object instead of the resolved value
   - Can lead to race conditions and unexpected behavior

4. **⚖️ Explain Promise.all() vs Promise.allSettled()**

   - Promise.all(): Fails fast - rejects if any promise rejects
   - Promise.allSettled(): Waits for all promises to complete, regardless of outcome

5. **🏃‍♂️ When would you use Promise.race()?**
   - Implementing timeouts
   - Getting the fastest response from multiple sources
   - Canceling operations after a certain time

### 🎯 Key Concepts to Remember

- **🛡 Promises solve callback hell** by providing a cleaner way to chain asynchronous operations
- **📖 Async/await makes asynchronous code more readable** and easier to debug
- **🚨 Always handle errors appropriately** using .catch() or try/catch
- **⚡ Understand when to use parallel vs sequential execution** for performance
- **🏛 Know the difference between Promise static methods** and when to use each

### ✅ Best Practices

1. **🛡 Always handle promise rejections**
2. **📖 Use async/await for readability, promises for complex chaining**
3. **🚀 Prefer parallel execution when operations are independent**
4. **📝 Use meaningful variable names and error messages**
5. **🚫 Don't mix callbacks and promises unnecessarily**

## 🛠 Practical Examples

### 🌐 Complete API Client Example

```javascript
class APIClient {
  constructor(baseURL) {
    this.baseURL = baseURL;
  }

  async request(endpoint, options = {}) {
    try {
      const url = `${this.baseURL}${endpoint}`;
      const response = await fetch(url, {
        headers: {
          "Content-Type": "application/json",
          ...options.headers,
        },
        ...options,
      });

      if (!response.ok) {
        throw new Error(`HTTP ${response.status}: ${response.statusText}`);
      }

      return await response.json();
    } catch (error) {
      console.error(`API request failed: ${endpoint}`, error);
      throw error;
    }
  }

  async fetchUserData(userId) {
    try {
      // Fetch user data in parallel for better performance
      const [user, posts, comments] = await Promise.all([
        this.request(`/users/${userId}`),
        this.request(`/users/${userId}/posts`),
        this.request(`/users/${userId}/comments`),
      ]);

      return {
        user,
        posts,
        comments,
        summary: {
          name: user.name,
          totalPosts: posts.length,
          totalComments: comments.length,
          joinDate: user.createdAt,
        },
      };
    } catch (error) {
      console.error("Failed to fetch user data:", error);

      // Return partial data if possible
      try {
        const user = await this.request(`/users/${userId}`);
        return {
          user,
          posts: [],
          comments: [],
          summary: { name: user.name, error: "Partial data only" },
        };
      } catch (fallbackError) {
        throw new Error("User data completely unavailable");
      }
    }
  }

  async batchProcess(items, processor) {
    const results = [];
    const errors = [];

    // Process in chunks to avoid overwhelming the server
    const chunkSize = 5;
    for (let i = 0; i < items.length; i += chunkSize) {
      const chunk = items.slice(i, i + chunkSize);

      const chunkResults = await Promise.allSettled(
        chunk.map((item) => processor(item))
      );

      chunkResults.forEach((result, index) => {
        if (result.status === "fulfilled") {
          results.push(result.value);
        } else {
          errors.push({
            item: chunk[index],
            error: result.reason,
          });
        }
      });
    }

    return { results, errors };
  }
}

// Usage example
async function demonstrateAPIClient() {
  const client = new APIClient("https://api.example.com");

  try {
    const userData = await client.fetchUserData(123);
    console.log("User data:", userData);

    const items = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
    const { results, errors } = await client.batchProcess(items, async (id) => {
      const data = await client.request(`/items/${id}`);
      return { id, data };
    });

    console.log(`Processed ${results.length} items successfully`);
    if (errors.length > 0) {
      console.log(`${errors.length} items failed:`, errors);
    }
  } catch (error) {
    console.error("Demo failed:", error);
  }
}
```

### ⏱ Promise-based Rate Limiter

```javascript
class RateLimiter {
  constructor(maxRequests, timeWindow) {
    this.maxRequests = maxRequests;
    this.timeWindow = timeWindow;
    this.requests = [];
  }

  async execute(fn) {
    return new Promise((resolve, reject) => {
      const now = Date.now();

      // Remove old requests outside the time window
      this.requests = this.requests.filter(
        (time) => now - time < this.timeWindow
      );

      if (this.requests.length < this.maxRequests) {
        // Execute immediately
        this.requests.push(now);
        fn().then(resolve).catch(reject);
      } else {
        // Wait until we can execute
        const oldestRequest = Math.min(...this.requests);
        const waitTime = this.timeWindow - (now - oldestRequest);

        setTimeout(() => {
          this.execute(fn).then(resolve).catch(reject);
        }, waitTime);
      }
    });
  }
}

// Usage
const limiter = new RateLimiter(5, 1000); // 5 requests per second

async function makeRateLimitedRequests() {
  const requests = Array.from({ length: 10 }, (_, i) =>
    limiter.execute(() => fetch(`/api/data/${i}`))
  );

  const results = await Promise.all(requests);
  console.log("All requests completed:", results.length);
}
```

This comprehensive guide covers everything you need to know about Promises and async/await for JavaScript interviews. Practice implementing these concepts and you'll be well-prepared!

## 📚 Additional Resources

### 📖 Documentation

- [MDN - Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
- [MDN - async function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)
- [MDN - await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)

### 🎯 Practice Platforms

- [JavaScript.info - Promises](https://javascript.info/promise-basics)
- [FreeCodeCamp - Async Programming](https://www.freecodecamp.org/learn/)
- [LeetCode - Concurrency Problems](https://leetcode.com/problemset/concurrency/)

### 💡 Quick Reference

| Method                 | Purpose         | Behavior           |
| ---------------------- | --------------- | ------------------ |
| `Promise.all()`        | Wait for all    | ❌ Fails fast      |
| `Promise.allSettled()` | Wait for all    | ✅ Never fails     |
| `Promise.race()`       | First to finish | ⚡ Speed matters   |
| `Promise.any()`        | First success   | 🎯 Success focused |

---

📝 **Remember**: The key to mastering async JavaScript is understanding when and why to use each approach. Practice with real examples and you'll ace any interview question!

🚀 **Pro Tip**: Always think about error handling and performance when discussing async code in interviews.
