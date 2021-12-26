---
layout: post
title: leetcode contest biweekly 68,  weekly 273
---

## 2115. Find All Possible Recipes from Given Supplies
it is basically topo sort, remember from->to when building the graph.
```cpp
class Solution {
public:
    vector<string> findAllRecipes(vector<string>& recipes, vector<vector<string>>& ingredients, vector<string>& supplies) {
        unordered_map<string, vector<string>> graph;
        unordered_map<string, int> indegree;
        for (int i = 0; i < recipes.size(); i++) {
            string to = recipes[i];
            for (auto& from: ingredients[i]) { 
                graph[from].push_back(to); //<<--
                indegree[to]++;
            }
        }
        
        queue<string> q;
        for (string& seed: supplies) {
            q.push(seed);
        }
        
        unordered_set<string> need(recipes.begin(), recipes.end());
        vector<string> order;
        while (!q.empty()) {
            string cur = q.front(); q.pop();
            if (need.count(cur)){
                order.push_back(cur);
            }
            for (string to: graph[cur]) {
                indegree[to]--;
                if (indegree[to] == 0) {
                    q.push(to);
                }
            }
        }
        return order;
    }
};
```

## 2116. Check if a Parentheses String Can Be Valid

because we only have one kind of parentheses. then we can simpify the stack problem into checking balance of counting ```()```.
one trick for any parentheses validation is greedily checking from left-to-right, and right-to-left, to check for any orphan '(' or ')'

* ->: flex + open must always >= close
* <-: flex + close must always >= open

```cpp
class Solution {
public:
    bool canBeValid(string s, string locked) {
        int flex = 0, open = 0, close = 0;
        int n = s.size();
        if (n % 2 == 1) return false;
        for (int i = 0; i <n; i++) {
            if (locked[i] == '0') {
                flex++;
            } else if (s[i] == '(') {
                open++;
            } else if (s[i] == ')') {
                close++;
            }
            // cout << flex << " " << open << " " << close << endl;
            if (flex + open < close) return false;
        }
        flex = open = close = 0;
        for (int i = n-1; i >= 0; i--) {
            if (locked[i] == '0') {
                flex++;
            } else if (s[i] == '(') {
                open++;
            } else if (s[i] == ')') {
                close++;
            }
            // cout << flex << " " << open << " " << close << endl;
            if (flex + close < open) return false;
        }
        return true;
    }
};
```

## 2120. Execution of All Suffix Instructions Staying in a Grid
simple BFS on matrix

```cpp
class Solution {
public:
    vector<int> executeInstructions(int n, vector<int>& startPos, string s) {
        int m = s.size();
        vector<int> ans;
        for (int i = 0; i < m; i++) {
            string instruction = s.substr(i);
            ans.push_back(countStep(n, startPos, instruction));
        }
        return ans;
    }
    
    int countStep(int n, vector<int>& startPos, string order) {
        // cout << order << endl;
        int row = startPos[0], col = startPos[1];
        int step = 0;
        for (char o: order) {
            if (o == 'L') {
                col--;
            } else if (o == 'R'){
                col++;
            } else if (o == 'U'){
                row--;
            } else if (o == 'D'){
                row++;
            }
            if (row < 0 || col < 0 || row >= n || col >= n) break;
            step++;
        }
        return step;
        
    }
};
```

## 2121. Intervals Between Identical Elements

first idea was to build a val->idxs structure,
then for each query of (val, idx), brute force calculate the sum of abs diff. this approach will LTE
notice that there is o(n^2) in the second part.
so we could prefix sum that index array

so the idea is to still keep the record of all the index, preprocess the data as much as possible,
and build a lookup table for the queries,
such as a two keys look up table, from (val, idx) to sum of intervals

1. first build value to list of idx
2. then preprocess the left scan and right scan using prefix sum
3. given the val, and the original idx, and prefix of left and right, build the desired lookup table.

For example:
let the input as [4,2,7,2,4,4,5]
and index as ....[0,1,2,3,4,5,6]

step 1. the val_idxs should be:
```
key: 1->{1 3 }
key: 2->{0 4 }
key: 3->{2 5 6 }
```

step 2.1: 
then left prefix interval sum:
```
key: 1->{0 2 }
key: 2->{0 4 }
key: 3->{0 3 5 }
```
step 2.2: 
then right prefix interval sum:
```
key: 1->{2 0 }
key: 2->{4 0 }
key: 3->{7 1 0 }
```

step 3, 
the final look up table is 
```
(val:1, idx: 1) -> 2
(val:1, idx: 3) -> 2
(val:2, idx: 0) -> 4
(val:2, idx: 4) -> 4
(val:3, idx: 2) -> 7
(val:3, idx: 5) -> 4
(val:3, idx: 6) -> 5
```


```cpp
class Solution {
public:
    vector<long long> getDistances(vector<int>& arr) {
        unordered_map<int, vector<int>> val_idxs;
        unordered_map<int, map<int, long long>> val_idx_sum;
		//step1
        for (int i = 0; i < arr.size(); i++) {
            int x = arr[i];
            val_idxs[x].push_back(i);
        }
		
        for (auto& [key, idxs]: val_idxs) {
            int k = idxs.size();
            // step 2.1
            vector<long long> l(k);
            for (int i = 1; i < k; i++) {
                l[i] = abs(idxs[i-1] - idxs[i]) * i + l[i-1];
            }
			// step 2.2
            vector<long long> r(k);
            for (int i = k - 2; i>= 0; i--) {
                int step = k- 1 - i;
                r[i] = abs(idxs[i+1] - idxs[i]) * step + r[i+1];
            }
            // step 3
            for (int i = 0; i < k; i++) {
                int idx = idxs[i];
                val_idx_sum[key][idx] = l[i] + r[i];
            }
        }
        
        
        vector<long long> ans;
        for (int i = 0; i < arr.size(); i++) {
            int x = arr[i];
            long long sum = 0;
            ans.push_back(val_idx_sum[x][i]);
        }
        return ans;
    }
};
```
## 2122. Recover the Original Array

### try to guess the original array from a silly hash function.
* what's is the k
* how to recover the original array if we know k

there is no smart way to get the k, just try all possible ones
```nums[i] - nums[0]```, then remove the diff that's 0 or not even

now we have a k, how to divide the array into two groups, so that they have the same pattern but diff by 2k at each position?

let's look at anther problem [954. Array of Doubled Pairs](https://leetcode.com/problems/array-of-doubled-pairs/)

so the idea is to build a Counter on the arr, sort the array, , match from smallest

### why we start from smallest?
if x is in the smaller final array, then there must exist a `2*x`, and must not exisit a `x/2`

take [2,4,|3,6], sort it to [2,3,|4,6], the smaller array is always at the left half after sorting. it is not about the relative position the x is in the whole array, it is about the relative position to the bigger counter part.

### why a freq counter?
then when we see 2, we take out 4. when we see 3, we take out 6. if there is not a counter part, then we can't make it


### what about the negative number? 
normal way is also start from the smallest but match x/2 instead. or sort the array by abs value, so both positive/negative number can have the same logic, the positive start from smallest, and the negative starts from biggest.

```cpp
class Solution {
public:
    bool canReorderDoubled(vector<int>& arr) {
        unordered_map<int,int> counter;
        for (int x: arr) counter[x]++;
        
        auto sortByAbs = [](int a, int b) {return abs(a) < abs(b);};
        sort(arr.begin(), arr.end(), sortByAbs);
        
        for (int x: arr){
            if (counter[x] == 0) continue;
            if (counter[2*x] == 0) return false;
            counter[x]--;
            counter[2*x]--;
        }
        return true;
    }
};
```

Now, back to our 2122. Recover the Original Array.
we generate all possible k, then try to see whether we could divide the nums into two groups.
