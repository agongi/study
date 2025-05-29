# Transpiler & Polyfill
```
https://javascript.info/polyfills
```

## Transpiler
It can parse modern code and rewrite it using older syntax, so that it’ll also work in outdated engines.

```tsx
// before running the transpiler
height = height ?? 100;

// after running the transpiler
height = (height !== undefined && height !== null) ? height : 100;
```


> Babel, TypeScript, and SWC (used in next.js with minify)

## Polyfill
New language features may include not only syntax constructs and operators, but also built-in functions.

For example, Math.trunc(n) is a function that “cuts off” the decimal part of a number, e.g Math.trunc(1.23) returns 1.

In some (very outdated) JavaScript engines, there’s no Math.trunc, so such code will fail.

```tsx
if (!Math.trunc) { // if no such function
  // implement it
  Math.trunc = function(number) {
    // Math.ceil and Math.floor exist even in ancient JavaScript engines
    // they are covered later in the tutorial
    return number < 0 ? Math.ceil(number) : Math.floor(number);
  };
}
```

> [next.js](https://nextjs.org/docs/architecture/supported-browsers#polyfills) 에서도 core-js 을 통해 polyfill 제공
