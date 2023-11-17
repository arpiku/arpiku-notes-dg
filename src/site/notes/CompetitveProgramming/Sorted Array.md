---
{"dg-publish":true,"permalink":"/competitve-programming/sorted-array/"}
---

`std::rotate` is something that cabe useful here:

-  `std::rotate`  `std::lower_bound`   #cppSTL #sorting #searching
- `std::rotate` can be implemented like the following:
```cpp
template<class ForwardIt>
constexpr // since C++20
ForwardIt rotate(ForwardIt first, ForwardIt middle, ForwardIt last)
{
    if (first == middle)
        return last;
 
    if (middle == last)
        return first;
 
    ForwardIt write = first;
    ForwardIt next_read = first; // read position for when "read" hits "last"
 
    for (ForwardIt read = middle; read != last; ++write, ++read)
    {
        if (write == next_read)
            next_read = read; // track where "first" went
        std::iter_swap(write, read);
    }
 
    // rotate the remaining sequence into place
    rotate(write, next_read, last);
    return write;
}
```

```
- This can be used to right or left rotate an array by any distance.
- Following is an example code to do so.
```cpp
#include <bitset>
#include <iostream>
#include <unordered_map>
#include <vector>

auto print = [](auto const remarks, auto const &v) {
  std::cout << remarks;
  for (auto &i : v)
    std::cout << i << ' ';
  std::cout << '\n';
};

int main() {
  std::vector<int> v = {0, 1, 2, 4, 5, 6, 7};
  print("org:", v);
  std::rotate(v.begin(), v.begin() + 7, v.end());
  print("rotation at idx 3:", v);
  std::rotate(v.rbegin(), v.rbegin() + 7, v.rend());
  print("rotation at idx 3:", v);
  return 0;
}
```

- `std::lower_bound` can be implemented like this 
```cpp
template<class ForwardIt, class T>
ForwardIt lower_bound(ForwardIt first, ForwardIt last, const T& value)
{
    ForwardIt it;
    typename std::iterator_traits<ForwardIt>::difference_type count, step;
    count = std::distance(first, last);
 
    while (count > 0)
    {
        it = first; 
        step = count / 2; 
        std::advance(it, step);
 
        if (*it < value)
        {
            first = ++it; 
            count -= step + 1; 
        }
        else
            count = step;
    }
 
    return first;
}
```
- `The above return Iterator pointing to the first element in the range \[first, last) such that element < value (or comp(element, value)) is false, or last if no such element is found.`

- The problem in of itself isn't that difficult but introduces some silly mistakes that I have made 
```cpp
    int getK(std::vector<int>& nums, int l, int r) {
        if(l > r)
            return -1;
        int mid = l + (r-l)/2;
        if(nums[mid+1] < nums[mid] || mid == nums.size()-1) {
            return mid;
        }
        if(nums[mid] > nums[r])
            return getK(nums,mid,r);
        else
            return getK(nums,l,mid);  
    }
```
- The above algorithm seems fine, but is wrong, as we are not incrementing anything, the code will get stuck in loop at the (l>r) condition.
- The pivot point to return is mid+1 not mid;
- The correct implementation would be as follows:
```python
def find_pivot(arr):
    left, right = 0, len(arr) - 1

    while left < right:
        mid = (left + right) // 2

        # Check if the mid element is greater than the element to its right
        if arr[mid] > arr[mid + 1]:
            return mid + 1  # The pivot point is at index mid + 1

        # If the mid element is greater than the rightmost element, search in the right half
        if arr[mid] > arr[right]:
            left = mid + 1
        else:
            right = mid

    # If the loop ends without finding a pivot point, the array is not rotated
    return 0
```
- The correct algorithm would be 
```cpp
    int getK(vector<int>& nums, int l , int r) {
        if(l >= r)
            return 0;
        int mid = l + (r-l)/2;
        if(nums[mid+1] < nums[mid])
            return mid+1;
        if(nums[mid] > nums[r]) {
            return getK(nums,mid+1,r);
        } else {
        return getK(nums,l,mid);
        }
    }
```

- However the following code also doesn't work
```cpp
class Solution {
public:
    int getK(vector<int>& nums, int l , int r) {
        if(l >= r)
            return 0;
        int mid = l + (r-l)/2;
        if(nums[mid+1] < nums[mid])
            return mid+1;
        if(nums[mid] > nums[r]) {
            return getK(nums,mid+1,r);
        } else {
        return getK(nums,l,mid);
        }
    }
    int search(vector<int>& nums, int target) {
        if(nums.size() == 1)
            if(nums[0] == target)
                return 0;

        int l = 0;
        int r = nums.size()-1;
        int k = getK(nums,l,r);

        // if(nums[k] == target)
        //     return k;
        
        auto iter = nums.begin();
        if(target >= nums[0] || k == r) {
            int i = std::lower_bound(iter,iter+k,target) -iter;
            if(i == k || nums[i] !=target) return -1;
            return i;
        } else {
            int i = std::lower_bound(iter+k,iter+r+1,target) - iter;
            if(i == r+1 || nums[i] != target) return -1;
            return i;
        }
        return -1;
    }
};
```




