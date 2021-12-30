---
layout: post
title: Prefix Sum
---

prefix Sum 用于cache了加法结果，使得sum of range立刻可以得到结果, 注意前面要加0， 值得prefix存的是`[)`的方式，
每次query要`prefix[right+1] - prefix[left]`

`nums:.....[3, 5, 2,-2, 4, 1]`

`prefix: [0,3 ,8,10,8 ,12,13]`

题外话话，为啥要题意要强调immutable?
因为prefixsum是一次性的cache结果，如果第一个元素变了，后面的prefixsum全要重新计算
解决办法是BIT， 或者fenwick tree

## 303. Range Sum Query - Immutable

```cpp
class NumArray {
    vector<int> prefix;
public:
    NumArray(vector<int>& nums) {
        int n = nums.size();
        prefix.resize(n+1);
        prefix[0] = 0;
        for (int i = 1; i <= n; i++) {
            prefix[i] = prefix[i-1] + nums[i-1];
        }
        
    }
    
    int sumRange(int left, int right) {
        return prefix[right+1] - prefix[left];
    }
};
```

## 304. Range Sum Query 2D - Immutable

画画框框就明白这里面的加减办法了
```cpp
class NumMatrix {
    
public:
    NumMatrix(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        if (m == 0 || n == 0) return;
        prefix2D.assign(m+1, vector<int>(n+1));
        
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                prefix2D[i][j] = prefix2D[i-1][j] + prefix2D[i][j-1] +
                    matrix[i-1][j-1] - prefix2D[i-1][j-1];
            }
        }
        
    }
    
    int sumRegion(int row1, int col1, int row2, int col2) {
        return prefix2D[row2+1][col2+1] - prefix2D[row1][col2+1]
               + prefix2D[row1][col1]   - prefix2D[row2+1][col1];          
    }
    
private:
    vector<vector<int>> prefix2D;
};
```

## 560. Subarray Sum Equals K
prefix sum 也是可以继续优化的哟
`i [0, n)` 和 `j [0, i)` 中找`prefix[i+1] - prefix[j] == k` 但是居然LTE了, 想想还能怎么优化？

```cpp
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> prefix(n+1);
        prefix[0] = 0;
        for (int i = 1; i <= n; i++) {
            prefix[i] = prefix[i-1] + nums[i-1];
        }
        int ans = 0;
        for (int i = 1; i <= n; i++) {
            for (int j = 0; j < i; j++) {
                if (prefix[i] - prefix[j] == k) {
                    ans++;
                }
            }
        }
        return ans;
    }
};
```

注意内循环, 是一个寻找j的过程 `prefix[j] == prefix[i] - k`
记录`prefix[i] - k`的个数， 每次找到新的`prefix[i']`后，去撞一撞, 注意边界条件， 如果prefix_i本身刚好就是k了，那么另一半就是0，要么单独处理，要么加入0=>1在counter里面

```cpp
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int ans = 0;
        int prefix_i = 0;
        unordered_map<int,int> prefixCount;
        prefixCount[0] = 1;
        //[j, i]
        for (int num: nums) {
            prefix_i += num;
            // if (prefix_i == k) ans++;
            
            int prefix_j = prefix_i - k;
            ans += prefixCount[prefix_j];
            prefixCount[prefix_i]++;
        }
        return ans;
             
    }
};
```