---
{"dg-publish":true,"permalink":"/cpp/cpp-const/"}
---

```cpp
//bit of a fake keyword, doesn't much do to the generated code
// it just implements rules as a promises to put some restrictions but it can be 
// easily overided

#include<iostream>
#include<string>

int main()
{
	const int MAX_AGE = 100;

	int* a = new int;
	const int* b = new int;// makes the data of b constant not the pointer
	int const* b = new int;// same as above
	int* const c = new int;// makes the pointer constant
	const int* const d = new int// both constant
	*a = 2;
	//a = (int*)&MAX_AGE; // override method one, don't do it //very studpid

	// you write to it you get a crash
	std::cout << *a << std::endl;




	delete[] a,b;

	std::cin.get();

}
```

- Usage example

```cpp

class Entity
{
private:
	int* m_X, m_Y; //only m_X is pointer
	mutable int var;
public:
	int Get() const
	{
		var = 2; // allows to modify 

		return m_X; // Why would you use this like above
		// Now the function cannot modify the m_X anymore
	}

	const int* const GetX() const
	{
		return m_X;//pointer cannot be modified,
		//the function cannot modify the pointer
		// and the data pointed by pointer cannot be modified
	}

	void SetX(int x)
	{
		m_X = x;
	}

	void GetXY()
	{
		return m_x;
	}
};

// Why would someone use const like as above

void PrintEntity(const Entity& e) //passing by ref. to avoid copy
// because we want to save perfomance penelty , the const keyword here will stop
// any function that is not declraed const from accessing the class 
{
	std::cout << e.GetX() << std::endl;
	std::cout << e.GetXY() << std::endl; // will give error
	//because the function does not promise no modification.


}
```

