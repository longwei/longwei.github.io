---
layout: post
title: Basic Calculator
---



```cpp
class Solution {
public:
    int calculate(string s) {
        queue<char> q;
        for (char c: s) {
            if (c != ' ') q.push(c);
        }
        return eval(q);
    }
    int eval(queue<char>& q) {
        stack<int> stk;
        int num = 0;
        char op = '+';
        while(!q.empty()) {
            char c = q.front();q.pop();
            if (c == ' ') continue;
            if (isdigit(c)) {
                num = num*10 + (c - '0');
            }
            
            if (c == '(') {
                num = eval(q);
            }
            // -  12  +
            // op num i
            //because we see another op
            // stk.top() op num
            //then we know what's num, and what's op
            if (!isdigit(c) || q.empty()){
                switch(op) {
                    case '+': stk.push(num); break;
                    case '-': stk.push(-num); break;
                    case '*': stk.top() *= num; break;
                    case '/': stk.top() /= num; break;
                }
                op = c;
                num = 0;
            }
            
            if (c == ')') {
                break;
            }
        }
        int ans = 0;
        while (!stk.empty()) {
            ans += stk.top(); stk.pop();
        }
        return ans;
    }
};
```

优化空间后
```cpp
    int eval(queue<char>& q) {
        int ans = 0;
        int prevNum = 0;
        int num = 0;
        char op = '+';
        while(!q.empty()) {
            char c = q.front();q.pop();
            if (isdigit(c)) {
                num = num*10 + (c - '0');
            }
            if (c == '(') {
                num = eval(q);
            }
            if (!isdigit(c) || q.empty()){
                switch(op) {
                    case '+': 
                        ans += prevNum;
                        prevNum = num;
                        break;
                    case '-': 
                        ans += prevNum;
                        prevNum = -num;
                        break;
                    case '*': 
                        prevNum *= num;
                        break;
                    case '/': 
                        prevNum /= num;
                        break;
                }
                op = c;
                num = 0;
            }
            
            if (c == ')') {
                break;
            }
        }
        ans += prevNum; //<==flush wip result to ans
        return ans;
    }
```