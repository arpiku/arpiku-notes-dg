---
{"dg-publish":true,"permalink":"/c/c-basics/"}
---

In C, you need to use the `struct` keyword before the `Point*` return type of the `makePoint` function because of the way C and C++ handle structure declarations and name resolution.

In C, when you declare a `struct` type like `struct Point`, the `struct` keyword becomes an integral part of the type name. That means you must use `struct Point` every time you refer to that type, including when declaring pointers to that type. This is why you use `struct Point*` as the return type of the `makePoint` function in C.

In contrast, C++ has a feature called "type name injection" that allows you to omit the `struct` keyword when referring to a struct type. This is a language feature designed for convenience and to make code more readable. In C++, you can simply use `Point*` as the return type of the `makePoint` function because the compiler automatically recognizes `Point` as referring to the `struct Point` type.

So, in summary:

- In C, you use `struct Point*` because the `struct` keyword is required to specify the type.
- In C++, you can use `Point*` because C++ automatically recognizes `Point` as the `struct Point` type due to type name injection.

This is one of the subtle but significant differences between C and C++ when it comes to working with structures.