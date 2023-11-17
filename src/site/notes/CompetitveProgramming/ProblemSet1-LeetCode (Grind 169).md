---
{"dg-publish":true,"permalink":"/competitve-programming/problem-set1-leet-code-grind-169/"}
---

The Problem Set is Week 1 from[ Grind 169]("https://www.techinterviewhandbook.org/grind75?hours=40&weeks=26")

## Two Sum
- [[Data_Structures/array\|array]]  [[Data_Structures/hashMap\|hashMap]]
- The problem is rather simple when going with a naive solution, using a O(n^2) solution by lopping through all the possible combinations and returning the one that is meets our requirement.
- TO(n^2), SO(1)
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        if(nums.empty()) 
            return {};
        for(int i = 0; i<nums.size(); i++) 
            for(int j = i+1; j<nums.size(); j++)
                if(nums[i]+nums[j]==target)
                    return {i,j};
        return {};
        
    }
};
```
- The solution is rather slow when n >> 1.
- We consider the fact that if the solution exist in the array, then the two numbers get related by being complement of each other, as only one unique solution leads to the correct sum.
- I.E if a + b = T, then a = T - b, and b = T - a.
- Now while iterating we can store the positions against these complements
- I.E fn(T-b) = pos(a), fn(T-a) = pos(b).
- We build this [[Data_Structures/hashMap\|hashMap]] to store the relevant information and we build it while iterating through the array itself.
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        if(nums.size() ==  0) 
            return {};
        std::unordered_map<int,int> m;
        for(int i = 0; i<nums.size(); i++) {
            if(m.find(target - nums[i]) != m.end())
                return {i, m[target-nums[i]]};
            m[nums[i]] = i;
        }

        return {};
    }
};
```
- Notice how the time and space complexity has changes, we iterate through the array at most in n steps, however it may require us to construct a map that can increase in size as much as the array. Hence
- TO(n), SO(n);

## Valid Parentheses
- [[string\|string]] [[stack\|stack]]
- Let's first see an interesting solution to this problem that uses counting a single variable to find the solution.
- It doesn't work for all the cases, but does provide an insight into the idea of using a single variable to keep more information just than the count of something, but may relate to the structure and values provide powerful solutions.
- **The solution below is wrong**
```cpp
class Solution {
public:
    bool isValid(string s) {
        int balance = 0; // To keep track of parentheses balance

    for (char c : s) {
        if (c == '(') {
            balance++;
        } else if (c == ')') {
            balance--;
        } else if (c == '[') {
            balance++;
        } else if (c == ']') {
            balance--;
        } else if (c == '{') {
            balance++;
        } else if (c == '}') {
            balance--;
        }
    }

    return balance == 0;
        
    }
};
```
- It will face in case two different types of bracket close each other.
- TO(n), SO(1)
- Let's see a valid solution in python
```python
class Solution:
    def isValid(self, s: str) -> bool:
        stack = []
        memo = {'}':'{', ')':'(', ']':'['}
        for ch in s:
            if(len(stack) == 0 and ch in memo):
                return False
            if(len(stack) != 0 and ch in memo):
                if(stack.pop() != memo[ch]):
                    return False
            else:
                stack.append(ch)
        return len(stack) == 0
```


- TO(n), SO(n)
- We introduce the concept of stack, but why?
- In this problem we are concerned with how things are ordered, and we know if a bracket is opened than it must be closed too in order, which is what the stack helps us with.
- Cpp Solution
```cpp
class Solution {
public:
    char topAndPop(std::stack<char>& s) {
        const char ch = s.top();
        s.pop();
        return ch;
    }
    bool isValid(string s) {
        std::unordered_map<char,char> hmap = {{'(',')'},{'{','}'},{'[',']'}};
        std::stack<char> ch_stack;
        for(auto& ch:s) {
            if(hmap.find(ch) != hmap.end())
                ch_stack.push(hmap[ch]);
            else if(ch_stack.empty() || ch != topAndPop(ch_stack))
                return false;
        }
        return ch_stack.empty();
    }
};
```
- Notice how in cpp stack.pop() does not return a value, so we wrap that functionality using another function.
- TO(n), SO(n)


## Merge Two Lists
- [[recursion\|recursion]] [[Data_Structures/linkedList\|linkedList]]
- This problem requires merge two lists, think of a function that takes the top front nodes and compares them and then properly links them in order and them moves to next comparison step.
- The iterative solution will be:
```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode dummy(0);
        ListNode* curr = &dummy;
        while(l1 && l2) {
            if(l1->val > l2->val)  {
                curr->next = l2;
                l2 = l2->next;
            } else {
                curr->next = l1;
                l1 = l1->next;
            }
            curr = curr->next;
        }

        if(l1 != NULL) {
            curr->next = l1;
        } else {
            curr->next = l2;
        }

        return dummy.next;
        
    }
};
```
- TO(n), SO(1)
- Thing to notice are the two variables we created, the problem that they solve is interesting.
- When iterating through a linked list, if you have an iterator node, that gets modified continuously, we cannot return that, so we need to store the value of original head somewhere.
- But since the head of the returned list maybe from either of the LLs we cannot not know before hand which to choose.
- So we create a dummy variable that exists before either of the linked list and then return the next node of dummy which will be the sorted head of our answer.
- We use another variable curr to iterate and correctly join our lists.
- Things to notice:
	- See we only use a single if statement and do not do and explicit comparison on the else statement as '>' not being true implies '<=' being true for the variable, one can miss this and write code like this.
```cpp
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        if(!list1)
            return list2;
        if(!list2)
            return list1;
        ListNode dummy(0);
        ListNode* curr = &dummy;

        while(list1&&list2) {
            if(list1->val > list2->val) {
                curr->next = list2;
                list2 = list2->next;
            }
            if(list2->val > list1->val)  {
                curr->next = list1;
                list1 = list1->next;
            } curr = curr->next;
        }
        if(list1)
            curr->next = list1;
        if(list2)
            curr->next = list2;

        return dummy.next;
        
    }
};
```
- This code fails because both statements can fail and result in an access attempt on null pointer ('curr->next')

- **Is there a way to remove the dummy variable?**
- What about recursion, each recursive step is function waiting for the function within to pass it the results, the very structure of recursion can be used to replace the need of a dummy variable.
- Let's see.
```cpp

class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        if(!list1)
            return list2;
        if(!list2)
            return list1;
        if(list1->val > list2->val) {
            list2->next = mergeTwoLists(list1,list2->next);
            return list2;
        }
        else  {
            list1->next = mergeTwoLists(list1->next,list2);
            return list1;
        }
    }
};
```
- TO(m+n) SO(m+n)

## Best Time to Buy and Sell Stocks
- Well only if I could figure that out!
- [[twoPointers\|twoPointers]] [[Data_Structures/array\|array]] [[dynamicProgramming\|dynamicProgramming]]
- Let's see analyse the problem:
	- The order matters in this problem, we can use two variables one of which is always lower than the other one, making sure we can't sell even before buying.
	- We store the maxProfit in a variable, and update it if the profit for a particular pair of days is greater.
	- In case the profit for a pair of days is -ve, that implies there is price lower than current price using which more profit can be made potentially.
- The following approach uses two variables to find the max profit
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty())
            return 0;
        int maxProfit = 0;
        int currProfit = 0;
        int buyDay = 0;
        int sellDay = 1;
        while(sellDay < prices.size()) {
            currProfit = prices[sellDay] - prices[buyDay];
            if(currProfit > maxProfit) 
                maxProfit = currProfit;
            if(currProfit < 0) 
                buyDay = sellDay;
            sellDay++;
        }
        return maxProfit;
    }
};
```
- TO(n), SO(1)
- One can also solve this problem by finding the minimum price, and calculating the relative profit w.r.t minimum price and return the maximum, but we do not need to make sure that the order is maintained.
- The order thing can be taken care easily by the very nature of iteration itself.
```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if(prices.empty())
            return true;
        int minPrice = prices[0];
        int maxProfit = 0;
        for(auto& price: prices) {
            minPrice = std::min(minPrice,price);
            int currProfit = price - minPrice;
            maxProfit = std::max(maxProfit, currProfit);
        }
        return maxProfit;
    }
};
```
- TO(n), SO(1)

## Invert a Binary Tree
- [[Data_Structures/binaryTree\|binaryTree]] [[recursion\|recursion]] [[Algorithms/bfs\|bfs]] [[dfs\|dfs]] 
- Binary Trees in general lend themselves well to recursion, imagine
	- Being a node, if its null return
	- swap the children otherwise
	- call the function on it's children then just simply return the root that was given
```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(!root)
            return root;
        std::swap(root->left, root->right);
        invertTree(root->left);
        invertTree(root->right);
        return root;
    }
};
```
- TO(n), SO(h)->SO(logn) if tree balanced
- Notice the order, we go to left revert everything then we go to the right and revert everything.
- Since the reversal of both sides is independent these steps can be parallelised?
- This solution was an implementation of [[dfs\|dfs]] algorithm
- We can also solve it using [[Algorithms/bfs\|bfs]]
- Let's see that solution:
```cpp
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if(!root) 
            return root;
        std::queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()) {
            TreeNode* currNode = q.front();
            q.pop();
            if(currNode->left)
                q.push(currNode->left);
            if(currNode->right)
                q.push(currNode->right);
            std::swap(currNode->left, currNode->right);
        }
        return root; 
    }
};
```
- TO(n), SO(w) -> (O(logn),O(n))
- Interesting thing to notice is the **swap statement**, one can put it just below the pop() statement too.

## Valid Anagram
- [[Data_Structures/hashMap\|hashMap]] [[sorting\|sorting]] [[String\|String]]
- We have two string is they are anagrams ,then their sorted version will be have to be equal (always?)
```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        std::sort(s.begin(),s.end());
        std::sort(t.begin(),t.end());
        return s == t;
    }
};
```
- TO(nlong),SO(1)
- The above solution is rather straight forward and easy, but what if we are not allowed to reverse the lists?
```cpp
class Solution {
public:
    bool isAnagram(string s, string t) {
        unordered_map<char,int> ms;
        unordered_map<char,int> mt;
        for(auto& ch:s) {
            ms[ch]++;
        }
        for(auto& ch:t) {
            mt[ch]++;
        }
        for(auto& z:ms) {
            if(z.second != mt[z.first])
                return false;
        }
        return true;
    }
};
```
- TO(n), SO(n)

## Binary Search
- [[binarySearch\|binarySearch]] [[searching\|searching]] [[Data_Structures/array\|array]]
- This solution requires a simple implementation of binary search.
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int l = 0;
        int r = nums.size() -1;
        int mid = 0;
        while(r>=l) {
            mid = l + (r-l)/2;
            if(nums[mid] < target) {
                l = mid + 1;
            }
            else if (nums[mid] > target) {
                r = mid - 1;
            }
            else {
                return mid;
            }
        }
        return  -1;
    }
};
```
- TO(logn), SO(1)
- One important thing to notice is the positioning of 'if else statement', i.e the following code
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int l = 0;
        int r = nums.size() -1;
        int mid = 0;
        while(r>=l) {
            mid = l + (r-l)/2;
            if(nums[mid] < target) {
                l = mid + 1;
            }
            if (nums[mid] > target) {
                r = mid - 1;
            }
            else {
                return mid;
            }
        }
        return  -1;
    }
};
```
- Will not work, as the target may not exist in the array.

## Flood Fill
- [[dfs\|dfs]] [[Algorithms/bfs\|bfs]] [[Data_Structures/array\|array]] [[Data_Structures/matrix\|matrix]]
- We can do a dfs, i.e we will go the first element, check whether it does or does not have the correct color, if it does then we simply return.
- If not we change it and call the function on all the neighbours that are valid.
```cpp
class Solution {
public:
    void _floodFill(vector<vector<int>>& image, int sr, int sc, int color, int org) {
        if(sr >=image.size() || sr < 0 || sc < 0 || sc >=image[0].size())
            return;

        if(image[sr][sc] == org) {
            image[sr][sc] = color;
        _floodFill(image,sr+1,sc,color,org);
        _floodFill(image,sr-1,sc,color,org);
        _floodFill(image,sr,sc+1,color,org);
        _floodFill(image,sr,sc-1,color,org);
        }
    }
vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int color)  { 
        if(image.empty())   
            return {};
        if(image[sr][sc] == color)
            return image;
        int org = image[sr][sc];
        _floodFill(image,sr,sc,color, org);
        return image;
    }
};
```
- The above code uses [[dfs\|dfs]], imagine all the image points as a node connected four way with other ndoes.
- We can also use [[Algorithms/bfs\|bfs]] to do the same.
```cpp
class Solution {
public:
    vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int color) {
        if(image.empty())
            return {};
        if(image[sr][sc] == color)
            return image;

        std::queue<std::pair<int,int>> q;
        const int dr[] = {-1,1,0,0};
        const int dc[] = {0,0,-1,1};
        const int R = image.size()-1;
        const int C = image[0].size()-1;
        const int org = image[sr][sc];

        q.push(std::make_pair(sr,sc));


        while(!q.empty()) {
            int r,c;
            std::tie(r,c) = q.front();
            q.pop();
            image[r][c] = color;
            for(int i = 0; i<4; i++) {
                int nr = r + dr[i];
                int nc = c + dc[i];
                if(nc <= C && nc >= 0 && nr >= 0 && nr <=R && image[nr][nc] == org)
                    q.push(std::make_pair(nr,nc));
                
            }
        }

        return image;
        
    }
};
```


## Lowest Common Ancestor
- [[dfs\|dfs]] [[Algorithms/bfs\|bfs]] [[recursion\|recursion]] [[Data_Structures/binaryTree\|binaryTree]]
- Again we have a binary search tree, it's very structure gives us a rather elegant solution.
```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root)
            return root;
        if(p->val > root->val && q->val > root->val)
            return lowestCommonAncestor(root->right, p,q);
        if(p->val < root->val && q->val < root->val) 
            return lowestCommonAncestor(root->left,p,q);
        return root;
    }
};
```
- Above is a **dfs**  approach, let's see how a **bfs** approach will look like.
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */

class Solution {
public:
   TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (!root) {
        return root;
    }

    // Create a queue for BFS
    std::queue<TreeNode*> bfsQueue;
    bfsQueue.push(root);

    while (!bfsQueue.empty()) {
        TreeNode* current = bfsQueue.front();
        bfsQueue.pop();
        std::cout << current->val << std::endl;

        if ((current->val >= p->val && current->val <= q->val) || (current->val <= p->val && current->val >= q->val)) {
            return current; // Found the LCA
        }

            if(current->right)
                bfsQueue.push(current->right);

            if (current->left) {
                bfsQueue.push(current->left);
        }
    }

    return NULL; // LCA not found
}
};
```
- The **IMPORTANT** thing is the equality check, see it's not just '<>' but '<>='. 

## Balanced Binary Tree
- [[Data_Structures/tree\|tree]] [[dfs\|dfs]] [[Data_Structures/binaryTree\|binaryTree]]
- For this problem the important insight is solving the problem of figuring out the height of the left and right subTrees.
- We can write another function that specifically gets the height of a subtree
- We see if the difference of two subtree height is greater than 1, if it is then , we simple return true.
```cpp
class Solution {
public:
    int getHeight(TreeNode* node) {
        if(!node)
            return 0;
        return std::max(1 + getHeight(node->right), 1 + getHeight(node->left));
    }


    bool isBalanced(TreeNode* root) {
        if(!root)
            return true;
        if(abs(getHeight(root->left) - getHeight(root->right) > 1))
            return false;
        return isBalanced(root->right) && isBalanced(root->left);
        
    }
};
```

-  TO, SO
- The thing to notice is how we use && operator in our recursive function call.

## Linked List Cycle.
- [[Data_Structures/hashMap\|hashMap]] [[Data_Structures/linkedList\|linkedList]] [[twoPointers\|twoPointers]]
- This problem uses property of numbers in order to find the cycle.
- Think of hands of clocks they move at different speeds but come at the same point through out the day.
- That point is the LCM of the hands.
- We can use the same idea to find if there is a cycle, imagine two iterators going through the linked list, with one moving at a higher speed, if there is a cycle then they will eventually reach the same node.
- If the cycle is not there, well then our loop will simply exit and result in false being returned.
```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode* s = head;
        ListNode* f = head;
        while(f && f->next) {
            s = s->next;f = f->next->next;
            if (s == f)
                return true;
        } return false;
        
    }
};
```
- Something interesting to notice is about C++ code is that following line doesn't work.
```cpp
        ListNode* s,f;
        s = head;
        f = head;
```
- The correct way of doing it is:
```cpp
        ListNode *s,*f;
        s = head;
        f = head;
```
- More you know!

## Implement Queue using stacks.
- This question is rather trivial and not fun : (.
- But here is the code
```cpp
class MyQueue {
private:
    std::stack<int> stack1;
    std::stack<int> stack2;
public:

    
    MyQueue() {
    }
    
    void push(int x) {
        stack1.push(x);
    }
    
    int pop() {
        int val = 0;
        if(stack2.empty())
            while(!stack1.empty()) {
                stack2.push(stack1.top());
                    stack1.pop();
            }
        
        val = stack2.top();
        stack2.pop();
        return val;
        
    }
    
    int peek() {
                if(stack2.empty())
            while(!stack1.empty()) {
                stack2.push(stack1.top());
                    stack1.pop();
            }
        return stack2.top();
    }
    
    bool empty() {
        return stack1.empty()&&stack2.empty();
    }
};

```
- Imagine a deck of cards as the stack, implementing queue is similar to rotating the stack, i.e getting it upside down.
- This is done using the second stack, take everything in s1 and put in s2
	- s1 -> s2
- Now the top of the s2 will be the bottom piece in s1, hence we successfully implement FIFO, using FILO. Amazing!


## First Bad Version
- The problem here is not that of writing the algorithm simply, because following is valid solution.
```cpp
class Solution {
public:
    int firstBadVersion(int n) {
        int i = 1;
        while(i<=n) 
            if(isBadVersion(i++))
                return i-1;
        return -1;
    }
};
```
- The solution they want from us is the following, which is another implementation of [[binarySearch\|binarySearch]] on the very number rather than an array.
```cpp
class Solution {
public:
    int firstBadVersion(int n) {
        int l=1;
        int h=n;
        while(l<h){
            const int mid=l+(h-l)/2;
            if(isBadVersion(mid)){
                h=mid;
            }
            else{
                l=mid+1;
            }
        }
        return l;
    }
};
```


## Ransom Note
- [[Data_Structures/hashMap\|hashMap]] [[string\|string]] [[counting\|counting]]
- This is an important algorithm for kidnappers, and have been widely used by Big Crime, so prepare it well before interviewing for the Mafia, Cartel etc.
- The idea is rather simple, count all the characters available in magazine and store the relevant counts against the characters in a hash table.
- Then iterate through the ransomNote string, if there is a character missing in magazine hash map or that character count has reached zero we return fasle.
```cpp
class Solution {
public:
    bool canConstruct(string ransomNote, string magazine) {
        if(magazine.size() == 0)
            return false;
        unordered_map<char,int>  m;
        for(auto& ch:magazine) {
            m[ch]++;
        }

        for(auto& ch:ransomNote) {
            if(m.find(ch) == m.end() || m[ch] == 0)
                return false;
            m[ch]--;
        }
        return true;
    }
};
```


## Climbing Stairs
- [[Data_Structures/hashMap\|hashMap]] [[dynamicProgramming\|dynamicProgramming]] [[recursion\|recursion]]
- This problem is just like implementing a recursive memoized function for Fibonacci series.
```cpp
class Solution {
public:
    int cs(std::unordered_map<int,int>& m, int n ){
        if(m.find(n) == m.end())   
            m[n] = cs(m,n-1) + cs(m,n-2);
        return m[n];
    }
    int climbStairs(int n) {
        if(n <=1)
            return 1;
        std::unordered_map<int,int> m = {{1,1},{2,2}};
        return cs(m,n);
    }
};
```
- Simple stuff!

## Longest Palindrome
- [[Algorithms/Greedy-Algorithm\|Greedy-Algorithm]][[counting\|counting]][[Data_Structures/hashMap\|hashMap]]
- This problem introduces interesting idea to solve it, imagine you are given a string, consider all the elements with event count and start filling the string at both ends, if their count is even then it means that the final string will infact be a palindrome.
- The characters that have a odd count say 2n+1 then the 2n times we can just follow the even approach and put the remaining one in the the centre.
- If there are characters with only one count then any of them can be used to form the centre of the string but only one.
```cpp
class Solution {
public:
    int longestPalindrome(string s) {
        unordered_map<char,int> map;
        int odd = 0;
        for(auto& ch:s) {
            map[ch]++;
            if(map[ch]%2 == 1) 
                odd++;
            else
                odd--;
        }
        if(odd>1)
            return s.size()-odd+1;
        return s.size();
        
    }
};
```


## Reverse Linked List
- Just like all the other recursion problem, we can use it here as well
```cpp
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if(!head || !head->next) 
            return head;
        ListNode* newHead = reverseList(head->next);
        head->next->next = head;
        head->next = NULL;
        return newHead;
    }
};
```

### Majority Element
- [[counting\|counting]] [[Data_Structures/hashMap\|hashMap]] [[divideAndConquer\|divideAndConquer]]
- [[Algorithms/Boyer-Moore Majority Vote Algorithm\|Boyer-Moore Majority Vote Algorithm]]
```cpp
class Solution {
public:
    int majorityElement(const vector<int>& nums) {
        int element = 0;
        int count = 0;
        for(auto& i:nums) {
            if(count < 0)
                element = i;
            else if (element == i)
                count++;
            else
                count --;
        }
        return element;
    }
};
```
- Another perhaps more straight forward approach would be 
```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        std::sort(nums.begin(),nums.end());
        return nums[nums.size()/2];
    }
};
```
- Also you cannot pass  const iterators (.cbegin(), .cend()) into std::sort.
- Another way of dealing with this problem is by using a hashMap as following.
```cpp
class Solution {
public:
    int majorityElement(vector<int>& nums) {
        unordered_map<int,int> m;
        for(auto i:nums) {
            m[i]++;
        }
        for(auto z:m) {
            if (z.second > nums.size()/2)
                return z.first;
        }
        return -1;
    }
};
```

## Add Binary
- An interesting problem
```cpp
class Solution {
public:
    string addBinary(string a, string b) {
        int i = a.size()-1;
        int j = b.size()-1;
        int carry = 0;
        std::string ans = "";
        while(i>=0 || j>=0 || carry >0) {
            int sum = carry;
            if(i>=0) 
                sum += a[i--] - '0';
            if(j>=0)
                sum += b[j--] - '0';
            ans = std::to_string(sum%2) + ans;
            carry = sum/2;
        }
        return ans;
    }
};
```

### Diameter of a binary tree.
- [[Data_Structures/binaryTree\|binaryTree]] [[dfs\|dfs]] [[Algorithms/bfs\|bfs]]
- We can use simple dfs to solve this problem
```cpp
class Solution {
public:
    int getHeight(TreeNode* root, int& dia) {
        if(!root)
            return 0;
        int lheight = getHeight(root->left,dia);
        int rheight = getHeight(root->right,dia);
        dia = std::max(dia, lheight+rheight);
        return 1 + std::max(getHeight(root->left,dia), getHeight(root->right,dia));

    }

    int diameterOfBinaryTree(TreeNode* root) {
        if(!root)
            return 0;
        int dia = 0;
        getHeight(root,dia);
        return dia;

    }
};
```

### Middle of the linked list
- [[Data_Structures/linkedList\|linkedList]] [[twoPointers\|twoPointers]]
```cpp
class Solution {
public:
    ListNode* middleNode(ListNode* head) {
        if(!head)
            return head;
        ListNode* slow = head;
        while(head && head->next) {
            slow = slow->next;
            head = head->next->next;
        }
        return slow;
    }
};
```

### Contains Duplicate
- [[Data_Structures/hashMap\|hashMap]] [[sorting\|sorting]] [[Data_Structures/array\|array]]
- Again this problem leads to multiple solution, one being the following which uses a hash map.
```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        unordered_map<int,int> m;
        for(auto& i:nums) {
            m[i]++;
            if(m[i] > 1)
                return true;
        }
        return false;
    }
};
```
- The following uses sorting
```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        std::sort(nums.begin(),nums.end());
        auto itr = nums.begin();
        while(itr+1 != nums.end()) {
            if(*itr == *(++itr))
                return true; 

        }
        return false;
    }
};
```
Also all the following are equivalent
```cpp
*++itr = * ++itr = *(++itr)
```
- But the following won't work
```cpp
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        std::sort(nums.begin(),nums.end());
        auto itr = nums.begin();
        while(itr+1 != nums.end()) {
            if(*itr == *(itr++))
                return true; 

        }
        return false;
    }
};
```
### Maximum Depth of a binary tree.
- [[dfs\|dfs]] [[Algorithms/bfs\|bfs]] [[Data_Structures/binaryTree\|binaryTree]]
- Again seeing that we have a binary tree, the problem leads itself well to a very simple recursive approach.
```cpp
class Solution {
public:
    int getHeight(TreeNode* root) {
        if(!root)
            return 0;
        return 1 + std::max(getHeight(root->left), getHeight(root->right));
    }

    int maxDepth(TreeNode* root) {
        if(!root)
            return 0;
        return getHeight(root);
        
    }
};
```

- Let us also see how a bfs approach would have worked.
```cpp
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root)
            return 0;
        std::queue<TreeNode*> q;
        uint height = 0; 
        q.push(root);
        while(!q.empty()) {
            int lvlwidht = q.size();
            for(int i =0; i<lvlwidht; i++) {
                TreeNode* tn = q.front();
                q.pop();
                if(tn->left) 
                    q.push(tn->left);
                if(tn->right)
                    q.push(tn->right);
            }

            height++;
        }
        return height;
    }
};
```
- The interesting thing to notice about this implementation is how we have seperated the task of equeing from the main loop by introducing another loop.

### Roman to Integer
- The problem introduces an insight into how the data is behaving and we use that to our advantage.
```cpp
class Solution {
public:
    int romanToInt(string s) {
        int res = 0;
        stack<char> stack;
        unordered_map<char,int> rti = {{'I',1},{'V',5},{'X',10},{'L',50},{'C',100},{'D',500},{'M',1000}};

        for(auto& ch:s) {
            if(!stack.empty() &&rti[stack.top()] < rti[ch] ) {
                res += (rti[ch] - rti[stack.top()]);
                stack.pop();
                continue;
            }
            stack.push(ch);
        } 

        while(!stack.empty()) {
            res += rti[stack.top()];
            stack.pop();
        }
        return res;
    }
};
```

### BackSpace String Compare
- [[stack\|stack]] [[simulation\|simulation]] [[twoPointers\|twoPointers]]
- We can solve this in a more natural way if use the stack.
```cpp
class Solution {
public:
    bool backspaceCompare(const string& s, const string& t) {
        std::stack<char> s_stack;
        std::stack<char> t_stack;
        for(auto& ch:s) {
            if(ch == '#' && !s_stack.empty())
                s_stack.pop();
            else if (ch != '#') 
                s_stack.push(ch);
        }

        for(auto& ch:t) {
            if(ch == '#' && !t_stack.empty())
                t_stack.pop();
            else if (ch != '#') 
                t_stack.push(ch);
        }

        return s_stack == t_stack;
        
    }
};
```
- Notice how the stacks mimics what we acutally do when typing.
- The other approach would be to use two variables as follows.
```cpp
class Solution {
public:
    bool backspaceCompare(std::string S, std::string T) {
    int i = S.length() - 1;
    int j = T.length() - 1;
    int skipS = 0, skipT = 0;

    while (i >= 0 || j >= 0) {
        while (i >= 0) {
            if (S[i] == '#') {
                skipS++;
                i--;
            } else if (skipS > 0) {
                skipS--;
                i--;
            } else {
                break;
            }
        }

        while (j >= 0) {
            if (T[j] == '#') {
                skipT++;
                j--;
            } else if (skipT > 0) {
                skipT--;
                j--;
            } else {
                break;
            }
        }

        // Compare the current characters if both are non-backspace characters
        if (i >= 0 && j >= 0 && S[i] != T[j]) {
            return false;
        }

        // If one string reaches the end but the other doesn't, they are not equal
        if ((i >= 0) != (j >= 0)) {
            return false;
        }

        i--;
        j--;
    }

    return true;
}
};
```

## Counting bits 
- [[Basic_shit/Bit-Manipulation\|Bit-Manipulation]]
```cpp
class Solution {
public:
    uint8_t hammingWeight(uint32_t n) {
        if(!n)
            return 0;
        uint8_t count = 0;
        while(n) {
            count += n & 1;
            n >>= 1;
        }
        return count;
    }


    vector<int> countBits(int n) {
        vector<int> res(n+1,0);
        for(int i = 0; i<=n; ++i) {
            res[i] = hammingWeight(i);
        }
        return res;
    }
};
```
- Another straight forward approach would be
```cpp
class Solution {
public:

    
    vector<int> countBits(int n) {
        vector<int> res(n+1,0);
        for(int i = 0; i<=n; ++i) {
            res[i] = __builtin_popcount(i);
        }
        return res;
    }
};
```

## Same Tree
- [[Data_Structures/binaryTree\|binaryTree]] [[recursion\|recursion]] [[dfs\|dfs]] [[Algorithms/bfs\|bfs]]
```cpp
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if(!p && !q)
            return true;
        if(!q || !p)
            return false;
        
        if(q->val != p->val)
            return false;

        return isSameTree(p->left,q->left) && isSameTree(p->right,q->right);
    }
```
- The above is a dfs approach.
- And the following is a bfs approach.

## Number of 1 bits
- [[Basic_shit/Bit-Manipulation\|Bit-Manipulation]] 
```cpp
class Solution {
public:
    uint8_t hammingWeight(uint32_t n) {
        if(!n)
            return 0;
        uint8_t count = 0;
        while(n) {
            count += n & 1;
            n >>= 1;
        }
        return count;
    }
};
```

## Longest Common Prefix
- [[string\|string]] [[Data_Structures/trie\|trie]]
- One can solve this problem rather easily using the following solution.
```cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        if(strs.empty())
            return "";
        if(strs.size() <= 1)
            return strs[0];
        int i = 0;
        bool flag = true;
        while(flag) {
            char cmp = strs[0][i];
            for(auto& str:strs) {
                if(str[i] == cmp)
                    continue;
                else {
                    flag = false;
                    break;
                }
            }
            i++;
        }
        if(!i)
            return "";
        return strs[0].substr(0,i-1);
    }
};
```
- But we have a special data structure when it comes to dealing with strings. The solution to that can be found in [[Data_Structures/trie\|trie]].


## Single Number 
- [[Basic_shit/Bit-Manipulation\|Bit-Manipulation]] [[Data_Structures/array\|array]]
```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        if(nums.empty()) 
            return -1;
        int ans = 0;
        for(auto& i:nums)
            ans ^= i;
        return ans;

    }
};
```


## Palindrome Linked List
- [[stack\|stack]] [[recursion\|recursion]] [[twoPointers\|twoPointers]] [[Data_Structures/linkedList\|linkedList]] 
```cpp
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        std::stack<int> intstack;
        ListNode* temp = head;
        while(temp!=NULL) {
            intstack.push(temp->val);
                temp = temp->next;
        }
        
        while(head != NULL) {
            if(head->val != intstack.top())
                return false;
            head = head->next;
            intstack.pop();
        }

        return true;
            
    }
};
```
- We can also use a recursive approach as follows
```cpp
class Solution {
public:
    bool isPalindrome(ListNode*& left, ListNode* right) {
    if (!right) {
        return true;
    }
    bool isPal = isPalindrome(left, right->next);
    isPal = isPal && (left->val == right->val);
    left = left->next;
    return isPal;
}

    bool isPalindrome(ListNode* head) {
        return isPalindrome(head,head);
        
    }
};
```

## Convert Sorted Array to Binary Search Tree
- [[Data_Structures/binaryTree\|binaryTree]] [[Data_Structures/binarySearchTree\|binarySearchTree]] [[Data_Structures/tree\|tree]] [[recursion\|recursion]] [[divideAndConquer\|divideAndConquer]]
```cpp
class Solution {
public:
    TreeNode* makeTree(std::vector<int>& nums,int l,int r) {
        if(l>r) 
            return NULL;
        int mid = l + (r-l)/2;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = makeTree(nums,l,mid-1);
        root->right = makeTree(nums,mid+1,r);
        return root;
    }
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        if(nums.empty())
            return NULL;
        return makeTree(nums,0,nums.size()-1);
    }
};
``` 

## Reverse Bits
- [[Basic_shit/Bit-Manipulation\|Bit-Manipulation]] [[divideAndConquer\|divideAndConquer]]
```cpp
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        int res = 0;
        int bits = 31;
        while(bits >= 0) {
            int currBit = n & 1;
            res = res | (currBit << bits);
            n >>= 1;
            bits--;
        }
        return res;
    }
};
```

## SubTree of another Tree
- [[Data_Structures/binaryTree\|binaryTree]] [[recursion\|recursion]]
```cpp
bool isIdentical(TreeNode* s, TreeNode* t) {
    if (!s && !t) {
        return true; 
    }
    if (!s || !t) {
        return false; 
    }

    return (s->val == t->val) && isIdentical(s->left, t->left) && isIdentical(s->right, t->right);
}

bool isSubtree(TreeNode* s, TreeNode* t) {
    if (!s) {
        return false; 
    }
    if (isIdentical(s, t)) {
        return true;
    }
    return isSubtree(s->left, t) || isSubtree(s->right, t);
}
```
- The bfs approach would be like this
```cpp
bool isIdentical(TreeNode* s, TreeNode* t) {
    if (!s && !t) {
        return true; 
    }
    if (!s || !t) {
        return false;
    }

    return (s->val == t->val) && isIdentical(s->left, t->left) && isIdentical(s->right, t->right);
}

bool isSubtree(TreeNode* s, TreeNode* t) {
    if (!s) {
        return false; 
    }

    std::queue<TreeNode*> bfsQueue;
    bfsQueue.push(s);

    while (!bfsQueue.empty()) {
        TreeNode* currentNode = bfsQueue.front();
        bfsQueue.pop();
        if (isIdentical(currentNode, t)) {
            return true;
        }
        if (currentNode->left) {
            bfsQueue.push(currentNode->left);
        }
        if (currentNode->right) {
            bfsQueue.push(currentNode->right);
        }
    }

    return false; 
}
```


## Squares of a Sorted Array
```cpp
bool isIdentical(TreeNode* s, TreeNode* t) {
    if (!s && !t) {
        return true; 
    }
    if (!s || !t) {
        return false;
    }

    return (s->val == t->val) && isIdentical(s->left, t->left) && isIdentical(s->right, t->right);
}

bool isSubtree(TreeNode* s, TreeNode* t) {
    if (!s) {
        return false; 
    }

    std::queue<TreeNode*> bfsQueue;
    bfsQueue.push(s);

    while (!bfsQueue.empty()) {
        TreeNode* currentNode = bfsQueue.front();
        bfsQueue.pop();
        if (isIdentical(currentNode, t)) {
            return true;
        }
        if (currentNode->left) {
            bfsQueue.push(currentNode->left);
        }
        if (currentNode->right) {
            bfsQueue.push(currentNode->right);
        }
    }

    return false; 
}
```

## Maximum SubArray
- [[Data_Structures/array\|array]] [[dynamicProgramming\|dynamicProgramming]] [[divideAndConquer\|divideAndConquer]]
```cpp
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if(nums.empty())
            return 0;
        int max = INT_MIN;
        int currSum = 0;
        for(int i = 0; i<nums.size(); i++) {
            currSum += nums[i];
            if(currSum > max) 
                max = currSum;
            if(currSum < 0)
                currSum = 0;
        }    
        return max;    
    }
};
```

## Insert Interval
- 

## 01 Matrix
- [[Data_Structures/array\|array]] [[dynamicProgramming\|dynamicProgramming]] [[Algorithms/bfs\|bfs]] [[Data_Structures/matrix\|matrix]]
```cpp
class Solution {
public:
std::vector<std::vector<int>> updateMatrix(std::vector<std::vector<int>>& matrix) {
        if (matrix.empty()) {
            return matrix; 
        }

        int rows = matrix.size();
        int cols = matrix[0].size();

        std::vector<std::vector<int>> result(rows, std::vector<int>(cols, INT_MAX));

        std::queue<std::pair<int, int>> bfsQueue;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (matrix[i][j] == 0) {
                    result[i][j] = 0;
                    bfsQueue.push(std::make_pair(i, j));
                }
            }
        }
        std::vector<int> dr = {-1, 1, 0, 0};
        std::vector<int> dc = {0, 0, -1, 1};
        while (!bfsQueue.empty()) {
            int r = bfsQueue.front().first;
            int c = bfsQueue.front().second;
            bfsQueue.pop();
            for (int dir = 0; dir < 4; dir++) {
                int newR = r + dr[dir];
                int newC = c + dc[dir];
                if (newR >= 0 && newR < rows && newC >= 0 && newC < cols &&
                    result[newR][newC] > result[r][c] + 1) {
                    result[newR][newC] = result[r][c] + 1;
                    bfsQueue.push(std::make_pair(newR, newC));
                }
            }
        }

        return result;
    }
};
```
- The **dfs** approach will be this 
```cpp
class Solution {
public:
std::vector<std::vector<int>> updateMatrix(std::vector<std::vector<int>>& matrix) {
        if (matrix.empty()) {
            return matrix;
        }

        int rows = matrix.size();
        int cols = matrix[0].size();
        std::vector<std::vector<int>> result(rows, std::vector<int>(cols, INT_MAX));
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (matrix[i][j] == 0) {
                    dfs(matrix, result, i, j, 0);
                }
            }
        }

        return result;
    }

private:
    void dfs(std::vector<std::vector<int>>& matrix, std::vector<std::vector<int>>& result, int r, int c, int distance) {
        int rows = matrix.size();
        int cols = matrix[0].size();

        if (r < 0 || r >= rows || c < 0 || c >= cols || distance >= result[r][c]) {
            return;
        }
        result[r][c] = distance;

        int dr[] = {-1, 1, 0, 0};
        int dc[] = {0, 0, -1, 1};

        for (int dir = 0; dir < 4; dir++) {
            int newR = r + dr[dir];
            int newC = c + dc[dir];

            dfs(matrix, result, newR, newC, distance + 1);
        }
    }
};
```
- But the above approach will fail due high time complexity
- Interestingly both algorithms have same time complexities theoretically but **bfs** will actually be faster.

## K Closest Points to origin
- [[quickSelect\|quickSelect]] [[prioirtyQueue\|prioirtyQueue]] [[divideAndConquer\|divideAndConquer]] [[Geometry\|Geometry]] [[math\|math]] [[sorting\|sorting]] 
- A straight forward approach would be 
```cpp
class Solution {
public:
    float _getdist(float x,float y) {
        return std::sqrt(pow(x,2)+pow(y,2));
    }
    vector<vector<int>> kClosest(vector<vector<int>>& points, int k) {
        std::map<float,std::vector<vector<int>>> pointMap;
        vector<vector<int>> res;
        for(auto& point:points) {
            float dist = _getdist(point[0],point[1]);
            if(pointMap.find(dist) != pointMap.end()) {
                pointMap[dist].push_back(point);
                continue;
            }
            pointMap[dist].push_back(point);
        }
        auto it = pointMap.begin();
        int i = 0;
        while(i<k) {
            auto vec = it->second;
            for(auto v:vec) {
                res.push_back(v); i++; 
                if(!(i<k))
                    break;
            }
            it++;
        }
        return res;
    }
};
```
- A cooler solution would be
```cpp
struct CompareDistance {
    bool operator()(const std::pair<int, int>& p1, const std::pair<int, int>& p2) {
        return (p1.first * p1.first + p1.second * p1.second) >
               (p2.first * p2.first + p2.second * p2.second);
    }
};

class Solution {
public:
    std::vector<std::vector<int>> kClosest(std::vector<std::vector<int>>& points, int k) {
        // Create a priority queue to store points sorted by distance from origin
        std::priority_queue<std::pair<int, int>, std::vector<std::pair<int, int>>, CompareDistance> pq;

        // Push the first K points into the priority queue
        for (int i = 0; i < k; i++) {
            pq.push({points[i][0], points[i][1]});
        }

        // Continue iterating through the remaining points and update the queue if closer points are found
        for (int i = k; i < points.size(); i++) {
            int dist = points[i][0] * points[i][0] + points[i][1] * points[i][1];
            int maxDist = pq.top().first * pq.top().first + pq.top().second * pq.top().second;
            
            if (dist < maxDist) {
                pq.pop();
                pq.push({points[i][0], points[i][1]});
            }
        }

        // Extract the K closest points from the priority queue
        std::vector<std::vector<int>> result;
        while (!pq.empty()) {
            result.push_back({pq.top().first, pq.top().second});
            pq.pop();
        }

        return result;
    }
};
```

## Longest Substring Without Repeating Characters
- [[slidingWindow\|slidingWindow]] [[string\|string]] [[Data_Structures/hashMap\|hashMap]]
```cpp
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        std::queue<char> charQ;
        uint l = 0;
        uint r = 0;
        uint maxL = 0;
        for(;r<0;) {
            if(charQ.front() != s[r]) {
                charQ.push(s[r++]);
            else 
                {charQ.pop();l++;};
            }
        }
        return charQ.size()-1;
        
    }
};
```

## 3Sum
- [[Data_Structures/array\|array]] [[twoPointers\|twoPointers]] [[sorting\|sorting]]
```cpp
class Solution {
public:
    std::vector<std::vector<int>> threeSum(std::vector<int>& nums) {
    std::vector<std::vector<int>> result;
    int n = nums.size();

    if (n < 3) {
        return result; 
    }

    // Sort the input array.
    std::sort(nums.begin(), nums.end());

    for (int i = 0; i < n - 2; i++) {
        // Avoid duplicates.
        if (i > 0 && nums[i] == nums[i - 1]) {
            continue;
        }

        int target = -nums[i];
        int left = i + 1;
        int right = n - 1;

        while (left < right) {
            int sum = nums[left] + nums[right];

            if (sum == target) {
                result.push_back({nums[i], nums[left], nums[right]});
                // Avoid duplicates.
                while (left < right && nums[left] == nums[left + 1]) left++;
                while (left < right && nums[right] == nums[right - 1]) right--;
                left++;
                right--;
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
    }

    return result;
}
};
```

## Binary Tree Level Order Traversal
- [[Data_Structures/binaryTree\|binaryTree]] [[Algorithms/bfs\|bfs]]
```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        if(!root)
            return {};

        std::vector<std::vector<int>> res;
        std::queue<TreeNode*> nq;
        nq.push(root);
        while(!nq.empty()) {
            int N = nq.size();
            std::vector<int> temp;
            for(int i = 0; i<N;i++) {
                TreeNode* curr = nq.front(); nq.pop();
                temp.push_back(curr->val);
                if(root->right)
                    nq.push(root->right);
                if(root->left)
                    nq.push(root->left);
            } res.push_back(temp);
        }
        return res;
    }
};
```

## Clone Graph
- [[Data_Structures/hashMap\|hashMap]] [[Algorithms/bfs\|bfs]] [[dfs\|dfs]] [[graph\|graph]]
```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> neighbors;
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
*/

class Solution {
public:
    std::unordered_map<Node*, Node*> visited;

Node* cloneGraph(Node* node) {
    if (!node) {
        return nullptr; // Return nullptr for an empty graph.
    }

    // If the node has already been cloned, return its clone.
    if (visited.find(node) != visited.end()) {
        return visited[node];
    }

    // Create a clone of the current node.
    Node* cloneNode = new Node(node->val);

    // Mark the node as visited and store its clone.
    visited[node] = cloneNode;

    // Recursively clone all neighbors of the current node.
    for (Node* neighbor : node->neighbors) {
        cloneNode->neighbors.push_back(cloneGraph(neighbor));
    }

    return cloneNode;
}
};
```

## Min Stack
- Too easy
```cpp
class MinStack {
public:
    typedef struct node{
        int v;
        int minUntilNow;
        node* next;
    }node;

    MinStack() : topN(nullptr){
        
    }
    
    void push(int val) {
        node* n = new node;
        n->v = n->minUntilNow = val;
        n->next = nullptr;
        
        if(topN == nullptr){
            topN = n;
        }

        else{
            n->minUntilNow = min(n->v,topN->minUntilNow);
            n->next = topN;
            topN = n;
        }
    }
    
    void pop() {
        topN = topN->next;
    }
    
    int top() {
        return topN->v;
    }
    
    int getMin() {
        return topN->minUntilNow;
    }

    private:
    node* topN;
};
```

## Evaluate Reverse Polish Notation
- [[math\|math]] [[stack\|stack]] [[arra\|arra]]
```cpp
class Solution {
public:
    bool isNum(const std::string& s) {
    std::istringstream ss(s);
    double num;
    return (ss >> num) && ss.eof();
    }

    int calc(std::stack<int>& s, std::string op) {
        int a = s.top(); s.pop();
        int b = s.top(); s.pop();
        if(op == "*")
            return b*a;
        if(op == "/")
            return b/a;
        if(op == "-")
            return b-a;
        if(op == "+")
            return b+a;
        return 0;
    }


    int evalRPN(vector<string>& tokens) {
        std::stack<int> stk;

        for(auto& s:tokens) {
            if(isNum(s)) {
                stk.push(atoi(s.c_str()));
            }
            else {
                stk.push(calc(stk, s));
            }

        }
        return stk.top();
        
    }
};
```

## Course Schedule
- [[topological sorting\|topological sorting]] [[Khan's Algorithm\|Khan's Algorithm]]
```cpp
class Solution {
public:
    bool canFinish(int numCourses, std::vector<std::vector<int>>& prerequisites) {
    std::vector<std::vector<int>> graph(numCourses);
    std::vector<int> inDegree(numCourses, 0);
    for (const auto& prerequisite : prerequisites) {
        int course = prerequisite[0];
        int prereq = prerequisite[1];
        graph[prereq].push_back(course);
        inDegree[course]++;
    }
    std::queue<int> q;
    for (int i = 0; i < numCourses; i++) {
        if (inDegree[i] == 0) {
            q.push(i);
        }
    }
    while (!q.empty()) {
        int course = q.front();
        q.pop();
        numCourses--;
        for (int dependentCourse : graph[course]) {
            if (--inDegree[dependentCourse] == 0) {
                q.push(dependentCourse);
            }
        }
    }
    return numCourses == 0;
}
};
```

## Implement Trie
- Just simply implement the datastructure
```cpp
class TrieNode {
public:
    std::unordered_map<char, TrieNode*> children;
    bool isEndOfWord;

    TrieNode() {
        isEndOfWord = false;
    }
};


class Trie {
private:
    TrieNode* root;
public:
    Trie() {
        root = new TrieNode(); 
    }
    
    void insert(string word) {
        TrieNode* node = root;
        for(auto c : word) {
            if(!node->children[c]) {
                node->children[c] = new TrieNode();
            }
            node = node->children[c];
        }
        node->isEndOfWord = true;
    }
    
    bool search(string word) {
        TrieNode* node = root;
        for(auto c: word) {
            if(!node->children[c])
                return false;
            node = node->children[c];
        }
        return node->isEndOfWord;
    }
    
    bool startsWith(string prefix) {
        TrieNode* node = root;
        for(auto c: prefix) {
            if(!node->children[c])
                return false;
            node = node->children[c];
        }
        return true;

    }
};

/**
 * Your Trie object will be instantiated and called as such:
 * Trie* obj = new Trie();
 * obj->insert(word);
 * bool param_2 = obj->search(word);
 * bool param_3 = obj->startsWith(prefix);
 */
```
## Coin Change 
- There are multiple ways to solve this problem, following is a **dfs** approach, but it will fail due to over recursion.
```cpp
class Solution {
public:
    void dfs(std::vector<int>& coins, int amount, int currentAmount, int& minCoins, int& currentCoins) {
    if (currentAmount == 0) {
        minCoins = std::min(minCoins, currentCoins);
        return;
    }

    if (currentAmount < 0 || currentCoins >= minCoins) {
        return;
    }

    for (int coin : coins) {
        currentCoins++;
        dfs(coins, amount, currentAmount - coin, minCoins, currentCoins);
        currentCoins--;
    }
}

int coinChange(std::vector<int>& coins, int amount) {
    int minCoins = amount + 1; 
    int currentCoins = 0;

    dfs(coins, amount, amount, minCoins, currentCoins);

    return (minCoins > amount) ? -1 : minCoins;
}

};
```
- Following is a **bfs** approach
```cpp
class Solution {
public:
    int coinChange(std::vector<int>& coins, int amount) {
    std::queue<int> q;
    std::vector<bool> visited(amount + 1, false);

    q.push(amount);
    visited[amount] = true;
    int level = 0; 
    while (!q.empty()) {
        int size = q.size();

        for (int i = 0; i < size; i++) {
            int currentAmount = q.front();
            q.pop();

            if (currentAmount == 0) {
                return level; 
            }

            for (int coin : coins) {
                int nextAmount = currentAmount - coin;

       
                if (nextAmount >= 0 && !visited[nextAmount]) {
                    q.push(nextAmount);
                    visited[nextAmount] = true;
                }
            }
        }

        level++; 
    }

    return -1; 
}
};
```
- And below is the iterative approach
```cpp
int coinChange(std::vector<int>& coins, int amount) {

    std::vector<int> dp(amount + 1, amount + 1);

    dp[0] = 0;


    for (int i = 1; i <= amount; i++) {
        for (int coin : coins) {
            if (coin <= i) {
                dp[i] = std::min(dp[i], dp[i - coin] + 1);
            }
        }
    }


    return (dp[amount] > amount) ? -1 : dp[amount];
}
```

## Product of Array except Self

## Min Stack

## Validate Binary Search Tree
- [[Data_Structures/binaryTree\|binaryTree]] [[recursion\|recursion]]
```cpp
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        if(root && !root->right && !root->left)
            return true;
        else if(!root->left && root->right)
            return isValidBST(root->right);
        else if(!root->right && root->left)
            return isValidBST(root->left);
        else if(root->val >= root->right->val || root->val <= root->left->val)
            return false;
        return isValidBST(root->right) && isValidBST(root->left);
    }
};

```

## Number Of Islands
- [[graph\|graph]] [[dfs\|dfs]] [[Algorithms/bfs\|bfs]] [[unionFind\|unionFind]] [[Data_Structures/matrix\|matrix]]
```cpp
class Solution {
public:
    int numIslands(std::vector<std::vector<char>>& grid) {
        if (grid.empty() || grid[0].empty()) {
            return 0; // Empty grid has no islands.
        }

        int numIslands = 0;
        int rows = grid.size();
        int cols = grid[0].size();

        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == '1') {
                    numIslands++;
                    dfs(grid, i, j);
                }
            }
        }

        return numIslands;
    }

private:
    void dfs(std::vector<std::vector<char>>& grid, int row, int col) {
        int rows = grid.size();
        int cols = grid[0].size();

        if (row < 0 || row >= rows || col < 0 || col >= cols || grid[row][col] == '0') {
            return; // Out of bounds or water cell, stop recursion.
        }

        // Mark the current cell as visited.
        grid[row][col] = '0';

        // Recursively explore neighboring cells.
        dfs(grid, row - 1, col); // Up
        dfs(grid, row + 1, col); // Down
        dfs(grid, row, col - 1); // Left
        dfs(grid, row, col + 1); // Right
    }
};
```
## Rotating Oranges
- [[Data_Structures/array\|array]] [[Algorithms/bfs\|bfs]] [[Data_Structures/matrix\|matrix]]
```cpp
class Solution {
public:
    int orangesRotting(std::vector<std::vector<int>>& grid) {
        int rows = grid.size();
        int cols = grid[0].size();
        int minutes = 0;

        std::queue<std::pair<int, int>> rottenQueue;

        // Count the number of fresh oranges and add rotten oranges to the queue.
        int freshOranges = 0;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 2) {
                    rottenQueue.push({i, j});
                } else if (grid[i][j] == 1) {
                    freshOranges++;
                }
            }
        }

        // Define possible directions to adjacent cells.
        std::vector<std::pair<int, int>> directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

        while (!rottenQueue.empty() && freshOranges > 0) {
            int queueSize = rottenQueue.size();

            for (int i = 0; i < queueSize; i++) {
                std::pair<int, int> curr = rottenQueue.front();
                rottenQueue.pop();

                for (const auto& direction : directions) {
                    int newRow = curr.first + direction.first;
                    int newCol = curr.second + direction.second;

                    if (newRow >= 0 && newRow < rows && newCol >= 0 && newCol < cols &&
                        grid[newRow][newCol] == 1) {
                        grid[newRow][newCol] = 2; // Mark the adjacent fresh orange as rotten.
                        rottenQueue.push({newRow, newCol});
                        freshOranges--;
                    }
                }
            }

            if (!rottenQueue.empty()) {
                minutes++; // Increment the minute if there are more rotten oranges.
            }
        }

        return (freshOranges == 0) ? minutes : -1; // Return -1 if some oranges remain fresh.
    }
};
```

