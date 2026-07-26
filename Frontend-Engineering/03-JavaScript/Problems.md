# JavaScript Coding Problems

## Easy

### 1. Reverse a String
```js
function reverseString(str) {
  return str.split("").reverse().join("");
}
reverseString("hello"); // "olleh"
```

### 2. Check Palindrome
```js
function isPalindrome(str) {
  const clean = str.toLowerCase().replace(/[^a-z0-9]/g, "");
  return clean === clean.split("").reverse().join("");
}
isPalindrome("A man, a plan, a canal: Panama"); // true
```

### 3. FizzBuzz
```js
function fizzBuzz(n) {
  for (let i = 1; i <= n; i++) {
    console.log(i % 15 === 0 ? "FizzBuzz" : i % 3 === 0 ? "Fizz" : i % 5 === 0 ? "Buzz" : i);
  }
}
```

### 4. Find the Maximum Number in an Array
```js
const max = arr => Math.max(...arr);
// or: arr.reduce((a,b) => a > b ? a : b)
max([3, 7, 2, 9, 1]); // 9
```

### 5. Count Vowels in a String
```js
function countVowels(str) {
  return (str.match(/[aeiou]/gi) || []).length;
}
countVowels("hello world"); // 3
```

### 6. Remove Duplicates from Array
```js
const unique = arr => [...new Set(arr)];
unique([1,2,2,3,3,4]); // [1,2,3,4]
```

### 7. Find the Missing Number (1 to n)
```js
function missingNumber(arr, n) {
  const expected = (n * (n + 1)) / 2;
  const sum = arr.reduce((a,b) => a+b, 0);
  return expected - sum;
}
missingNumber([1,2,4,5], 5); // 3
```

### 8. Anagrams Check
```js
function areAnagrams(s1, s2) {
  const normalize = s => s.toLowerCase().split("").sort().join("");
  return normalize(s1) === normalize(s2);
}
areAnagrams("listen", "silent"); // true
```

### 9. Sum of All Digits
```js
function sumDigits(num) {
  return String(num).split("").reduce((a,d) => a + Number(d), 0);
}
sumDigits(1234); // 10
```

### 10. Capitalize First Letter of Each Word
```js
function capitalize(str) {
  return str.replace(/\b\w/g, c => c.toUpperCase());
}
capitalize("hello world"); // "Hello World"
```

## Medium

### 11. Two Sum
```js
function twoSum(nums, target) {
  const map = new Map();
  for (let i = 0; i < nums.length; i++) {
    const comp = target - nums[i];
    if (map.has(comp)) return [map.get(comp), i];
    map.set(nums[i], i);
  }
  return [];
}
twoSum([2,7,11,15], 9); // [0,1]
```

### 12. Debounce Function
```js
function debounce(fn, delay) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
}
```

### 13. Flatten an Array (Deep)
```js
function flatten(arr) {
  return arr.reduce((acc, val) =>
    acc.concat(Array.isArray(val) ? flatten(val) : val), []);
}
flatten([1,[2,[3,4],5]]); // [1,2,3,4,5]
```

### 14. Implement `Array.prototype.map`
```js
function myMap(arr, fn) {
  const result = [];
  for (let i = 0; i < arr.length; i++) {
    result.push(fn(arr[i], i, arr));
  }
  return result;
}
```

### 15. Implement `Array.prototype.reduce`
```js
function myReduce(arr, fn, initial) {
  let acc = initial !== undefined ? initial : arr[0];
  const start = initial !== undefined ? 0 : 1;
  for (let i = start; i < arr.length; i++) {
    acc = fn(acc, arr[i], i, arr);
  }
  return acc;
}
```

### 16. Currying
```js
function curry(fn) {
  return function curried(...args) {
    if (args.length >= fn.length) return fn(...args);
    return (...more) => curried(...args, ...more);
  };
}
const add = curry((a,b,c) => a+b+c);
add(1)(2)(3); // 6
```

### 17. Deep Clone Object
```js
function deepClone(obj, hash = new WeakMap()) {
  if (obj === null || typeof obj !== "object") return obj;
  if (hash.has(obj)) return hash.get(obj);
  const clone = Array.isArray(obj) ? [] : {};
  hash.set(obj, clone);
  for (const key of Reflect.ownKeys(obj)) {
    clone[key] = deepClone(obj[key], hash);
  }
  return clone;
}
```

### 18. Throttle Function
```js
function throttle(fn, limit) {
  let lastCall = 0;
  return (...args) => {
    const now = Date.now();
    if (now - lastCall >= limit) {
      lastCall = now;
      fn(...args);
    }
  };
}
```

### 19. Group By Property
```js
function groupBy(arr, key) {
  return arr.reduce((acc, item) => {
    const k = typeof key === "function" ? key(item) : item[key];
    (acc[k] = acc[k] || []).push(item);
    return acc;
  }, {});
}
groupBy([{age:20},{age:20},{age:30}], "age");
// {20: [{age:20},{age:20}], 30: [{age:30}]}
```

### 20. LRU Cache
```js
class LRUCache {
  constructor(capacity) {
    this.capacity = capacity;
    this.cache = new Map();
  }
  get(key) {
    if (!this.cache.has(key)) return -1;
    const val = this.cache.get(key);
    this.cache.delete(key);
    this.cache.set(key, val);
    return val;
  }
  put(key, value) {
    if (this.cache.has(key)) this.cache.delete(key);
    else if (this.cache.size === this.capacity)
      this.cache.delete(this.cache.keys().next().value);
    this.cache.set(key, value);
  }
}
```

## Hard

### 21. Promise.all Implementation
```js
function myPromiseAll(promises) {
  return new Promise((resolve, reject) => {
    const results = [];
    let completed = 0;
    if (promises.length === 0) return resolve(results);
    promises.forEach((p, i) => {
      Promise.resolve(p).then(val => {
        results[i] = val;
        completed++;
        if (completed === promises.length) resolve(results);
      }).catch(reject);
    });
  });
}
```

### 22. Event Emitter
```js
class EventEmitter {
  constructor() { this.events = {}; }
  on(event, listener) {
    (this.events[event] = this.events[event] || []).push(listener);
    return () => this.off(event, listener);
  }
  off(event, listener) {
    if (!this.events[event]) return;
    this.events[event] = this.events[event].filter(l => l !== listener);
  }
  emit(event, ...args) {
    (this.events[event] || []).forEach(l => l(...args));
  }
  once(event, listener) {
    const wrapper = (...args) => { listener(...args); this.off(event, wrapper); };
    this.on(event, wrapper);
  }
}
```

### 23. Memoize Function
```js
function memoize(fn) {
  const cache = new Map();
  return (...args) => {
    const key = JSON.stringify(args);
    if (cache.has(key)) return cache.get(key);
    const result = fn(...args);
    cache.set(key, result);
    return result;
  };
}
// Fibonacci with memoization
const fib = memoize(n => n <= 1 ? n : fib(n-1) + fib(n-2));
```

### 24. Deep Equality Check
```js
function deepEqual(a, b) {
  if (a === b) return true;
  if (a == null || b == null) return false;
  if (typeof a !== typeof b) return false;
  if (typeof a !== "object") return false;
  const keysA = Reflect.ownKeys(a), keysB = Reflect.ownKeys(b);
  if (keysA.length !== keysB.length) return false;
  return keysA.every(k => deepEqual(a[k], b[k]));
}
```

### 25. Observable (Simple)
```js
class Observable {
  constructor(subscribe) { this._subscribe = subscribe; }
  subscribe(observer) {
    const safeObserver = {
      next: x => observer.next?.(x),
      error: e => observer.error?.(e),
      complete: () => observer.complete?.()
    };
    const teardown = this._subscribe(safeObserver);
    return { unsubscribe: () => teardown?.() };
  }
  pipe(...operators) {
    return operators.reduce((obs, op) => op(obs), this);
  }
}
// Usage
const obs$ = new Observable(observer => {
  observer.next(1); observer.next(2);
  return () => console.log("unsubscribed");
});
```

### 26. Rate Limiter
```js
function rateLimiter(fn, limit, interval) {
  const queue = [];
  let running = 0;
  const process = async () => {
    if (running >= limit || queue.length === 0) return;
    running++;
    const { args, resolve, reject } = queue.shift();
    try { resolve(await fn(...args)); }
    catch (e) { reject(e); }
    finally {
      running--;
      setTimeout(process, interval);
    }
  };
  return (...args) => new Promise((resolve, reject) => {
    queue.push({ args, resolve, reject });
    process();
  });
}
```

### 27. Implement `instanceof`
```js
function myInstanceof(obj, constructor) {
  let proto = Object.getPrototypeOf(obj);
  while (proto) {
    if (proto === constructor.prototype) return true;
    proto = Object.getPrototypeOf(proto);
  }
  return false;
}
myInstanceof([], Array); // true
myInstanceof({}, Array); // false
```
