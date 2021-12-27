---
layout: post
title: leetcode retro
---


## 704. Binary Search

```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int lo = 0, hi = nums.size() - 1;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (nums[mid] >= target) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        if (nums[lo] != target) return -1;
        return lo;
    }
};
```

## 1539. Kth Missing Positive Number
any element <= than k will bump the k

```cpp
class Solution {
public:
    int findKthPositive(vector<int>& arr, int k) {
        int inserted = 0;
        for (int e: arr) {
            if (e <= k) {
                k++;
            }
        }
        return inserted + k;
    }
    
    // if no missing number, then arr[i] - i = 1, and k 
    // if arr[i] - i - 1 >= k, then k+i is ans
    // if i is totally out n, then k + len
    // if i stop at 0, then k + 0;
    // then arr[i] - i - 1 is increasing array
    // find the first one that >= k
    int findKthPositive(vector<int>& arr, int k) {
        int lo = 0, hi = arr.size();
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (arr[mid] - mid - 1 >= k){
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        return k + lo;
    }
};
```

## 349. Intersection of Two Arrays

1. asking for unique, so set_intersection of two set
2. solve it in stream

```cpp
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        set<int> seen(nums1.begin(), nums1.end());
        set<int> seen2(nums2.begin(), nums2.end());
        vector<int> ans;
        for (int num: seen2) {
            if (seen.count(num)){
                ans.push_back(num);
            }
        }
        return ans;
    }
};

class Solution {
public:
    vector<int> intersection(vector<int>& a, vector<int>& b) {
        sort(a.begin(),a.end());
        sort(b.begin(),b.end());
        int i = 0, j = 0;
        int m = a.size();
        int n = b.size();
        vector<int> ans;
        while (i < m && j < n) {
            int l = a[i], r = b[j];
            if (l == r) {
                ans.push_back(l);
                // while (i < a.size() && a[i] == ans.back()) i++;
                // while (j < b.size() && b[j] == ans.back()) j++;
                i = upper_bound(a.begin() + i, a.end(), l) - a.begin();
                j = upper_bound(b.begin() + j, b.end(), l) - b.begin();
            } else if (l < r) {
                //move i to the first of > l
                // i++;
                i = upper_bound(a.begin() + i, a.end(), l) - a.begin();
            } else {
                //move j to the first > r
                // j++;
                j = upper_bound(b.begin() + j, b.end(), r) - b.begin();
            }
        }
        return ans;
        
    }
};
```

## 33. Search in Rotated Sorted Array

```cpp
class Solution {
public:
    int search(vector<int>& A, int x) {
        int n = A.size();
        int lo = 0, hi = n - 1;
        while (lo + 1 < hi) {
            int mid = lo + (hi - lo ) / 2;
            if (A[lo] <= A[mid]){
                //left
                if (A[lo] <= x && x <= A[mid]){
                    hi = mid;
                } else {
                    lo = mid;
                }
            } else {
                if (A[mid] <= x && x <= A[hi]){
                    lo = mid;
                } else {
                    hi = mid;
                }
            }
        }
        if (A[lo] == x) return lo;
        if (A[hi] == x) return hi;
        return -1;
    }
};
```

## 162. Find Peak Element

find any peak, find first of nums[i] > nums[i+1]

```cpp
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int lo = 0, hi = nums.size() - 1;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (nums[mid] > nums[mid+1]) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        return lo;
        
    }
};
```
====

## 1482. Minimum Number of Days to Make m Bouquets
## 1283. Find the Smallest Divisor Given a Threshold
## 1231. Divide Chocolate
## 1011. Capacity To Ship Packages In N Days
## 875. Koko Eating Bananas
## 774. Minimize Max Distance to Gas Station
## 410. Split Array Largest Sum

