---
{"dg-publish":true,"permalink":"/basic-shit/basic-coding/"}
---

### Getting sum of series for n >> 1:
- The simple stuff
```cpp
long long nsum(int n ) {
return n * ((n + 1) / 2);
}
```
- The above code will not work for really large integer inputs.
```cpp
long long nsum(int n) {
  if (n % 2 == 0)
    return (n + 1) * (n / 2);
return n * ((n + 1) / 2);
}
```
- The following is the correct solution.
```cpp
long long nsum(int n) {
long long sum = (static_cast<long long>(n)*(n+1))/2;
return sum;
}
```

## Printing alternate elements of an array
```cpp
void print(int ar[], int n)
{
    for(int i = 0; i<n; i+=2) {
            std::cout << ar[i] << " ";
    }
    
}
```


### Print 1 to N without Loop
```cpp
    void printNos(int N)
    {
        if(N == 0)
            return;
        printNos(N-1);
        std::cout << N << " ";
        
    }
```
 - This introduces an interesting property of recursion, the following code looks similar but will result in the numbers being print N to 1;
 ```cpp
     void printNos(int N)
    {
        if(N == 0)
            return;
        std::cout << N << " ";
        printNos(N-1);
        
    }
```

### Print Pattern
```cpp
void printPat(int n)
{
    int j0 = 1;
    while(j0<=n) {
    for(int i = n; i>=1; i--) {
        for(int j = n; j>=j0; j--) {
            std::cout<< i << " ";
        }
    }std::cout << "$";j0++;
    }
}
```

## Count Digits

```cpp
    int evenlyDivides(int N){
        int org_N = N;
        int dividerCount = 0;
        while(N) {
            int digit = N%10;
            if(digit != 0) //Look how this check is really important
                if(org_N%digit == 0)
                    dividerCount++;
            N /= 10;
        }
        return dividerCount;
    }
```

## Finding Median
```cpp
		int find_median(vector<int> v)
		{
		    int n = v.size();
		    std::sort(v.begin(),v.end());
		    int ans = 0;
		    if(n%2 == 0)
		        ans = (v[(n-1)/2]+v[n/2])/2;
		    else
		        ans = v[n/2];
		  return ans;
		   
		}
```

## isBinary
- We some use of boolean algebra in this problem
```cpp
bool isBinary(string str)
{
   for(int i = 0; i<str.size();i++) {
       if(str[i] != '0' && str[i] != '1') {
            return false;
        // return false;
       }
   }
   return true;
}
```
- The above code and the code below are logically same, look how the boolean statement changes
```cpp
bool isBinary(string str)
{
   for(int i = 0; i<str.size();i++) {
       if(str[i] == '0' && str[i] == '1') {
            continue;
        return false;
       }
   }
   return true;
}
```

### Count smaller elements
```cpp
int countOfElements(int arr[], int n, int x) 
{   
    int count =0;
    for(int i = 0; i<n; i++) {
        if(arr[i] > x) //if(arr[i] >= x) This will be wrong
            break;
        count++;
    }
    return count;
    
}
```


### Find Index
```cpp
    vector<int> findIndex(int a[], int n, int key)
    {
        int l = 0;
        int r = n-1;
        while(r>=l) {
            if(a[r]==key && a[l]==key)
                return {l,r};
            if(a[r] != key)
                r--;
            if(a[l] != key)
                l++;
        }
        return {-1,-1};
    }
```
- The Kid version of [[twoPointers\|twoPointers]]

### Remove space
```cpp
    string modify (string s)
    {
        std::string res = "";
        for(int i = 0; i<s.size(); i++) {
            if(std::isspace(s[i]))
                continue;
            res += s[i];
        }
        return res;
    }
```

## GCD of two integers
- [[math\|math]] [[numberTheory\|numberTheory]]
```cpp
class Solution
{
	public:
    int gcd(int A, int B) 
	{ 
	    int r;
	    while(A%B > 0) {
	        r = A%B;
	        A = B;
	        B = r;
	    }
	    return B;
	      
	} 
};
```


## Implementing a print function in C++ for a vector
- #niceTidbit
```cpp
auto print = [](auto const remark, auto const& v)
{
    std::cout << remark;
    for (auto n : v)
        std::cout << n << ' ';
    std::cout << '\n';
};
```
