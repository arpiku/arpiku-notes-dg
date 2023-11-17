---
{"dg-publish":true,"permalink":"/cpp/cpp-auto/"}
---


```cpp
// In C++, you can make the code automatically deduce the datatype of a variable
// instead of always defining the data type of some new variable to which we are passing some value, we can just
// let C++ figure it out

// Again its not something one should go overboard with

#include<iostream>
#include<vector>
#include<string>


std::string GetTruth()
{
	return "CppIsAwesome";
}

//char* GetTruth2() // decomment this to understand the errors
//{
//	return "Cpp";
//}

int main()
{
	int a = 5;
	auto b = a;
	// One does not need existing variables for this
	auto example = "Arpit"; // string
	auto example2 = 5L; //Long
	auto example3 = 3.3f; // float
	auto example4 = nullptr;
	auto example5 = 'a'; 


	// so this sort makes C++ 'weakly typed'(where you don't always specify datatype) language, (Javascipts fans?),
	//it means one could get away without ever defining  
	// the datatypes

	// So why do we even use datatype?!?

	auto Truth = GetTruth();
	//auto Truth = GetTruth2();

	// Imaginge if the function was actually was only one and only the return type was changed
	// On the API side of things the user won't be able to realize that
	// this means that 
	Truth.size(); // will just fail (again if the name didn't have '2' in it)
	// So even though auto made things easier it also added some mystery to the code which can turn into a nightmare
	// So if the data type was defined the error will just clear everything up
	// So don't use auto when you want to know the type of variable
	// Without it code becomes more understandable if not much more readable.

	// Other important use
	std::vector<std::string> fruits;
	fruits.push_back("Apple");
	fruits.push_back("Tomato"); // facts!
	for(std::vector<std::string>::iterator it = fruits.begin(); //look into cpp-iterators' folder
		it !=fruits.end(); it++) // but basically this can be replaced by a simple for loop instead of 'iterator'
	{
		std::cout << *it << std::endl;

	}

	// Look at the monstority above called a for loop, we can use auto keyword to simpify things qutie a bit
	for (auto it = fruits.begin(); //look into 'iterators' folder
		it != fruits.end(); it++) // but basically this can be replaced by a simple for loop instead of 'iterator'
	{
		std::cout << *it << std::endl;

	}

	// it can be used with references auto&


	std::cin.get();

}
```