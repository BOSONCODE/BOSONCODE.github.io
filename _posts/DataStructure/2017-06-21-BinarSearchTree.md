---
layout: post
title:  BinarySearchTree
date:   2017-06-21 13:14:00 +0800
categories: Homework
tag: DataStructure
---

#BST.h
```c++
#include <iostream>
using namespace std;

struct node {
	node *LeftChild, *RightChild;
	int value;
	node() {
		LeftChild = RightChild = NULL;
	}
};

class BST {
private:
	node *root;
public:
	BST() { root = NULL; }
	void CreateBBST(int a[], int n);
	node* Search(int key, int &CmpTime);
	void BuildTree(node *&root, int a[], int low, int high);
	void InOrder(node *root);
	void OutputData() { InOrder(root); }
};

#ifndef BOSON
#define BOSON
#include "BST.h"
#include <iostream>

void BST::CreateBBST(int a[], int n) {
	BuildTree(root, a, 0, n-1);

}

void BST::BuildTree(node *&root, int a[], int low, int high){
	if (low > high)	return ;
	root = new node;
	root->value = a[(low + high) / 2];
    BuildTree(root->LeftChild, a, low, (low + high) / 2 - 1);
	BuildTree(root->RightChild, a, (low + high) / 2 + 1, high);
}


node* BST::Search(int key, int &CmpTime) {
	node *rt = root;
	while (rt != NULL) {
		++CmpTime;
		if (rt->value > key) rt = rt->LeftChild;
		else if (rt->value < key) rt = rt->RightChild;
		else return rt;
	}
	return NULL;
}

void BST::InOrder(node *root) {
	if (root == NULL) { return; }
	InOrder(root->LeftChild);
	cout << root->value << endl;
	InOrder(root->RightChild);
}
#endif
```

#LinearList
```c++
class LinearList {
private:
	int element[250000];
	int sze;
public:
	LinearList() { sze = 0; }
	void InputData(int Inputa[], int n);
	int LinearSearch(int key, int &CmpTime);
	int BinarySearch(int key, int &CmpTime);
	void OutputData();
};

#ifndef BOSON
#define BOSON
#include "LinearList.h"
#include <iostream>

void LinearList::InputData(int Inputa[], int n) {
	for (int i = 0; i < n; ++i)
		element[i] = Inputa[sze++];
}

int LinearList::LinearSearch(int key, int &CmpTime) {
	for (int i = 0; i < sze; ++i) {
		++CmpTime;
		if (element[i] == key)
			return i;
	}
	return -1;
}

int LinearList::BinarySearch(int key, int &CmpTime) {
	int l = 0, r = sze-1;
	while (l <= r) {
		int mid = (l + r) >> 1;
		++CmpTime;
		if (element[mid] == key) return mid + 1;
		if (element[mid] > key) r = mid - 1;
		else l = mid + 1;
	}
	return -1;
}

void LinearList::OutputData() {
	for (int i = 0; i < sze; ++i)
		std::cout << element[i] << std::ends;
	std::cout << std::endl;
}

#endif
```


#test.cpp
```c++
#include <iostream>
#include <ctime>
#include <algorithm>
#include <cstdlib>
#include "LinearList.h"
#include "BST.h"
#include <cstring>
#include <string>

using namespace std;

#define MAXN 1000000
#define ST 1000000

int SearchTime = ST;

int a[MAXN], b[MAXN];

void Input(int n, bool IsRand){
	for (int i = 0; i < n; ++i) a[i] = 2 * i + 1, b[i] = 2 * (i+1);
	if(IsRand) random_shuffle(a, a + n);
	//cout << "a: ";
	//for (int i = 1; i <= n; ++i) cout << a[i] << ends; cout << endl;
}

void TestLinearSearch(int n, bool IsRand) {
	srand((unsigned)time(NULL));
	Input(n, IsRand);
	LinearList test;
	test.InputData(a, n);
	//int SearchTime; cin >> SearchTime;
	int key = 0, res, sum = SearchTime;
	int CmpTime = 0;
	while (SearchTime-- > 0) {
		key = rand() % (n); key = a[key];
		res = test.LinearSearch(key, CmpTime);
		//if (res == -1) cout << "There is no such element!QAQ~" << endl;
		//else cout << "The key lies in " << res << " position!@_@" << endl;
		key = 0;
	}
	cout << "The successful average compare time is " << CmpTime*1.0/sum << endl;
	
	SearchTime = sum;
	while (SearchTime-- > 0) {
		key = rand() % (n);   key = b[key];
		res = test.LinearSearch(key, CmpTime);
		//if (res == -1) cout << "There is no such element!QAQ~" << endl;
		//else cout << "The key lies in " << res << " position!@_@" << endl;
		key = 0;
	}
	cout << "The unsuccessful average compare time is " << CmpTime*1.0 / sum << endl;
}

void TestBinarySearch(int n){
	srand((unsigned)time(NULL));
	Input(n, false);
	LinearList test;
	test.InputData(a, n);
	//int SearchTime; cin >> SearchTime;
	int key = 0, res, sum = SearchTime;
	int CmpTime = 0;
	while(SearchTime-->0){
		key = rand() % (n); key = a[key];
		res = test.BinarySearch(key, CmpTime);
		//if(res == -1) cout << "There is no such element!QAQ~" << endl;
		//else cout << "The key lies in " << res << " position!@_@" << endl;
		key = 0;
	}
	cout << "The successful average compare time is " << CmpTime*1.0/sum << endl;

	SearchTime = sum;
	while (SearchTime-->0) {
		key = rand() % (n);   key = b[key];
		res = test.BinarySearch(key, CmpTime);
		//if (res == -1) cout << "There is no such element!QAQ~" << endl;
		//else cout << "The key lies in " << res << " position!@_@" << endl;
		key = 0;
	}
	cout << "The unsuccessful average compare time is " << CmpTime*1.0 / sum << endl;
}

void TestBST(int n){
	srand((unsigned)time(NULL));
	Input(n, false);
	BST test;
	test.CreateBBST(a, n);
	//int SearchTime; cin >> SearchTime;
	int key = 0, sum = SearchTime;
	node *res;
	int CmpTime = 0;
	while(SearchTime-->0){
		key = rand() % (n); key = a[key];
		res = test.Search(key, CmpTime);
		//if(res == NULL) cout << "There is no such element!QAQ~" << endl;
		//else cout << "The key lies in " << res << " position!@_@" << endl;
		key = 0;
	}
	cout << "The successful average compare time is " << CmpTime*1.0/sum << endl;

	SearchTime = sum;
	while (SearchTime-->0) {
		key = rand() % (n);   key = b[key];
		res = test.Search(key, CmpTime);
		//if (res == NULL) cout << "There is no such element!QAQ~" << endl;
		//else cout << "The key lies in " << res << " position!@_@" << endl;
		key = 0;
	}
	cout << "The unsuccessful average compare time is " << CmpTime*1.0 / sum << endl;
}

void test(){
	int n;
	clock_t start, end;
	
	cout << "TestLinearSearch: randdata" << endl;
	cin >> n; SearchTime = ST;
	start = clock(); 
	TestLinearSearch(n, true);
	end = clock();	
	cout << (end-start)*1.0/CLOCKS_PER_SEC << endl;

	cout << "TestLinearSearch:  " << endl;
	cin >> n; SearchTime = ST;
	start = clock();	
	TestLinearSearch(n, false);
	end = clock();		
	cout << (end-start)*1.0/CLOCKS_PER_SEC << endl;

	cout << "TestBinarySearch: " << endl;
	cin >> n; SearchTime = ST;
	start = clock();	
	TestBinarySearch(n);
	end = clock();		
	cout << (end-start)*1.0/CLOCKS_PER_SEC << endl;

	cout << "TestBST: " << endl;
	cin >> n; SearchTime = ST;
	start = clock();	
	TestBST(n);
	end = clock();		
	cout << (end - start)*1.0 / CLOCKS_PER_SEC << endl;
}

int main(){
	ios::sync_with_stdio(false);
	test();
	return 0;
}
```