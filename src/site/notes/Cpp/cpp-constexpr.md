---
{"dg-publish":true,"permalink":"/cpp/cpp-constexpr/"}
---

In C++, `constexpr` is a keyword used to declare that a variable or function can be evaluated at compile-time. It's used to indicate that a value or computation can be known by the compiler during compilation, allowing for optimizations and ensuring that certain operations are performed at compile-time rather than run-time. `constexpr` can be applied to variables, functions, and constructors.

Here are the primary use cases of `constexpr`:

1. **Compile-Time Constants**:
   You can use `constexpr` to declare variables as constants that must be known at compile-time. This allows the compiler to perform compile-time checks and optimizations. For example:

   ```cpp
   constexpr int square(int x) {
       return x * x;
   }

   int main() {
       constexpr int side = 5;
       constexpr int area = square(side); // Computed at compile-time
       return 0;
   }
   ```

   In this example, `side` and `area` are known at compile-time, and the `square` function is evaluated during compilation.

2. **Array Sizes**:
   `constexpr` can be used to define the size of arrays at compile-time, enabling the creation of fixed-size arrays:

   ```cpp
   constexpr int arraySize = 5;
   int myArray[arraySize];
   ```

3. **User-Defined Types**:
   You can use `constexpr` constructors to create user-defined types that are initialized at compile-time. This is especially useful for defining custom compile-time constants and small, efficient types.

   ```cpp
   struct MyStruct {
       constexpr MyStruct(int x) : value(x) {}
       int value;
   };

   constexpr MyStruct myInstance = MyStruct(42); // Initialized at compile-time
   ```

4. **Conditional Compilation**:
   `constexpr` can be used in conjunction with conditional compilation directives, such as `#if`, to enable or disable code blocks based on compile-time conditions:

   ```cpp
   constexpr bool enableFeature = true;

   #if enableFeature
   // This code block is compiled if enableFeature is true
   #endif
   ```

5. **Loop Unrolling**:
   `constexpr` can help in loop unrolling, where loops are optimized by the compiler by unrolling them into a sequence of instructions. This can improve performance for small loops.

   ```cpp
   constexpr int factorial(int n) {
       return (n <= 1) ? 1 : n * factorial(n - 1);
   }
   ```

6. **Template Metaprogramming**:
   `constexpr` is essential in template metaprogramming, where compile-time computations and type manipulations are performed using template parameters. It allows you to perform complex computations at compile-time to generate code tailored to specific types or values. #templateMetaProgramming #cppTemplates 

   ```cpp
   template <int N>
   struct Fibonacci {
       static constexpr int value = Fibonacci<N - 1>::value + Fibonacci<N - 2>::value;
   };

   template <>
   struct Fibonacci<0> {
       static constexpr int value = 0;
   };

   template <>
   struct Fibonacci<1> {
       static constexpr int value = 1;
   };
   ```

In summary, `constexpr` in C++ is a powerful feature that enables compile-time evaluation, constant expressions, and various compile-time optimizations. It is used to improve code efficiency, create compile-time constants, and support advanced metaprogramming techniques.