---
{"dg-publish":true,"permalink":"/cpp/cpp-classes/"}
---

```cpp
#include<iostream>
#include<string>

class Entity
{
private:
	//Entity() {} to make contructor unaccessable

public:
	// also this Entity() = delete;

	Entity() {
		//Doesn't have a return type
		//by default it exist but the scope is empty unlike java and fortran one need to
		// specifically initialize variables

		X = 0; Y = 0; 
		std::cout << "created entity" << std::endl;
	}
	float X, Y;
	void print()
	{

		std::cout << X << " " << Y << std::endl;
	}

	~Entity() {
		std::cout << "deleted entity" << std::endl;
		// should be used when cleaning memory when you have manually acllocated memory

		
	}
	virtual std::string GetName2() = 0; // PURE VIRTUAL FUNCTION

	virtual std::string GetName() { return "entity"; }

};

class player : public Entity
{
private:
	std::string m_Name;
public:
	player(const std::string& name)
		: m_Name(name) {}
	std::string GetName() override { return m_Name; } //'override' to make code //readable
	// V. functions do have some perfomance penelties but usually they are fine

	// Interface or pure functions

	

	
};

void PrintName(Entity* entity) {
	std::cout << entity->GetName() << std::endl;
}

int main()
{
	//Entity e; // one can avoid such initialization by the above.
	//Entity* e = new Entity();
	//std::cout << e.X << e.Y << std::endl;
	//e.print();
	//PrintName(e);

	//Pure virtual function will also stop intintiation of class untill the pure virtual
	// functions are given proper defination

	player*  p = new player("arpit");
	PrintName(p);

	std::cin.get();
	}

	



}

```