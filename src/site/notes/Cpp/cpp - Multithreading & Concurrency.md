---
{"dg-publish":true,"permalink":"/cpp/cpp-multithreading-and-concurrency/"}
---


## Concurrency Vs Parallelism:
- **Concurrency:** Means doing things concurrently, maybe switching between them.
	-  Like chopping onions
	- Stirring the food.
- **Parallism** : Doing things simultaneously
	- Stirring the food.
	- Talking on phone.

In broad strokes, parallel programming is a hardware problem while concurrency is more of a software problem, i.e hyper-threading etc.
We didn't have concurrency in the standard till #cpp11 , we had third party libraries but that means the standard had no governance over how multithreading should be done in C++.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 3m20s].png]]

The above slide shows that we need **data mutation protection**, when one thread is accessing it, other threads are not allowed to modify that data.
**Synchronisation** is important to know that when you are not looking at the variable while the thread is modifying it.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 5m11s].png]]
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 6m02s].png]]

Interesting: The level of abstraction leads to a situation where the hardware has a say in how the code will work internally, hence the actual code you write may not represent the actual way things are happening on the hardware.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 6m52s].png]]

So in C++, we rely on the gurantees provided by the standard and use them to properly design our program.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 11m06s].png]]
- So what happens is that our main thread of execution get a branch with the new thread B, 
*.join()* basically blocks thread A until threadB is done, so thread A is waiting for thread B to finish and only after word continues it's execution.
- **Thread do not have ways to get explicit results**, we can't ask a thread it's status, because joining with the child thread is a syncronization operation.
- Hence the above code, works, as the join makes sure that everyone is at the same page before continuing.
```cpp
#include<iostream>
#include<thread>
#include<cstdio>
int main() {
	//The memory can be read at the same time but even if a single
	//thread tries to access the location and to write then we 
	//have a data race condition.



	using SC = std::chrono::stead_clock;
	auto deadline = SC::now() + std::chrono::seconds(10);

//	int counter = 0;
****	std::atomic<int> counter = 0; // This fixes everything, now ****
	// are not in a datarace condition but a logic race condition.
	std::thread t1 = std::thread([&]() {
		while(SC::now() < deadline)
			printf("B: %d\n", ++counter);
		});
	while(SC::now() < deadline)
		printf("A: %d\n", ++counter);
	t1.join();


	return 0;
}

```

![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 1104x621 - 15m11s].png]]

In #cpp11 we could call the functions set up a thread as follows:
```cpp
std::thread t1 = std::thread([]() {
long int sum = 0
for(int i = 0; i<1000000; i++)
	sum+=i;
return sum;
})
```
- **The above code starts execution immediately.**

- Also the example shows you can pass a callable object into the constructor of a thread.

- **Once the thread does it's job it stops and becomes joinable**.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 1104x621 - 16m30s].png]]
- In the above code the compiler might notice that the boolean value will never change by anything thread B can do, and it may hoist thread B out of a loop, resulting in a undefined behaviour.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 1104x621 - 18m42s].png]]
- So what is this `std::mutex` thingy?
	- It is like a key, an authority over who gets to do the things first, it's like a permission required to do anything.
	- As thread A locks the mutex, when thread B gets going and it also wants to deal with the mutex, it has to wait, until thread A is done.
	- The above example as **atypical** use of std::mutex, it is just an example following are some more real world use case.
## Example of a data race condition:
```cpp
using SC = std::chrono::steady_clock;
auto deadline = SC + std::chrono::seconds(10);
int counter = 0;
std::thread threadB = std::thread([&](){
while(SC::now() < deadline) 
	printf("B : %d\n", ++counter);
});
while(SC::now() < deadline) 
	printf("A: %d\n", ++counter);
threadB.join();
```
- The above is a datarace, because both thread A and thread B are trying to update the same variable without sync.
- 

The following magically fixes everything
```cpp
using SC = std::chrono::steady_clock;
auto deadline = SC + std::chrono::seconds(10);
std::atomic<int>  counter = 0;
std::thread threadB = std::thread([&](){
while(SC::now() < deadline) 
	printf("B : %d\n", ++counter);
});
while(SC::now() < deadline) 
	printf("A: %d\n", ++counter);
threadB.join();
```

![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 1104x621 - 21m23s].png]]
- Now we are going to protect some data.
- 
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 1104x621 - 22m35s].png]]
**- Is this a bad code because we are not using a unique clk?
	- YES**
- The size() is a read, but that is not right, imagine a situation where got `getToken()` and `numTokenAvailable()` gets called.
- So protected write and unprotected reads can lead to data race.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 1104x621 - 24m38s].png]]
- When exceptions are called they break the execution, you never get a return. 
- This results in a bug, as the mutex is locked and the exception blocks the code, mutex never gets unlocked.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 1104x621 - 25m33s].png]]
- [[Cpp/cpp-RAII principles\|cpp-RAII principles]] [[Cpp/cpp-CTAD\|cpp-CTAD]]
- `std::lock_guard` will automatically unlock the mutex in case of an exception, by running its destructor.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 26m25s].png]]
- Understanding for what a resource is in Cpp
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 1104x621 - 27m51s].png]]
- **This all works fine!**
- The mutex can be returned by a function, can be prematurely cleaned up just like any other unique_ptr.
- Suggestions:
	-  If you are not going to pass it around use **lock_guard**, if you are going to pass it around use **unique_lock**.

![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 1104x621 - 28m38s].png]]
- #cpp17 
- In Cpp17 we have a better version of lock_guard, where it can take multiple mutexs.


![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 1104x621 - 33m23s].png]]
- `std::condition_variable`
- In this example the thread needs to get the token it is done with to other threads that might be "wating" on it.
- So we put the token into the vector.
- Unlock the mutex
- and notify_one (This will notify one waiting thread, there also notify_all, which will wake all of them up.)
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 34m57s].png]]
- **It will atomically drop the lock and go sleep**, i.e one cannot get in between the threads sleeping and waiting while the other threads are waiting.
- `cv_wait` has the lock, it will relinquish the lock and go sleep instantly.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 36m43s].png]]
- We could have used `std::lock_guard` here, that would make lk.unlock() unnecessary, and the notify_one line can be either above or below the unlock, semantically it doesn't matter.

![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 37m07s].png]]

![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 39m19s].png]]
- The singleton makes sure that each thread gets one chance to construct the object, it succeds it opens up the object to all the threads.
- If not it unblocks only one of them.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 40m47s].png]]
- The above can result in a data race condtion.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 41m15s].png]]
- This is alright but not performant, you have to wait for the lock and check it everytime.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 41m21s].png]]
- `std::once_flag` you can just call `std::call_once` on it.

![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 42m55s].png]]

![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 43m56s].png]]
- #cpp17
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 44m45s].png]]
- Here the counter goes up and down till the chips are empty, then it blocks.
- Just releasing without acquiring is UB. #cpp20
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 45m44s].png]]
- #cpp20
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 46m18s].png]]
- #cpp20
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 47m06s].png]]
- `std::barrier` is basically a resettable latch.
### What is the difference between`.join()` and `std::latch`
- `.join()` blocks the thread that calls `.join()`, while the other thread finished
- A latch is called from a thread that is running, and it blocks it until others get there as well.
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 48m45s].png]]
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 49m13s].png]]
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 50m33s].png]]
- This code automatically handles, no matter what order the functions get called in.
- **No sync primitive is copyable.** 
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 51m35s].png]]
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 51m35s].png]]
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 53m58s].png]]

![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 53m58s].png]]
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 57m58s].png]]

![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 1h00m27s].png]]
![[CppCon - Back to Basics Concurrency - Arthur O'Dwyer - CppCon 2020 [F6Ipn7gCOsY - 966x543 - 1h00m51s].png]]



