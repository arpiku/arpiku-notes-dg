---
{"dg-publish":true,"permalink":"/cpp/cpp-cherno-mutithreading/"}
---

```cpp
// Modern processors have multiple cores and can run many tasks simultaneously
// a piece of code that executes 'independently' is called a thread
// here the word 'independently' does not mean that it cannot communicate with other threads
// one can have multiple threads running simultaneously working on different tasks, they can be 
// completely unaware of what is going on with other threads or they can wait for some other thread
// to get something done first or request for some data or maybe send some data etc.

#include<iostream>
#include<thread> // Header required to use multiple threads
#include<chrono>

struct Timer
{
	Timer()
	{
		std::chrono::time_point < std::chrono::steady_clock> start, end;
		std::chrono::duration<float> duration;
	}
};


void for_loop() // This function will be executed on a different thread than our main function
{
	using namespace std::literals::chrono_literals;
	for (int i = 0; i < 10; i++)
	{
		std::cout << "This is thread 1" << std::endl;
	}
}

void while_loop()
{
	int i = 0;
	while (i<5)
	{
		std::cout << "This is thread 2" << std::endl;
		i++;
	}
}
void Detach_thread()
{
	// Do something
}

void Example(int i)
{
	std::cout << i << std::endl;
}
int main() // By default our main code executes on a single 'main' thread
{

	//std::thread thread1(for_loop); // Declaring a thread with a thread name, this will also launch the
	// thread
	//std::thread thread2(while_loop);

	//thread1.join(); // After complition of code this statement stops the threads
	//thread2.join(); // hence clearing the cpu for other tasks

	// The above code will print varying result depending on which thread got the access to the 
	// out buffer first, hence parallel programming needs to be done in such a way to avoid conflicts between
	// threads, basically imagine if two threads are updating the same variable, which value would actually get 
	// stored in the end?
	// Also there are many tasks that need to handled in a serial manner and need a  fast CPU core for faster
	//  results rather than many threads.

	//----Avoiding conflicts between threads----------//

	// Making a thread completely independent//
	
	//std::thread (Detach_thread).detach(); // This statement will make the thread completely independents, and it can't 
			// be stoped  or  joined, only terminated as the program ends
	// Thread_Name.joinable() will return a boolean depending on whether a thread can be joined or not

	// Thread--id
	// Each thread has a unique id, this can be found using 'thread::get_id'

	auto id = std::this_thread::get_id;
	std::cout << id << std::endl;

	std::thread threads[2]; // Default thread
	for (int i = 0; i < 2; i++)
	{
		threads[i] = std::thread(Example,i);
		threads[i].join(); // Important to join the joinable threads
	}



	std::cin.get();
	
}
```