# familial
TypeScript-first, composable, reusable functions and constants for using vanilla APIs in the best possible way.

# Why
Every month, the JavaScript, and TypeScript, community is flooded by new libraries that re-implement familiar APIs. There's always an incremental improvement, and sometimes the convenience is great. But the downside is, these libs often abstract a common, well-known library away and replace it with a new one. Many are simple boilerplate functions.

While in JavaScript, you'd always want one function that is fully-documented by itself and does everything in your use-case, TypeScript opens a new door. We can define functions that output a set of arguments for a familiar function (e.g. it returns `Parameters<typeof familiarFunction>`), or common constants (e.g. defined as Pick<Parameters<typeof familiarFunction>, 'some' | 'common' | 'boilerplate' | 'parameters'>). This approach could've been a nuisance in pure JS, but our editors are all good at telling us what we need to do to use a TypeScript interface.

# What
The most commonly re-implemented API in my world is `fetch`. `fetch` was lauded as a huge improvement when it came out, but there have been so many special cases around it that it gets reinvented many times a year. Bigger projects rarely even use it directly, they'll wrap around it. So `fetch` continues to endure.

We don't like seeing:
```js
const res = await fetch("//example.com/product", {
  method: "POST",
  body: JSON.stringify({ name: "pants", price: 123 }),
  headers: {
    "content-type": "application/json",
  }
});

if (!res.ok) {
  throw new Error(`Failed with ${res.statusText}`);
}

const json = await res.json();
```
over and over, so we write a new lib that looks like old jQuery.
```js
import myFetcher from "myFetcher";

const json = await myFetcher.post("//example.com/product", { json: { name: "pants", price: 123 } }.json();
```
and celebrate over how much easier it is.

We've saved a bit of boilerplate, but now have `myFetcher` to think about. Even devs that don't know every situation for `fetch` are at least familiar with it. Even if they like `myLib` more, they don't know it right away.

We can get the same savings by composing the existing APIs from new functions that return familiar types. Nobody is guessing over the behaviour, because the behaviour hasn't changed. Hover your mouse (or however your editor does it) and see the types that return, pass them into the functions everyone knows:

```
import { resJson, postJson } from "familial";

const json = await fetch("//example.com/product", postJson({ name: "pants", price: 123 })).then(resJson);
```

We're still calling `fetch`, and `postJson` gives a return based on the type of `fetch`'s second arg. It's as clear and concise as the `myLib`, but as familiar as `fetch`. `familial` can be used at your discretion, so if you don't want error handling, don't include it. It can be easily migrated to (or from...).
