---
layout: post
title: map reduce
---

## Word Count
Using map reduce to count word frequency

```cpp
```

## Top K Frequent Words (Map Reduce)
Find top k frequent words with map reduce framework.

The mapper's key is the document id, value is the content of the document, words in a document are split by spaces.

For reducer, the output should be at most k key-value pairs, which are the top k words and their frequencies in this reducer. The judge will take care about how to merge different reducers' results to get the global top k frequent words, so you don't need to care about that part.

The_k_is given in the constructor of TopK class.

Notice
For the words with same frequency, rank them with alphabet.


```cpp
```

## Inverted Index (Map Reduce)
Use map reduce to build inverted index for given documents.
想一想，如何在reducer里面排出重复，为什么拿到的list of int是排序过的？
`key:word, val: id`

```cpp
class InvertedIndexMapper: public Mapper {
public:
    void Map(Input<Document>* input) {
        while(!input->done()) {
            Document cur = input->value();
            int id = cur.id;
            istringstream iss(cur.content);
            string token;
            while (iss >> token) {
                output(token,id);
            }
            input->next();
        }
    }
};


class InvertedIndexReducer: public Reducer {
public:
    void Reduce(string &key, Input<int>* input) {
        vector<int> ans;
        int previous = -1;
        while (!input->done()) {
            int now = input->value();
            if (previous != now) {
                ans.push_back(now);
            }
            input->next();
            previous = now;
        }
        output(key, ans);
    }
};
```

## Anagram (Map Reduce)

Use Map Reduce to find anagrams in a given list of words.

Example

Given["lint", "intl", "inlt", "code"], return["lint", "inlt", "intl"],["code"].

Given["ab", "ba", "cd", "dc", "e"], return["ab", "ba"], ["cd", "dc"], ["e"].

`k:sorted(word),v: word`

```cpp
class AnagramMapper: public Mapper {
public:
    void Map(Input<string>* input) {
        while (!input->done()) {
            istringstream iss (input->value());
            string word;
            while (iss >> word) {
                string key = word;
                sort(key.begin(), key.end());
                output(key, word);
            }
            input->next();
        }
    }
};


class AnagramReducer: public Reducer {
public:
    void Reduce(string &key, Input<string>* input) {
        vector<string> ans;
        while (!input->done()) {
            string val = input->value();
            ans.push_back(val);
            input->next();
        }
        output(key, ans);
    }
};
```