---
{"dg-publish":true,"permalink":"/cpp/cpp-dynamic-arrays/"}
---

```cpp
#include<iostream>
#include<vector>

// Dynamic Arrays, for this vector, we need to get used to standard cpp library
// For this we will be using the standard template library
// the whole library is templated so the datatype the container contains is on the
// user to decide, don't need to completely understand template, you just have to provide
// basic type for these structures,

// Vectors are not mathematical vector, its  more like a set, it does not enforce some 
// type to its elements, it is dynamic, when you create it the size can be undefined, it 
// will automatically update

struct Vertex
{
	float x, y, z;
};

void Function(const std::vector<Vertex>& vertices)
{}

std::ostream& operator<<(std::ostream& stream, const Vertex& vertex)
{
	stream << vertex.x << ", " << vertex.y << ", "<<vertex.z;
	return stream;
}

int main()
{
	Vertex* vertices1 = new Vertex[5];
	//vertice[10];// Not working

	std::vector<Vertex> vertices;  // Vertex optimal as they are linearly stored, why not use pointer
	vertices.push_back({ 1,2,3 });
	vertices.push_back({ 4,5,6 });

	for (Vertex& v : vertices)
		std::cout << v << std::endl;

	vertices.erase(vertices.begin() + 1); // Have to take in itertor

	for (Vertex& v : vertices)
		std::cout << v << std::endl;

	vertices.clear();


	std::cin.get();


}
```