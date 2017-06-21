---
layout: post
title:  BinaryTree
date:   2017-06-21 13:14:00 +0800
categories: Homework
tag: DataStructure
---

#BinTree.h
```c++
#ifndef BINTREE_H
#define BINTREE_H
#include <iostream>
#include <fstream>
#include <stdlib.h>
#include <stack>
#include <queue>
using namespace std;
template<class T>
struct BinTreeNode {
	T data;
	BinTreeNode<T> *leftChild, *rightChild;
	BinTreeNode(T x, BinTreeNode<T>*l = NULL, BinTreeNode<T> *r = NULL)
		:data(x), leftChild(l), rightChild(r) {}
};

template<class T>
class BinaryTree {
private:
	BinTreeNode<T> *root;
	T RefValue;
public:
	BinTreeNode<T> *getRoot() { return this->root; }
	BinaryTree() :root(NULL) {}												//(1)Construct BinaryTree
	BinaryTree(T value, T *arr, int len) : RefValue(value), root(NULL) {
		int index = 0;
		CreateBinTreeByArr(root, arr, len, index); 
	}
	bool IsEmpty() const { return (root == NULL); }							//(2)Judge if the BinaryTree is empty
																			//(3)PreOrder, InOrder, PostOrder
	void preOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p));
	void inOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p));
	void postOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p));
	void levelOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T> *p));	//(4)LevelTranverse
																				//(5)non-recursive PreOrder, InOrder, PostOrder
	void NoRecursivepreOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p));
	void NoRecursiveinOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p));
	void NoRecursivepostOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p));
	int CountLeaf(BinTreeNode<T> *subTree);										//(6)calculate the node number and the leaf number
	int CountNode(BinTreeNode<T> *subTree);
	void destroy(BinTreeNode<T>* subTree);										//(7)destroy the BinaryTreery
	int Height(BinTreeNode<T>*subTree, int depth = 0);							//(8)calculate the height
	bool Insert(BinTreeNode<T> *subTree, const T &x);							//(9)insert a node
	BinaryTree(const BinaryTree<T>*rhs) {										//(10)copy construction function
		this->root = copy(rhs);
	}																	
	BinaryTree<T> &operator = (const BinaryTree<T>& rhs);						//(11) override assignment
	~BinaryTree() { destroy(root); }											//(12)destructor
	BinTreeNode<T> *createBinaryTree(T *VLR, T *LVR, int n);					//(13)create BinaryTree from two Traverse Sequence(pre and in)
	BinTreeNode<T> *copy(const BinTreeNode<T> *orignode);
	void printNodeLevel(BinTreeNode<T> *subTree, int depth = 0);				//(14)output the level of all the nodes
	void destroyLeaf(BinTreeNode<T> *subTree);									//(15)delete all the leaf of BinaryTree
	int getNodeLevel(BinTreeNode<T> *subTree, int target, int depth = 0);		//(16)output the level of specific node
	void CreateBinTreeByArr(BinTreeNode<T> *&subTree, T arr[], int len, int &index);				//construct a full tree
	void CreateBinTree(istream &in, BinTreeNode<T> *&subTree);
	void Traverse(BinTreeNode<T> *subTree, ostream &out);
	friend istream &operator >> (istream &in, BinaryTree<T> &Tree) {
		cout << "input BinaryTree: " << endl;
		Tree.CreateBinTree(in, Tree.root);
		return in;
	}
	friend ostream &operator << (ostream &out, BinaryTree<T>&Tree) {
		out << "The preOrder is:" << endl;
		Tree.Traverse(Tree.getRoot(), out);
		return out;
	}
	void preOrder1(void(*visit)(BinTreeNode<T>*p)) { preOrder(root, visit); }
	void inOrder1(void(*visit)(BinTreeNode<T>*p)) { inOrder(root, visit); }
	void postOrder1(void(*visit)(BinTreeNode<T>*p)) { postOrder(root, visit); }
	void levelOrder1(void(*visit)(BinTreeNode<T> *p)) { levelOrder(root, visit); }
	void preOrder2(void(*visit)(BinTreeNode<T>*p)) { NoRecursivepreOrder(root, visit); }
	void inOrder2(void(*visit)(BinTreeNode<T>*p)) { NoRecursiveinOrder(root, visit); }
	void postOrder2(void(*visit)(BinTreeNode<T>*p)) { NoRecursivepostOrder(root, visit); }
};

template<class T>
void BinaryTree<T>::CreateBinTree(istream &in, BinTreeNode<T> *&subTree) {
	if (!in.eof()) {
		T x; in >> x;
		if (x == RefValue) subTree = nullptr;
		else {
			subTree = new BinTreeNode<T>(x);
			if (!subTree) {
				cerr << "There is something wrong with stroring memories!" << endl;
				exit(1);
			}
			CreateBinTree(in, subTree->leftChild);
			CreateBinTree(in, subTree->rightChild);
		}
	}
}

template<class T>
void BinaryTree<T>::CreateBinTreeByArr(BinTreeNode<T> *&subTree, T arr[], int len, int &index) {
	if (index >= len) return;
	T tmp = arr[index++];
	if (tmp == RefValue) subTree = nullptr;
	else {
		subTree = new BinTreeNode<T>(tmp);
		if (!subTree) {
			cerr <<  "There is something wrong with stroring memories!" << endl;
			exit(1);
		}
		CreateBinTreeByArr(subTree->leftChild, arr, len, index);
		CreateBinTreeByArr(subTree->rightChild, arr, len, index);
	}
}

template<class T>
bool BinaryTree<T>::Insert(BinTreeNode<T> *subTree, const T &x) {
	if (subTree->leftChild == NULL) {
		BinTreeNode<T> tmp = new BinTreeNode<T>(x);
		if (tmp == NULL) return false;
		subTree->leftChild = tmp;
		delete tmp;
		return true;
	}
	if (subTree->rightChild == NULL) {
		BinTreeNode<T> tmp = new BinTreeNode<T>(x);
		if (tmp == NULL) return false;
		subTree->rightChild = tmp;
		delete tmp;
		return true;
	}
	Insert(subTree->leftChild, x);
	Insert(subTree->rightChild, x);
}

template<class T>
struct stkNode {
	BinTreeNode<T> *ptr;
	int tag;
	stkNode(BinTreeNode<T> *N = NULL) : ptr(N), tag(0) {}
};

template<class T>
void BinaryTree<T>::preOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p)) {
	if (subTree) {
		visit(subTree);
		preOrder(subTree->leftChild, visit);
		preOrder(subTree->rightChild, visit);
	}
}

template<class T>
void BinaryTree<T>::inOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p)) {
	if (subTree) {
		inOrder(subTree->leftChild, visit);
		visit(subTree);
		inOrder(subTree->rightChild, visit);
	}
}

template<class T>
void BinaryTree<T>::postOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p)) {
	if (subTree) {
		postOrder(subTree->leftChild, visit);
		postOrder(subTree->rightChild, visit);
		visit(subTree);
	}
}

template<class T>
void BinaryTree<T>::levelOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T> *p)) {
	queue<BinTreeNode<T>* >q;
	BinTreeNode<T>*p = root;
	q.push(p);
	while (!q.empty()) {
		p = q.front(); q.pop(); visit(p);
		if (p->leftChild) q.push(p->leftChild);
		if (p->rightChild) q.push(p->rightChild);
	}
}

template<class T>
void BinaryTree<T>::NoRecursivepreOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p)) {
	stack<BinTreeNode<T>* >s;
	BinTreeNode<T> *p = root;
	s.push(NULL);
	while (p != NULL) {
		visit(p);
		if (p->rightChild != NULL) s.push(p->rightChild);
		if (p->leftChild != NULL) p = p->leftChild;
		else p = s.top(), s.pop();
	}
}

template<class T>
void BinaryTree<T>::NoRecursiveinOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p)) {
	stack<BinTreeNode<T>* >s;
	BinTreeNode<T> *p = root;
	do {
		while (p) {
			s.push(p);
			p = p->leftChild;
		}
		if (!s.empty()) {
			p = s.top(); s.pop();
			visit(p);
			p = p->rightChild;
		}
	} while (p || !s.empty());
}

template<class T>
void BinaryTree<T>::NoRecursivepostOrder(BinTreeNode<T> *subTree, void(*visit)(BinTreeNode<T>*p)) {
	stack<stkNode<T> >s; stkNode<T>w;
	BinTreeNode<T> *p = root;
	do {
		while (p) {
			w.ptr = p; w.tag = 0; s.push(w);
			p = p->leftChild;
		}
		int continuel = 1;
		while (continuel && !s.empty()) {
			w = s.top(); s.pop();
			p = w.ptr; 
			switch (w.tag) {
			case 0: w.tag = 1; s.push(w);
				continuel = 0;
				p = p->rightChild;
				break;
			case 1:
				visit(p);
				break;
			}
		}
	} while (!s.empty());
	cout << endl;
}

template<class T>
void BinaryTree<T>::Traverse(BinTreeNode<T> *subTree, ostream &out) {
	if (subTree) {
		out << subTree->data << " ";
		Traverse(subTree->leftChild, out);
		Traverse(subTree->rightChild, out);
	}
}

template<class T>
void BinaryTree<T>::destroy(BinTreeNode<T>* subTree) {
	if (subTree) {
		destroy(subTree->leftChild);
		destroy(subTree->rightChild);
		delete subTree;
	}
}

template<class T>
int BinaryTree<T>::getNodeLevel(BinTreeNode<T> *subTree, int target, int depth = 0) {
	if (subTree->data == target) {
		return depth + 1;
	}
	if (subTree->leftChild)
		return getNodeLevel(subTree->leftChild, target, depth + 1);
	if (subTree->rightChild)
		return getNodeLevel(subTree->rightChild, target, depth + 1);
	return -1;
}

template<class T>
int BinaryTree<T>::CountLeaf(BinTreeNode<T> *subTree) {
	if (subTree == NULL) return 0;
	if (subTree->leftChild == NULL && subTree->rightChild == NULL)
		return 1;
	return CountLeaf(subTree->leftChild) + CountLeaf(subTree->rightChild);
}

template<class T>
int BinaryTree<T>::CountNode(BinTreeNode<T> *subTree) {
	if (subTree == NULL) return 0;
	return CountNode(subTree->leftChild) + CountNode(subTree->rightChild) + 1;
}

template<class T>
void BinaryTree<T>::destroyLeaf(BinTreeNode<T> *subTree) {
	if (subTree == NULL) return;
	if (subTree->leftChild == NULL && subTree->rightChild == NULL) {
		delete subTree;
		return;
	}
	destroyLeaf(subTree->leftChild);
	destroyLeaf(subTree->rightChild);
}

template<class T>
int BinaryTree<T>::Height(BinTreeNode<T> *subTree, int depth = 0) {
	if (subTree == NULL) return depth;
	return max(Height(subTree->leftChild, depth + 1), Height(subTree->rightChild, depth+1));
}

template<class T>
void BinaryTree<T>::printNodeLevel(BinTreeNode<T> *subTree, int depth = 0) {
	if (subTree) {
		cout << depth + 1 << endl;
		printNodeLevel(subTree->leftChild, depth + 1);
		printNodeLevel(subTree->rightChild, depth + 1);
	}
}

template<class T>
BinTreeNode<T>* BinaryTree<T>::createBinaryTree(T *VLR, T *LVR, int n) {
	if (!n) return 0; int k = 0;
	while (VLR[0] != LVR[k]) ++k;
	BinTreeNode<T> *t = new BinTreeNode<T>(VLR[0]);
	t->leftChild = createBinaryTree(VLR + 1, LVR, k);
	t->rightChild = createBinaryTree(VLR + k + 1, LVR + 1 + k, n - k - 1);
	return t;
}

template<class T>
BinTreeNode<T>* BinaryTree<T>::copy(const BinTreeNode<T> *orignode) {
	if (orignode == NULL) return NULL;
	BinTreeNode<T> *temp = new BinTreeNode<T>(orignode->data);
	temp->leftChild = copy(orignode->leftChild);
	temp->rightChild = copy(orignode->rightChild);
	return temp;
}

template<class T>
BinaryTree<T>& BinaryTree<T>::operator=(const BinaryTree<T>& rhs) {
	this->destroy(this->root);
	this->root = copy(rhs.root);
	return *this;
}

#endif
```

#test.cpp
```c++
#include "BinTree.h"

void visit(BinTreeNode<int> *subTree) {
	cout << subTree->data << ends;
}
void visitchar(BinTreeNode<char> *subTree) {
	cout << subTree->data << ends;
}


void test() {
	int arr[7] = {1, 2, 3, 4, 5, 6, -1};
	int end = -1;
	char VLR[] = { 'A', 'B', 'C', 'D', 'E', 'F' };
	char LVR[] = { 'B', 'C', 'A', 'D', 'F', 'E' };
	BinaryTree<char>str;
	BinTreeNode<char> *res = str.createBinaryTree(VLR, LVR, 6);
	str.preOrder(res, visitchar); cout << endl;
	str.inOrder(res, visitchar); cout << endl;
	str.postOrder(res, visitchar); cout << endl;
	BinaryTree<int> t(-1, arr, 7);
	BinaryTree<int> s;	s = t;
	BinaryTree<int>c;
	cin >> c; cout << c << endl;
	s.preOrder1(visit); cout << endl;
	cout << "recursive preOrder:" << endl;
	t.preOrder1(visit); cout << endl;
	cout << "recursive inOrder:" << endl;
	t.inOrder1(visit); cout << endl;
	cout << "recursive postOrder:" << endl;
	t.postOrder1(visit); cout << endl;
	cout << "no-recursive level tranverse: " << endl;
	t.levelOrder1(visit); cout << endl;
	cout << "no-recursive preOrder:"" << endl;
	t.preOrder2(visit); cout << endl;
	cout << "no-recursive inOrder:"" << endl;
	t.inOrder2(visit); cout << endl;
	cout << "no-recursive postOrder:"" << endl;
	t.postOrder2(visit); cout << endl;
	cout << "The height of the tree: " << endl;
	cout << t.Height(t.getRoot()) << endl;
	cout << "The number of the leaf: " << endl;
	cout << t.CountLeaf(t.getRoot()) << endl;
	cout << "The number of the node: " << endl;
	cout << t.CountNode(t.getRoot()) << endl;
	cout << "Output the level of the specific node " << endl;
	t.printNodeLevel(t.getRoot());
	cout << t << endl;
}

int main() {
	test();
	return 0;
}
```