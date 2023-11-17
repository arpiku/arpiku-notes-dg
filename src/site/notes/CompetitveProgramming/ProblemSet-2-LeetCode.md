---
{"dg-publish":true,"permalink":"/competitve-programming/problem-set-2-leet-code/"}
---

## Search in Rotated SubArray
- Interesting approach but doesn't work need to fix
- The idea 
	- Find k
	- Find the element in the array.
- The pivot points can be at 0 implying no rotation has happened, or at n-1 implying the array is sorted in reverse order.
- 
```cpp
class Solution {
public:
    int binarySearch(vector<int>& nums, int target, bool pivoted) {
        int l = 0;
        int r = nums.size() -1;
        int mid = 0;
        while(r>=l) {
            if(nums[mid] == target) 
                return mid;
            else if  ((nums[mid] > target)^pivoted) {
                l = mid + 1;
            }
            else {
                r = mid - 1;
            }
            mid = l + (r-l)/2;

        }
        return  -1;
    }

    int getPivotPoint(vector<int>& nums, int l, int r) {
        if(r < l)
            return -1;
        int mid = l + (r-l)/2;
        if(mid == nums.size()-1 || nums[mid+1] < nums[mid]) {
            return mid;
        };
        if(nums[l] < nums[mid])
            return getPivotPoint(nums,mid,r);
        else
            return getPivotPoint(nums,l,mid);
    }



    int search(vector<int>& nums, int target) {
        const int n = nums.size()-1;
        bool pivoted = false;
        int k = -1;
        if(nums[0] >= nums[n])
            pivoted = true;
        if(!pivoted)
            return binarySearch(nums,target,false);
        k = getPivotPoint(nums,0,n);
        if(nums[k] == target)
            return k;
        if(k == n)
            return binarySearch(nums,target,pivoted);
        else if(nums[k] > target) {
            std::vector<int> temp = {nums.begin()+k+1,nums.end()};
            for(auto& i:temp)
                std::cout << i << " ";
            return k + 1 + binarySearch(temp,target,false);
        }
        std::vector<int> temp = {nums.begin(),nums.begin()+k+1};
            for(auto& i:temp)
                std::cout << i << " ";
        return binarySearch(temp,target,false);
        
        
    }
};
```
- If I had to do it quickly and cheekly
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        auto iter = std::find(nums.begin(),nums.end(),target);
        auto idx = std::distance(nums.begin(),iter);
        if(idx == nums.size())
            return -1;
        return idx;        
    }
};
```


## Combinations:
- [[Data_Structures/array\|array]] [[backTracking\|backTracking]]
```cpp


```

## Permutations
- [[backTracking\|backTracking]] [[Data_Structures/array\|array]]
```cpp
class Solution {
public:
    int binarySearch(vector<int>& nums, int target, bool pivoted) {
        int l = 0;
        int r = nums.size() -1;
        int mid = 0;
        while(r>=l) {
            if(nums[mid] == target) 
                return mid;
            else if  ((nums[mid] > target)^pivoted) {
                l = mid + 1;
            }
            else {
                r = mid - 1;
            }
            mid = l + (r-l)/2;

        }
        return  -1;
    }

    int getPivotPoint(vector<int>& nums, int l, int r) {
        if(r < l)
            return -1;
        int mid = l + (r-l)/2;
        if(mid == nums.size()-1 || nums[mid+1] < nums[mid]) {
            return mid;
        };
        if(nums[l] < nums[mid])
            return getPivotPoint(nums,mid,r);
        else
            return getPivotPoint(nums,l,mid);
    }



    int search(vector<int>& nums, int target) {
        const int n = nums.size()-1;
        bool pivoted = false;
        int k = -1;
        if(nums[0] >= nums[n])
            pivoted = true;
        if(!pivoted)
            return binarySearch(nums,target,false);
        k = getPivotPoint(nums,0,n);
        if(nums[k] == target)
            return k;
        if(k == n)
            return binarySearch(nums,target,pivoted);
        else if(nums[k] > target) {
            std::vector<int> temp = {nums.begin()+k+1,nums.end()};
            for(auto& i:temp)
                std::cout << i << " ";
            return k + 1 + binarySearch(temp,target,false);
        }
        std::vector<int> temp = {nums.begin(),nums.begin()+k+1};
            for(auto& i:temp)
                std::cout << i << " ";
        return binarySearch(temp,target,false);
        
        
    }
};
```

- Why is this not allowed in a class:
```cpp
    auto sumArr = (const std::vector<int>& vecForSum)[] {
        int sum = 0;
        for(auto& i:vecForSum) {
            sum += i;
        }
        return sum;
    };
```


## Merge Intervals
- [[Data_Structures/array\|array]] [[sorting\|sorting]]
```cpp
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& nums) {
       vector<vector<int>>v;
       int start;
       int end;
       int i;
        sort(nums.begin(), nums.end(), [](const vector<int>& a, const vector<int>& b) {
           return a[0] < b[0];
       });
       
       for( i=0;i<nums.size();i++)
       {
           int start=nums[i][0];
           int end=nums[i][1]; 
            while(i!=nums.size()-1 && end>=nums[i+1][0])
           {
               end= max(end,nums[i+1][1]);
               i++;
           }
           
           
           vector<int> temp={start,end};
           v.push_back(temp);
       } 
       return v;
    }
};
```


## Lowest Common Ancestor of Binary Tree
```cpp
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(!root)
            return NULL;
        if(root->val == p->val || root->val == q->val) return root;

        TreeNode* lst = lowestCommonAncestor(root->left,p,q);
        TreeNode* rst = lowestCommonAncestor(root->right,p,q);
        if(!lst) return rst;
        if(!rst) return lst;

        return root;
    }
};
```

## Time Based Key-Value Store
- [[Data_Structures/hashMap\|hashMap]] [[string\|string]] [[binarySearch\|binarySearch]] 
```cpp
class TimeMap {
public:
    map<pair<string, int>, string> mp;
    map<string, string> mp2;

    TimeMap() {
        
    }
    
    void set(string key, string value, int timestamp) {
        mp[{key, timestamp}] = value;
        mp2[key] = value;
    }
    
    string get(string key, int timestamp) {
        if(mp2.find(key) == mp2.end()) return "";
        else {
            auto it = mp.upper_bound({key, timestamp});
            if (it == mp.begin()) return "";
            it--;
            return mp[it->first];
        }
    }
};
```

## Accounts Merge
- [[Data_Structures/array\|array]] [[Data_Structures/hashMap\|hashMap]] [[String\|String]] [[dfs\|dfs]] [[Algorithms/bfs\|bfs]] [[unionFind\|unionFind]] [[sorting\|sorting]]
```cpp
class DSU{
    private:
        const int N = 1e6+10;
        vector<int> parent, size, rank;
    public:
        DSU(){
            parent.resize(N);
            iota(begin(parent),end(parent),0);
            size.resize(N,1);
            rank.resize(N,0);
        }
        DSU(int n){
            parent.resize(n+1);
            iota(begin(parent),end(parent),0);
            size.resize(n+1,1);
            rank.resize(n+1,0);
        }
        int find(int v){
            if(parent[v] == v) return v;
            //path compression
            return parent[v] = find(parent[v]);
        }
        void unionS(int a,int b){
            a = find(a);
            b = find(b);
            if(a!=b){
                //union by size
                if(size[a] < size[b]) swap(a,b);
                parent[b] = a;
                size[a] += size[b];
            }
        }
        void unionR(int x, int y) {
            x = find(x), y = find(y);
            if(x == y) return;
            //union by rank
            else if(rank[x] < rank[y]) parent[x] = y;
            else if (rank[x] > rank[y]) parent[y] = x;
            else parent[y] = x,rank[x]++;
      }
};
class Solution {
private:
    unordered_map<string,int> mergeEmails(vector<vector<string>>& A,DSU& ds){
        int n = size(A);
        unordered_map<string,int> mp;
        for(int i=0; i<n; ++i){
            int m = size(A[i]);
            for(int j=1; j<m; ++j){
                if(mp.count(A[i][j])) ds.unionS(i,mp[A[i][j]]);
                mp[A[i][j]] = i;
            }
        }
        return mp;
    }
    unordered_map<int,vector<string>> emailsMapping(unordered_map<string,int>& mp,DSU& ds){
        unordered_map<int,vector<string>> merge;
        for(auto&&[email,index]: mp){
            index = ds.find(index);
            merge[index].push_back(email);
        }
        return merge;
    }
    vector<vector<string>> buildAnswer(vector<vector<string>>& A,unordered_map<int,vector<string>>& merge){
        vector<vector<string>> ans;
        for(auto&&[index,emails]: merge){
            vector<string> person {A[index][0]};
            person.insert(end(person),begin(emails),end(emails));
            sort(begin(person)+1,end(person));
            ans.push_back(person);
        }
        return ans;
    }
public:
    vector<vector<string>> accountsMerge(vector<vector<string>>& A) {
        int n = size(A);
        DSU ds(n-1);
        auto merge_emails = mergeEmails(A,ds);
        auto emails_mapping = emailsMapping(merge_emails,ds);
        auto ans = buildAnswer(A,emails_mapping);
        return ans;
    }
};
```

## Sort Colours
```cpp
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int low = 0; // Pointer for 0s
        int mid = 0; // Pointer for 1s
        int high = nums.size() - 1; // Pointer for 2s
        
        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums[low], nums[mid]);
                low++;
                mid++;
            } else if (nums[mid] == 1) {
                mid++;
            } else {
                swap(nums[mid], nums[high]);
                high--;
            }
        }
    }
};
```

## Word Break

```cpp
class Solution {
public:
    vector<int> dp;
    bool solve(int i, string s, vector<string>& wordDict){
        if (i < 0) return 1;
        if (dp[i] != -1) return dp[i] == 1;
        for (string& w : wordDict) {
            int sz = w.size();
            if (i - sz + 1 < 0) continue;
            if (s.rfind(w, i-sz+1)== i-sz+1 && solve(i - sz, s, wordDict)) {
                dp[i] = 1;
                return 1;
            }
        }
        dp[i] = 0;
        return 0;
    }

    bool wordBreak(string s, vector<string>& wordDict) {
        int&& n = s.size();
        dp.assign(n, -1);
        return solve(n - 1, s, wordDict );
    }
};
```




## Remove the Nth Node from LL
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
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode *slow=head,*fast=head;
        while(n--){
            fast=fast->next;
        }
        if(fast==NULL){
            return slow->next;
        
        }
        while(fast->next != NULL){
            slow=slow->next;
            fast=fast->next;
        }
        slow->next = slow->next->next;
        return head;
        }

        
    
};
```


## Find the duplicate number
```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        unordered_map<int,int> m;
        for(auto& i:nums) {
            m[i]++;
            if(m[i] > 1)    
                return i;
        }
        return -1;

    }
};
```

```cpp
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        while(true) {
            if(nums[nums[0]] == nums[0]) {
                return nums[0];
            }
            swap(nums[0],nums[nums[0]]);
        }
        return -1;
        
    }
};
```


## Top K Most Frequent words
- [[Data_Structures/hashMap\|hashMap]] [[Data_Structures/trie\|trie]] [[String\|String]] [[sorting\|sorting]] [[heap\|heap]] [[prioirtyQueue\|prioirtyQueue]] [[bucket sort\|bucket sort]] [[counting\|counting]]
```cpp
class Solution {
public:
    vector<string> topKFrequent(vector<string>& words, int k) {
        std::unordered_map<std::string,int> m;
        std::vector<std::pair<std::string,int>> v;
        std::vector<std::string> ans;

        for(string s : words) {
            m[s]++;
        };
        copy(m.begin(),m.end(), std::back_inserter<vector<pair<string,int>>> (v));
        sort(v.begin(),v.end(), [] (const pair<string, int>& a, const pair<string,int>& b) {
            if(a.second == b.second)
                return (a.first < b.first);
            return a.second > b.second;
        });

        for(std::pair p:v) {
            ans.push_back(p.first);
            if(--k == 0)
                break;
        }

        return ans;


        
    }
};
```