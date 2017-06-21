---
layout: post
title:  SparseMatrix
date:   2017-06-21 13:14:00 +0800
categories: Homework
tag: DataStructure
---

# SparseMatrix

```c++
#include <iostream>
#include <algorithm>
#include <string.h>
#include <string>
#include <stdio.h>
#include <assert.h>
#include <stdlib.h>
using namespace std;
#define DefaultSize 100

template<class T>
struct Trituple {
	int row, col;
	T value;
	Trituple<T> &operator = (Trituple &x) {
		row = x.row; col = x.col; value = x.value;
		return *this;
	}
};

template<class T>
class SparseMatrix {
public:
	SparseMatrix(int maxSz = DefaultSize);
	SparseMatrix(SparseMatrix<T>&x);
	~SparseMatrix() { delete[]smArray; }
	SparseMatrix<T>& operator = (SparseMatrix<T> &x) {
		Rows = x.Rows; Cols = x.Cols; Terms = x.Terms;
		maxTerms = x.maxTerms;
		smArray = new Trituple<T>[maxTerms];
		if (NULL == smArray) {
			cerr << "There is something wrong with storing memories!" << endl; exit(1);
		}
		for (int i = 0; i < Terms; ++i)
			smArray[i] = x.smArray[i];
			return *this;	
		}
		SparseMatrix<T>Transpose();
		SparseMatrix<T>Add(SparseMatrix<T> &b);
		SparseMatrix<T>Multiply(SparseMatrix<T>&b);
private:
		int Rows, Cols, Terms;
		Trituple<T> *smArray;
		int maxTerms;
		friend ostream& operator << (ostream &out, SparseMatrix<T> & M) {
			out << "rows = " << M.Rows << endl;
			out << "cols = " << M.Cols << endl;
			out << "Nonzeor terms = " << M.Terms << endl;
			for (int i = 0; i < M.Terms; ++i)
				out << "M[" << M.smArray[i].row << "][" << M.smArray[i].col
				<< "]=" << M.smArray[i].value << endl;
			return out;
		}
		friend istream& operator >> (istream &in, SparseMatrix<T> & M) {
			cout << "Enter number of rows, columns and terms" << endl;
			in >> M.Rows >> M.Cols >> M.Terms;
			if (M.Terms > M.maxTerms) {
				cerr << "Number of terms overflow!" << endl;
				exit(1);
			}
			for (int i = 0; i < M.Terms; ++i) {
				cout << "Enter row, column and value of term:" << i + 1 << endl;
				in >> M.smArray[i].row >> M.smArray[i].col >> M.smArray[i].value;
			}
			return in;
		}
};

template<class T>
SparseMatrix<T>::SparseMatrix(int maxSz = DefaultSize) :maxTerms(maxSz) {
	if (maxSz < 1) {
		cerr << "The initialization of matrix is wrong!" << endl; exit(1);
	}
	smArray = new Trituple<T>[maxSz];
	if (NULL == smArray) {
		cerr <<  "There is something wrong with storing memories!" << endl; exit(1);
	}
	Rows = Cols = Terms = 0;
}

template<class T>
SparseMatrix<T>::SparseMatrix(SparseMatrix<T>&x) {
	Rows = x.Rows; Cols = x.Cols; Terms = x.Terms;
	maxTerms = x.maxTerms;
	smArray = new Trituple<T>[maxTerms];
	if (NULL == smArray) {
		cerr <<  "There is something wrong with storing memories!" << endl; exit(1);
	}
	for (int i = 0; i < Terms; ++i)
		smArray[i] = x.smArray[i];
	}

template<class T>
SparseMatrix<T> SparseMatrix<T>::Transpose() {
	SparseMatrix<T>b(maxTerms);
	b.Rows = Cols;
	b.Cols = Rows;
	b.Terms = Terms;
	if (Terms > 0) {
		int k, i, CurrentB = 0;
		for (k = 0; k < Cols; ++k) {
			for (i = 0; i < Terms; ++i) {
				if (smArray[i].col == k) {
					b.smArray[CurrentB].row = k;
					b.smArray[CurrentB].col = smArray[i].row;
					b.smArray[CurrentB].value = smArray[i].value;
					++CurrentB;
				}
			}
		}
	}
	return b;
}

template<class T>
SparseMatrix<T> SparseMatrix<T>::Add(SparseMatrix<T>&b) {
	SparseMatrix<T>Result(Rows, Cols);
	if (Rows != b.Rows || Cols != b.Cols) {
	cout << "Incopatible matrces" << endl;
		return Result;
	}
	int i = 0, j = 0, index_a, index_b;
	Result.Terms = 0;
	while (i < Terms && j < b.Terms) {
		index_a = Cols*smArray[i].row + smArray[i].col;
		index_b = Cols*b.smArray[i].row + b.smArray[i].col;
		if (index_a < index_b) {
			Result.smArray[Result.Terms] = smArray[i];
			++i;
		}
		else if (index_a > index_b) {
			Result.smArray[Result.Terms] = b.smArray[i];
			++j;
		}
		else {
			Result.smArray[Result.Terms] = smArray[i];
			Result.smArray[Result.Terms].value = smArray[i].value + b.smArray[i].value;
			++i; ++j;
		}
		++Result.Terms;
	}
	for (; i < Terms; ++i) {
		Result.smArray[Result.Terms] = smArray[i];
		++Result.Terms;
	}
	for (; j < b.Terms; ++j) {
		Result.smArray[Result.Terms] = b.smArray[i];
		++Result.Terms;
	}
	return Result;
}

template<class T>
SparseMatrix<T>SparseMatrix<T>::Multiply(SparseMatrix<T>&b) {
	SparseMatrix<T>Result(Rows, b.Cols);
	if (Cols != b.Rows) {
		cerr << "Incompatible matrices" << endl;
		return Result;
	}
	if (Terms == maxTerms || b.Terms == maxTerms) {
		cerr << "One additional space in a or b needed" << endl;
		return Result;
	}
	int *rowSize = new int[b.Rows];
	int *rowStart = new int[b.Rows + 1];
	T *tmp = new T[b.Cols];
	int i, Current, lastInResult, RowA, ColA, ColB;
	for (int i = 0; i < b.Rows; ++i) rowSize[i] = 0;
	for (int i = 0; i < b.Terms; ++i) rowSize[b.smArray[i].row]++;
	rowStart[0] = 0;
	for (int i = 1; i <= b.Rows; ++i){
		rowStart[i] = rowStart[i - 1] + rowSize[i - 1];
		Current = 0; lastInResult - 1;
		while (Current < Terms) {
			RowA = smArray[Current].row;
			for (i = 0; i < b.Cols; ++i) tmp[i] = 0;
			while (Current < Terms && smArray[Current].row == RowA) {
				ColA = smArray[Current].col;
				for (i = rowStart[ColA]; i < rowStart[ColA + 1]; ++i) {
					ColB = b.smArray[i].col;
					tmp[ColB] += smArray[Current].value = b.smArray[i].value;
				}
				++Current;
				for (i = 0; i < b.Cols; ++i) {
					if (tmp[i]) {
						++lastInResult;
						Result.smArray[lastInResult].row = RowA;
						Result.smArray[lastInResult].col = i;
						Result.smArray[lastInResult].value = tmp[i];
					}
				}
				Result.Rows = Rows; Result.Cols = b.Cols;
				Result.Terms = lastInResult + 1;
			}
		}
		delete[]rowSize; delete[]rowStart; delete[]tmp;
		return Result;
	}

void test() {
	SparseMatrix<int>t;
	cin >> t; cout << t << endl;
	t = t.Transpose(); 
	cout << "The matrix after tranposing is as follows:" << endl;
	cout << t << endl;
}

int main() {
	ios::sync_with_stdio(false);
	test();
	return 0;
}
```