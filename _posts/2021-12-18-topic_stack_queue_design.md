---
layout: post
title: day 11 on stack and queue design
---


## 232. Implement Queue using Stacks

经典的两个stack组成queue， 重点是peek的时候需要把back倒腾到front去
```cpp
class MyQueue {
    stack<int> front_;
    stack<int> back_;
public:
    MyQueue() {}
    
    void push(int x) {
        back_.push(x);
    }
    
    int pop() {
        if (empty()) return -1;
        peek();
        int ret = front_.top();front_.pop();
        return ret;
    }
    
    int peek() {
        if (front_.empty()) {
            while (!back_.empty()) {
                int x = back_.top(); back_.pop();
                front_.push(x);
            }
        }
        return front_.top();
    }
    
    bool empty() {
        return front_.empty() && back_.empty();
    }
};
```

## 225. Implement Stack using Queues

```cpp
class MyStack {
    queue<int> q;
public:
    MyStack() {
        
    }
    
    void push(int x) {
        int k = q.size();
        q.push(x);
        while (k--) {
            int x = q.front(); q.pop();
            q.push(x);
        }
    }
    
    int pop() {
        if (empty()){
            return -1;
        }
        int ret = q.front(); q.pop();
        return ret;
    }
    
    int top() {
        return q.front();
    }
    
    bool empty() {
        return q.empty();
    }
};
```

## 341. Flatten Nested List Iterator

可以把整个数据结构都dump到一个list里面
也可以只留一部分，需要的时候unzip再用
```cpp
class NestedIterator {
    deque<NestedInteger> cache;
public:
    NestedIterator(vector<NestedInteger> &nestedList) {
        for (auto& ni: nestedList) {
            cache.push_back(ni);
        }
    }
    
    int next() {
        if (!hasNext()) return -1;
        int ans = cache.front().getInteger();
        cache.pop_front();
        return ans;
    }
    
    bool hasNext() {
        while (!cache.empty() && !cache.front().isInteger()) {
            vector<NestedInteger> nl = cache.front().getList();
            cache.pop_front();
            for (int i = nl.size() - 1; i >= 0; i--) {
                cache.push_front(nl[i]);
            }
            
        }
        return !cache.empty();
    }
};
```

## 716. Max Stack


最简单的办法是maxStack 和data stack 同步，
要删除的时候，利用一个tmp stack，倒腾出来，pop掉和peekMax()一样的，在恢复回去。
```cpp
class MaxStack {
    stack<int> data;
    stack<int> maxStack;
public:
    MaxStack() {
        
    }
    
    void push(int x) {
        int maxSofar = maxStack.empty() ? x : maxStack.top();
        maxStack.push(max(maxSofar, x));
        data.push(x);
    }
    
    int pop() {
        int ret = data.top(); data.pop();
        maxStack.pop();
        return ret;
    }
    
    int top() {
        return data.top();
    }
    
    int peekMax() {
        return maxStack.top();
    }
    
    int popMax() {
        stack<int> tmp;
        int maxValue = peekMax();
        while (top() != maxValue) {
            tmp.push(pop());
        }
        pop();
        while (!tmp.empty()) {
            push(tmp.top());
            tmp.pop();
        }
        return maxValue;
    }
};
```

更好的办法是用bst在判断最大， 类似LFU
BST of iteratr, 指向一个list, and use vector to store the duplicate element
why not multimap? the order matter!
why remove if empty, iterator validation

```cpp
class MaxStack {
    using IT = list<int>::iterator;
    list<int> l;
    map<int, vector<IT>> mp;
public:
    MaxStack() {}
    
    void push(int x) {
        l.push_front(x);
        mp[x].push_back(l.begin());
    }
    
    int pop() {
        int x = l.front(); l.pop_front();
        removeKey(x);
        return x;
    }
    
    int top() {
        return *l.begin();
    }
    
    int peekMax() {
        return mp.rbegin()->first;
    }
    
    int popMax() {
        int x = peekMax();
        auto it = mp[x].back();
        l.erase(it);
        removeKey(x);
        return x;   
    }
    
private:
    void removeKey(int x) {
        mp[x].pop_back();
        if (mp[x].empty()) {
            mp.erase(x);
        }
    }
};
```

## 353. Design Snake Game

1. 需要注意的是，是先动尾巴还是先动头
2. unordered_set/map 没有default的hash function

```cpp
class SnakeGame {
    int W, H;
    int pos = 0;
    vector<vector<int>> food;
    set<pair<int,int>> hist;//<<== no hash function
    deque<pair<int,int>> q;
public:
    SnakeGame(int width, int height, vector<vector<int>>& food) {
        this->food = food;
        W = width, H = height;
        pos = 0;
        q.push_back({0, 0});
        
    }
    
    int move(string direction) {
        auto [row, col] = q.front();
        auto tail = q.back(); q.pop_back();
        hist.erase(tail);
        
        if (direction == "U")
            row--;
        else if (direction == "D")
            row++;
        else if (direction == "L")
            col--;
        else if (direction == "R")
            col++;
        if (!onboard(row, col) || hist.count({row, col})) {
            return -1;//wall
        }
        
        hist.insert({row, col});
	    q.push_front({row, col});
        
        if (pos >= food.size()) {
            return q.size() - 1;
        }
        
        if (row == food[pos][0] && col == food[pos][1]) {
		    pos++;
		    q.push_back(tail);
		    hist.insert(tail);
        }
        
        return q.size() - 1;
        
    }
    
    int onboard(int r, int c) {
        return (0 <= r && r < H) && (0 <= c && c < W);
    }
};
```




