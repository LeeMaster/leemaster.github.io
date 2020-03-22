---
title: Trie 这个东西
date: 2019-11-06 00:50:49
categories: [Datasruct]
tags:
- ds
---

在美团的工作中，稳定性建设一直是我毕业到现在整个业务组的主旋律，很荣幸，我负责整个系统的稳定性建设，最近出现了一个新的需求，希望可以提供用户可配置的方式来实现接口级别的熔断，路由匹配大多数都是改造Radix树，正好复习一下这三棵树。

<!--more-->

# Trie 

ACM常用模板之一，字典树。性质如下

* 每一条从根节点到任意节点路径都代表一个字符串。
* 两个节点相连的边都被字符串中的一个元素所标记。
* 字符串集合中，最长字符串的长度为整个Trie的高度

## 实现难点

* 如何实现搜索操作（插入操作的前提）
* 如何实现删除操作

## 实现

```cpp
//// trie.hpp
#ifndef _TRIE_HPP_
#define _TRIE_HPP_
#include <map>
#include <vector>
#include <string>
struct TrieNode{
	bool isLeaf;
  std::map<char,TrieNode*> edges;
  void * data;
}
class Trie {
	public:
  	Trie();
  	bool contains(const std::string & key)const;
  	void remove(const std::string & key);
  	bool insert(const std::string & key,void * data);
  	void* seek(const std::string & key) const;
  	~Trie();
  private:
  	inline bool isEmpty(TrieNode * node){
    	return node -> edges.empty();
    }
  	inline bool isLeaf(TrieNode * node){
    	return node -> isLeaf;
    }
  	TrieNode* _remove(TrieNode * root,const std::string & key,int depth=0);
  	TrieNode * root;
};
#endif

//// trie.cpp 
#include "trie.hpp"

Trie::Trie(){
	root = new TrieNode{false,std::map<char,TrieNode*>(),nullptr};
}

bool Trie::contains(const std::string& key)const{
	if (root -> edges.empty())return false;
  TrieNode * node = root;
  for(auto it = key.cbegin();it != key.cend();it++){
  	 auto temp = node -> edges.find(*it);
     if (temp == node -> edges.end())return false;
     node = temp -> second;
  }
  return (node != nullptr && node -> isLeaf);
}

TrieNode* Trie::_remove(TrieNode * root,const std::string & key,int depth){
	if (root == nullptr) return nullptr;
  if (key.size() == size_t(depth)){
  	if (isLeaf(root))
      	root -> isLeaf = false;
    if (isEmpty(root)){
    	delete root;
      root = nullptr;
    }
   	return root;
  }

  auto index = key[depth];
  // 递归地删除一波
  root -> edges[index] = _remove(root -> edges[index],key,depth + 1);

  if (isEmpty(root)){
  	delete root;
    root = nullptr;
  }
  return root;
}

void Trie::remove(const std::string& key){
	_remove(this -> root,key);
}

bool Trie::insert(const std::string & key,void * data){
	TrieNode* node = root;
  for(auto it = key.cbegin();it != key.cend();it++){
  	if (node -> edges.empty() || node -> edges.find(*it) == node -> edges.end()){
    	auto entry = new TrieNode{false,map<char,TrieNode*>(),nullptr};
      node -> edges[*it] = entry;
    }
    node = node -> edges[*it].second;
  }
  node -> isLeaf = true;
  node -> data = data;
  return true;
}

void* Trie::seek(const std::string & key) const{
	if (root -> edges.empty())return nullptr;
  TrieNode * node = root;
  for(auto it = key.cbegin();it != key.cend();it++){
  	 auto temp = node -> edges.find(*it);
     if (temp == node -> edges.end())return nullptr;
     node = temp -> second;
  }
  return node != nullptr ? node -> data : nullptr;
}
```

## OJ 问题

[Trie字典树题集](https://blog.csdn.net/weixin_41162823/article/details/101801789)

