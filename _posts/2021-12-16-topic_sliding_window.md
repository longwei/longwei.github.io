---
layout: post
title: leetcode sliding window
---


## 643. Maximum Average Subarray I

We have two techniques for getting these sums efficiently: prefix sums, or sliding window
1. the sum between a range => prefix sum
2. mark the variant j, check when to pop, when to do avg, which one come first.
3. another sliding window is easier to understand, but first total [0...k-1], then from [k, n), `sum += num[i] - num[i-k]`

```cpp
class Solution {
public:
    double findMaxAverage(vector<int>& nums, int k) {
        int n = nums.size();
        vector<int> prefix(n);
        prefix[0] = nums[0];
        for (int i = 1; i < n; i++) {
            prefix[i] = prefix[i-1] + nums[i];
        }
        int total = prefix[k-1];
        for (int i = k; i < n; i++) {
            total = max(total, prefix[i] - prefix[i-k]);
        }
        return (double) total / k;
    }
};

class Solution {
public:
    double findMaxAverage(vector<int>& nums, int k) {
        int ans = INT_MIN;
        int sum = 0;
        for (int i = 0; i < nums.size(); i++) {
            sum += nums[i];
            int j = i - k + 1;
            if (i - k + 1  >= 1) {
                sum -= nums[j-1];
            }
            if (i - k + 1 >= 0) {
                ans = max(ans, sum);
            }
        }
        return (double) ans / k;
    }
};
```

## 209. Minimum Size Subarray Sum

### min windows, when to reduce the size?
1. when the condition meet, reduce the window as much as possbile, update the best
2. if the init state could be invalid, then double check before return it 

```cpp
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int i = 0, n = nums.size(), sum = 0, ans = INT_MAX;
        for (int j = 0; j < n; j++) {
            sum += nums[j];
            while (sum >= target) {
                ans = min(ans, j-i+1);
                sum -= nums[i++];
            }
        }
        return ans == INT_MAX ? 0: ans;
    }
};
```

## 1151. Minimum Swaps to Group All 1's Together
fix windows with max sum
```cpp
class Solution {
public:
    //fix windows with max sum
    int minSwaps(vector<int>& data) {
        int len = 0;
        for (int x: data) {
            len += x;
        }
        
        int i = 0, sum = 0, ans = 0, n = data.size();
        for (int j = 0; j < n; j++) {
            sum += data[j];
            while (j-i+1 > len) {
                sum -= data[i++];
            }
            ans = max(ans, sum);
        }
        return len - ans;
    }
};
```
## 713. Subarray Product Less Than K

1. sliding window works only because input are postitive
2. if k <=1, we never going to make it, `if (k <= 1) return 0;`
3. find a max array that sum < k, this is set of contiguous array that starts at i, then number of array is the length of this range.


```cpp
class Solution {
public:
    //find a max array that sum < k
    //each ending j => ans += j-i+1
    int numSubarrayProductLessThanK(vector<int>& nums, int k) {
        if (k <= 1) return 0;
        int i = 0, n = nums.size(), product = 1, ans = 0;
        for (int j = 0; j < n; j++) {
            product *= nums[j];
            while (product >= k) {
                product /= nums[i];
                i++;
            }
            ans += j-i+1;
        }
        return ans;
    }
};
```

### Binary Search on Logarithms
log k and log each element, so the product problem => sum problem, for each i, find a j so that sum [i,j] < k

last of mid that, prefix[mid] - prefix[i] < k
first of prefix[mid] - prefix[i] + little, then lo--
then lo++, to be `[i, j] j+i`, it is prefix[lo] - prefix[i]

```cpp
class Solution {
    public int numSubarrayProductLessThanK(int[] nums, int k) {
        if (k == 0) return 0;
        double logk = Math.log(k);
        double[] prefix = new double[nums.length + 1];
        for (int i = 0; i < nums.length; i++) {
            prefix[i+1] = prefix[i] + Math.log(nums[i]);
        }

        int ans = 0;
        for (int i = 0; i < prefix.length; i++) {
            int lo = i+1, hi = prefix.length;
            //last of mid that, prefix[mid] - prefix[i] < k
            //first of prefix[mid] - prefix[i] + little
            // then lo-- 
            while (lo < hi) {
                int mi = lo + (hi - lo) / 2;
                if (prefix[mi] - prefix[i] >= logk - 1e-9) {
                    hi = mi;
                } else {
                    lo = mi + 1;
                }
            }
            //lo is upper_bound
            ans += lo - i - 1;
        }
        return ans;
    }
}
```

## 1052. Grumpy Bookstore Owner

```cpp
class Solution {
public:
    int maxSatisfied(vector<int>& customers, vector<int>& grumpy, int k) {
        int n = customers.size();
        vector<int> unhappy(n);
        int total = 0;
        for (int c: customers) {
            total += c;
        }
        int totalUnhappy = 0;
        for (int i = 0; i < n; i++) {
            unhappy[i] = customers[i] * grumpy[i];
            totalUnhappy += unhappy[i];
        }
        
        //find the fixed window size k have the max sum
        int i = 0, saved = 0, sum = 0;
        for (int j = 0; j < n; j++) {
            sum += unhappy[j];
            while (j-i+1 > k) {
                sum -= unhappy[i++];
            }
            saved = max(saved, sum);
        }
        return total - (totalUnhappy-saved);
    }
};
```





