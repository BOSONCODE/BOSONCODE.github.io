---
layout: post
title:  Sort
date:   2017-06-21 13:14:00 +0800
categories: Homework
tag: DataStructure
---

#sort

```C++
#include <fstream>
#include <queue>
#include <time.h>
#include <vector>
#include <stdlib.h>

template<class T>
class LinearList {
private:
	T a[200000];
	int sze;
public:
	LinearList() { sze = 0; }
	void setLinearList(int val) { sze = val; }
	void input(char *filename) {
		ifstream fin(filename);
		T n;
		while (fin >> n)
			a[sze++] = n;
		fin.close();
	}

	void FileInput(char *filename) {
		ifstream fin(filename);
		T n;
		while (fin >> n)
			a[sze++] = n;
		fin.close();
	}

	void StdinInput(int t[], int n) {
		for (int i = 0; i < n; ++i)
			a[i] = t[i];
		sze = n;
	}

	void StdoutOutput() {
		for (int i = 0; i < sze; ++i)
			cout << a[i] << ends;
		cout << endl;
	}

	void InsertSort(int &CmpTime, int &MoveTime) {
		for (int i = 0; i < sze; ++i) {
			int j = i;
			while (j && a[j - 1] > a[j]) {
				swap(a[j - 1], a[j]), --j; ++CmpTime; ++MoveTime;
			}
			++CmpTime;
		}
	}

	void MergeSort(int &CmpTime, int &MoveTime, int l = 0, int r = sze - 1) {
		if (l >= r) return;
		int m = l + r >> 1;
		MergeSort(CmpTime, MoveTime, l, m);
		MergeSort(CmpTime, MoveTime, m + 1, r);
		queue<T>q;
		int ll = l, rr = m + 1, tot = l;
		for (; ll <= m && rr <= r;) {
			++CmpTime;
			if (a[ll] > a[rr]) q.push(a[rr]), ++rr;
			else q.push(a[ll]), ++ll;
		}
		while (ll <= m) q.push(a[ll++]);
		while (rr <= r) q.push(a[rr++]);
		MoveTime += q.size();
		while (!q.empty()) a[tot++] = q.front(), q.pop();
	}

	void QuickSort(int &CmpTime, int &MoveTime, int left = 0, int right = sze - 1) {
		if (left >= right) return;
		int i = left, j = right;
		srand((unsigned)time(NULL));
		int pos = rand() % (right - left + 1) + left;
		T pivot = a[pos];
		swap(a[left], a[pos]);
		while (i < j) {
			++CmpTime;
			while (i < j && a[j] > pivot) --j, ++CmpTime;
			a[i] = a[j]; ++MoveTime;
			++CmpTime;
			while (i < j && a[i] <= pivot) ++i, ++CmpTime;
			a[j] = a[i]; ++MoveTime;
		}
		a[i] = pivot; ++MoveTime;
		QuickSort(CmpTime, MoveTime, left, i - 1);
		QuickSort(CmpTime, MoveTime, i + 1, right);
	}

	void STLHeapSort() {
		priority_queue<T, vector<T>, less<T> >q;
		for (int i = 0; i < sze; ++i) q.push(a[i]);
		for (int i = 0; i < sze; ++i) a[i] = q.top(), q.pop();
	}

	void HeapAdjust(int &CmpTime, int &MoveTime, int fa, int sizee) {
		int lchild = 2 * fa, rchild = 2 * fa + 1;
		int nxt = fa;
		if (fa <= sizee / 2) {
			if (lchild <= sizee && a[lchild] > a[nxt])
				nxt = lchild, ++CmpTime;
			if (rchild <= sizee && a[rchild] > a[nxt])
				nxt = rchild, ++CmpTime;
			if (nxt != fa) {
				swap(a[fa], a[nxt]); ++MoveTime;
				HeapAdjust(CmpTime, MoveTime, nxt, sizee);
			}
		}
	}

	void HeapSort(int &CmpTime, int &MoveTime) {
		for (int i = sze / 2; i >= 0; --i)
			HeapAdjust(CmpTime, MoveTime, i, sze);
		for (int i = sze; i >= 0; --i) {
			swap(a[i], a[0]); ++MoveTime;
			HeapAdjust(CmpTime, MoveTime, 0, i - 1);
		}
	}

	void SelectSort(int &CmpTime, int &MoveTime) {
		CmpTime = 0; MoveTime = 0;
		for (int i = 0; i < sze; ++i)
			for (int j = i + 1; j < sze; ++j) {
				if (a[i] > a[j]) {
					swap(a[i], a[j]), ++MoveTime, ++CmpTime;
				}
				++CmpTime;
			}
	}

	void BubbleSort(int &CmpTime, int &MoveTime) {
		for (int i = 0; i < sze; ++i) {
			for (int j = 0; j < sze - i - 1; ++j) {
				if (a[j] > a[j + 1]) {
					swap(a[j], a[j + 1]), ++MoveTime, ++CmpTime;
				}
				++CmpTime;
			}
		}
	}

	void ShellSort(int &CmpTime, int &MoveTime) {
		int i, j, gap;
		for (gap = sze / 2; gap > 0; gap /= 2) {
			for (i = 0; i < gap; i++) {
				for (j = i + gap; j < sze; j += gap) {
					if (a[j] < a[j - gap]) {
						++CmpTime;
						int temp = a[j];
						int k = j - gap;
						while (k >= 0 && a[k] > temp) {
							a[k + gap] = a[k];
							k -= gap;
							++CmpTime; ++MoveTime;
						}
						a[k + gap] = temp; ++MoveTime;
					}
					++CmpTime;
				}
			}
		}
	}
};

```


##test.cpp
```c++
#include <iostream>
#include <string>
#include <functional>
#include <stdio.h>
#include <algorithm>
#include "LinearList.h"
using namespace std;

int a[1000000] = { 15, 9, 8, 1, 4, 11, 7, 12, 13, 6, 5, 3, 16, 2, 10, 14 };
int c[1000000];

void GenerateDataFile(int n) {
	srand((unsigned)time(NULL));
	fstream fout("Sequence.txt", ios::out);
	for (int i = 0; i < n; ++i)  fout << i << endl;
	for (int i = n-1; i >= 0; --i)  fout << i << endl;
	for (int i = 0; i < n; ++i)  fout << rand() << endl; 
	fout.close();
}

void test(int b[], int n) {
	LinearList<int>test;
	for (int i = 0; i < n; ++i) c[i] = a[i] = b[i];
	test.StdinInput(a, n);
	clock_t start, end;
	int CmpTime = 0, MoveTime = 0;
	cout << "/*************************Test STL Sort: ************************/" << endl;
	start = clock();
	sort(c, c + n);
	end = clock();
	cout << end << ends << start << endl;
	cout << "Time consumption: " << (end - start) << endl;

	cout << "/*************************Test Insert Sort: ************************/" << endl;
	CmpTime = 0, MoveTime = 0;
	start = clock();
	test.InsertSort(CmpTime, MoveTime);
	end = clock();
	cout << end << ends << start << endl;
	cout << "Time consumption: " << (end-start) << endl;
	cout << "Move time: " << MoveTime << ends << "Cmpare time: " << CmpTime << endl;
//	cout << "The sort result: " << endl;  test.StdoutOutput();

	cout << "/*************************Test Merge Sort: ************************/" << endl;
	test.setLinearList(0);
	CmpTime = 0, MoveTime = 0;
	test.StdinInput(a, n);
	start = clock();
	test.MergeSort(CmpTime, MoveTime, 0, n - 1);
	end = clock();
	cout << end << ends << start << endl;
	cout << "Time consumption: " << end - start << endl;
	cout << "Move time: " << MoveTime << ends << "Cmpare time: " << CmpTime << endl;
	//cout << "The sort result: " << endl;  test.StdoutOutput();

	cout << "/*************************Test Quick Sort: ************************/" << endl;
	test.setLinearList(0);
	CmpTime = 0, MoveTime = 0;
	test.StdinInput(a, n);
	start = clock();
	test.QuickSort(CmpTime, MoveTime, 0, n - 1);
	end = clock();
	cout << end << ends << start << endl;
	cout << "Time consumption: " << end - start << endl;
	cout << "Move time: " << MoveTime << ends << "Cmpare time: " << CmpTime << endl;
	//cout << "The sort result: " << endl;  test.StdoutOutput();

	cout << "/*************************Test Heap Sort: ************************/" << endl;
	test.setLinearList(0);
	CmpTime = 0, MoveTime = 0;
	test.StdinInput(a, n);
	start = clock();
	test.HeapSort(CmpTime, MoveTime);
	end = clock();
	cout << end << ends << start << endl;
	cout << "Time consumption: " << end - start << endl;
	cout << "Move time: " << MoveTime << ends << "Cmpare time: " << CmpTime << endl;
	//cout << "The sort result: " << endl;  test.StdoutOutput();

	cout << "/*************************Test Bubble Sort: ************************/" << endl;
	test.setLinearList(0);
	CmpTime = 0, MoveTime = 0;
	test.StdinInput(a, n);
	start = clock();
	test.BubbleSort(CmpTime, MoveTime);
	end = clock();
	cout << end << ends << start << endl;
	cout << "Time consumption: " << end - start << endl;
	cout << "Move time: " << MoveTime << ends << "Cmpare time: " << CmpTime << endl;
	//cout << "The sort result: " << endl;  test.StdoutOutput();

	cout << "/*************************Test Select Sort: ************************/" << endl;
	test.setLinearList(0);
	CmpTime = 0, MoveTime = 0;
	test.StdinInput(a, n);
	start = clock();
	test.SelectSort(CmpTime, MoveTime);
	end = clock();
	cout << end << ends << start << endl;
	cout << "Time consumption: " << end - start << endl;
	cout << "Move time: " << MoveTime << ends << "Cmpare time: " << CmpTime << endl;
	//cout << "The sort result: " << endl;  test.StdoutOutput();

	cout << "/*************************Test Shell Sort: ************************/" << endl;
	test.setLinearList(0);
	CmpTime = 0, MoveTime = 0;
	test.StdinInput(a, n);
	start = clock();
	test.ShellSort(CmpTime, MoveTime);
	end = clock();
	cout << end << ends << start << endl;
	cout << "Time consumption: " << end - start << endl;
	cout << "Move time: " << MoveTime << ends << "Cmpare time: " << CmpTime << endl;
	//cout << "The sort result: " << endl;  test.StdoutOutput();
}


int main() {
	ios::sync_with_stdio(false);
	int n; cin >> n; GenerateDataFile(n);
	fstream fin("Sequence.txt", ios::in);
	cout << "/**********************************************Test Of Sequence:***********************************/ " << endl;
	for (int i = 0; i < n; ++i) fin >> a[i];
	test(a, n);
	cout << "/**********************************************Test Of Reverse Sequence***********************************/" << endl;
	for (int i = 0; i < n; ++i) fin >> a[i];
	test(a, n);
	cout << "/**********************************************Test Of Random Sequence***********************************/ " << endl;
	for (int i = 0; i < n; ++i) fin >> a[i];
	test(a, n);
	fin.close();
	return 0;
}
```