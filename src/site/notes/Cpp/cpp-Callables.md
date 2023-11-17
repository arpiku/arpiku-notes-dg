---
{"dg-publish":true,"permalink":"/cpp/cpp-callables/"}
---

- #callables 
In C++, callables are entities that can be called like functions. There are several types of callables in C++:

1. **Functions**: Traditional functions, both free functions and member functions, are callable in C++. Here's an example of a free function:

    ```cpp
    int add(int a, int b) {
        return a + b;
    }
    ```

2. **Function Pointers**: You can create pointers to functions, and these function pointers are callable.

    ```cpp
    int (*functionPtr)(int, int) = &add;
    int result = functionPtr(2, 3); // Calls the function through the pointer
    ```

3. **Lambda Functions**: Lambda functions allow you to define small, anonymous functions directly in your code. They are callable objects.

    ```cpp
    auto lambda = [](int a, int b) {
        return a + b;
    };
    int result = lambda(2, 3); // Calls the lambda function
    ```

4. **Function Objects (Functors)**: Functors are objects of a class that define the `operator()` function, allowing instances of the class to be callable.

    ```cpp
    struct MyFunctor {
        int operator()(int a, int b) {
            return a + b;
        }
    };

    MyFunctor functor;
    int result = functor(2, 3); // Calls the functor
    ```

5. **Member Function Pointers**: You can also create pointers to member functions of classes, and these member function pointers are callable.

    ```cpp
    class MyClass {
    public:
        int multiply(int a, int b) {
            return a * b;
        }
    };

    MyClass obj;
    int (MyClass::*memberFunctionPtr)(int, int) = &MyClass::multiply;
    int result = (obj.*memberFunctionPtr)(2, 3); // Calls the member function through the pointer
    ```

6. **std::function**: `std::function` is a versatile callable wrapper that can hold and invoke callable objects of various types, including functions, lambdas, functors, and more.

    ```cpp
    std::function<int(int, int)> func = [](int a, int b) {
        return a + b;
    };
    int result = func(2, 3); // Calls the std::function
    ```

7. **std::bind**: `std::bind` allows you to create callable objects by binding arguments to functions or function pointers.

    ```cpp
    #include <functional>

    int add(int a, int b) {
        return a + b;
    }

    auto boundFunction = std::bind(add, 2, std::placeholders::_1);
    int result = boundFunction(3); // Calls add(2, 3)
    ```

These are the common types of callables in C++. Each has its own use cases and advantages, depending on the specific requirements of your code.

Certainly! Here are some benefits and drawbacks of the different types of C++ callables:

1. **Functions**:
   - **Benefits**:
     - Simple and straightforward.
     - Efficient function calls.
   - **Drawbacks**:
     - Limited in terms of encapsulation and reusability.

2. **Function Pointers**:
   - **Benefits**:
     - Can be used to create callbacks and to select functions at runtime.
   - **Drawbacks**:
     - Pointers can be error-prone and less expressive.
     - Limited support for capturing state or data.

3. **Lambda Functions**:
   - **Benefits**:
     - Concise syntax for defining small, inline functions.
     - Can capture variables from their enclosing scope.
   - **Drawbacks**:
     - Limited to simple tasks and cannot be reused across different scopes.
     - Cannot have complex member function pointers.

4. **Function Objects (Functors)**:
   - **Benefits**:
     - More expressive and reusable than simple functions.
     - Can maintain internal state.
   - **Drawbacks**:
     - Requires defining a separate class for each functor, which can be verbose.

5. **Member Function Pointers**:
   - **Benefits**:
     - Enable access to member functions of classes.
     - Useful for implementing callback systems in object-oriented programming.
   - **Drawbacks**:
     - Complex syntax for defining and using them.
     - Limited to member functions within the same class.

6. **std::function**:
   - **Benefits**:
     - Provides a flexible way to store and invoke callable objects of various types.
     - Supports function pointers, lambdas, functors, and more.
   - **Drawbacks**:
     - Slightly less efficient than direct function calls or function pointers due to the type erasure overhead.

7. **std::bind**:
   - **Benefits**:
     - Allows you to create callable objects with pre-bound arguments.
     - Useful for partial function application and creating adapted function objects.
   - **Drawbacks**:
     - Requires the `<functional>` header.
     - Less intuitive than lambda expressions for simple cases.

The choice of which callable to use depends on the specific requirements of your code. Simple functions are suitable for straightforward tasks, while functors or lambdas can be used for more complex logic. Function pointers and member function pointers are valuable when dealing with callback systems or dynamic function selection. `std::function` and `std::bind` offer greater flexibility but come with some performance overhead.

Consider the trade-offs in terms of readability, reusability, and performance when choosing the appropriate callable for a particular task in your C++ program.