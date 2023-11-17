---
{"dg-publish":true,"permalink":"/cpp/cpp-array/"}
---

### ChernoCpp
```cpp
//std::array is part of C++ Standard Template Library
// this is for array with predefined sizes and that are not going to grow



#include<iostream>
#include<array> // header file that is required

void PrintArr(const std::array<int, 5>& arr) // Here we know the size, but to pass is we will have to use template
{
	for (int i = 0; i < 5; i++)
	{
		std::cout << arr[i] << std::endl;
	}
}



int main()
{
	std::array<int, 5> arr; //from the template library
	arr[0] = 34;
	arr[3] = 123;

	int oldMethod[5]; // the older way to declare array
	// and it works identically

	// So why?!? 

	// The new features have to have something to offer in order for them to useful, otherwise the old school type of array
	// defining was not at all bad

	// as the std array is actually a class defination, it provides functions that may be useful when handling arrays
	// as shown in the code of the function return below
	PrintArr(arr);// One will get weird values for undefined indexes, thats because C++ does not initialize variables 
	// to zero like some other languages
	

	//In memory they are exactly the same, unlike vectors

	// std::array also have bounce checking, that is in case one assigns value to something like arr[6], the std::arr will stop you(debug mode)
	// in normal arrays, the code might run and write data into memory that is not owned by it.

	std::cin.get();

	//as there is almost no perfomance cost and added debugging features
	// use them when ever you can


}


```