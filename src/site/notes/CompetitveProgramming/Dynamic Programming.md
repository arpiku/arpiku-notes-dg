---
{"dg-publish":true,"permalink":"/competitve-programming/dynamic-programming/"}
---

# Begining
#### Fibonnaci series
#recursion method
~~~python
def fib(x):
	if(x <= 2) return 1
	return fib(n - 1) + fib(n-2)
~~~
for n>>1 this is really inefficient.

![Linux_GNU/img/snapshot.jpg](/img/user/Linux_GNU/img/snapshot.jpg)

Some more examples 
![snapshot_2.jpg](/img/user/CompetitveProgramming/img/snapshot_2.jpg)
![snapshot_4.jpg](/img/user/CompetitveProgramming/img/snapshot_4.jpg)

### Memoization
Simple techinque to store the reocurring calculations to avoid calculating them again.
~~~python
fib_memo = {0:0, 1:1, 2:1}
def fib(n):
	if n in fib_memo:
		return fib_memo[n]
	fib_memo[n] = fib(n-1) + fib(n-2)
	return fib_memo[n]
fib(50)
~~~

![snapshot_5.jpg](/img/user/CompetitveProgramming/img/snapshot_5.jpg)

#### gridTraveler
![snapshot_6.jpg](/img/user/CompetitveProgramming/img/snapshot_6.jpg)

~~~python

memo = {}
def gridtraveler(m,n):
	const key = str(m) + ',' + str(n)
	if(key in memo) return memo[key]
	if(m|n < 1):
		return 0
	if(m == 1 && n == 1):
		return 1
	memo[key] = gridtraveler(m-1,n) + gridtraveler(m,n-1)
	return memo[key]
~~~

![snapshot_7.jpg](/img/user/CompetitveProgramming/img/snapshot_7.jpg)

#### Alvin's technique
1. Make it work!:
	1. visualize the problem as a tree
	2. implement the tree using recursion
	3. test it!
2. Make it efficient!:
	1. add a memo object
	2. add a base case to the memo object
	3. store and return values from memo object



#### canSum
BruteForce
~~~python

def cs(targetsum, numbers):
	if(targetsum < 0) return False
	if(targetsum == 0) return True

	for n in numbers:
		remainder = targetsum - n
		if((cs(remainder, numbers))):
			return True
	return False

print(cs(300,[7,14])) #very slow
print(cs(7, [2,3]))
~~~
![snapshot_8.jpg](/img/user/CompetitveProgramming/img/snapshot_8.jpg)


Memoized
~~~python
memo = {}

def cs(targetsum, numbers):
	if(targetsum in memo):
		return memo[targetsum]
	if(targetsum < 0) return False
	if(targetsum == 0) return True

	for n in numbers:
		remainder = targetsum - n
		if((cs(remainder, numbers))):
			memo[targetsum] = True
			return True
	memo[targetsum] = False
	return False

print(cs(300,[7,14])) #very slow
print(cs(7, [2,3]))
~~~

![snapshot_9.jpg](/img/user/CompetitveProgramming/img/snapshot_9.jpg)

#### howSum
Brute force
~~~python
def hs(targetsum, numbers):
	if (targetsum == 0):
		return []
	if (targetsum < 0):
		return None
	for n in numbers:
		remainder = targetsum - n
		result = hs(remainder,numbers)
		if(result != None):
			return result + n
		
	return None	

~~~

Memoized
~~~python

memo = {}

def hs(targetsum, numbers):
	if(targetsum in memo) return memo[targetsum]
	if (targetsum == 0):
		return []
	if (targetsum < 0):
		return None
	for n in numbers :
		remainder = targetsum - n
		result = hs(remainder,numbers)
		if(result != None):
			memo[targetsum] = result + [n]
			return memo[targetsum]
	memo[targetsum] = None
	return None	
~~~

![snapshot_10.jpg](/img/user/CompetitveProgramming/img/snapshot_10.jpg)

#### bestSum
brute force
~~~python

bestsum = []
def bs(targetsum, numbers):
	if(targetsum == 0)
		return []
	if(targetsum < 0):
		return None
	shortestsum = None
	for n in numbers:
		remainder = targetsum - n
		result = bs(remainder, numbers)
		if(result != None):
			arr = result + [n]
			if(shortest == None ||len(arr) < shortestsum):
			shortsum = arr
		
	return shortestsum
~~~
