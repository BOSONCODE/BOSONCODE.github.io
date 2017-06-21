---
layout: post
title:  General Gradient Optimization
date:   2017-06-21 01:08:00 +0800
categories: ACM-ICPC-Template
tag: DP
---

题目链接： http://codeforces.com/contest/660/problem/F

这篇博客讲的挺详细的： （下面说的都是借用链接博客中的一些 Notations）

http://blog.csdn.net/kg20006/article/details/51333093

 

这题真的是让我对斜率优化理解又进一步了。 

之前的斜率优化我所知道都是像  http://www.cnblogs.com/ka200812/archive/2012/08/03/2621345.html

这篇博客里说的， 但是这一题 sum[i]不是单调的， 或者说这题是斜率优化的一个推广形式

 

Core: dp优化就是去掉没用的点， 之前我所理解的斜率优化是维护队首的，  那是因为sum[i] 是单调的，所以对于队列中的

元素有 (k < j < i) 有 gradient(j, k) < sum[j], gradient(i, j) < sum[i], 从而有i比j优，j比k优， 因为sum[i] > sum[j] 所以如果gradient(i, j) > sum[i]

那么显然 gradient(i, j) > sum[j] 不可能加入到队列中去， 所以队列中斜率是递增的， 所以队列中的都是前一个比后一个更优， 从而我们可以维护队首是最优值。

这都是很显然的但是都是建立在sum数组是单调的情况下。

而这一题就是sum数组不是单调的，所以我们就没有办法去维护队首最优。理由是我们可能会多删掉一些点

根据队列的单调性，我们可以得出这个函数的图像为单峰函数如(y = x^2)   我就拿y = x^2这个函数来举个栗子说明为啥会可能多删掉点

设k < j < i  那么斜率是这样的 gradient(i, j) > max{gradient(i, k), gradient(j, k)} 按照之前的做法，那么就可以去掉k点了， 但是后面

有可能会出现一个点m使得 gradient(m, k) > gradient(i, j) 但是由于k已经被删除了， 从而导致了结果的错误 所以这里不能够删除

能够删除的只有http://blog.csdn.net/kg20006/article/details/51333093这篇博客里说的那种情况 

对于单峰函数可以用三分去求解最大的极值函数y(x)的值  用二分可以求解对应的下标x = argmax(f(x)) 原理就是由于斜率队列是单调的根据费马引理f'(x) = 0 对应着极值

所以gradient最接近sum[r] 的 就是最大值的取值下标

```c++
#include <iostream>
#include <string.h>
#include <algorithm>
#include <string>
#include <stdio.h>
using namespace std;

const int maxn = 2e5 + 5;
int n, head, tail; 
long long a[maxn], sum[maxn], p[maxn];
int q[maxn];

long long y(long long x) {
    return p[x] - x*sum[x];
}

long long getdp(int i, int j){
    return p[i] - p[j] - j*(sum[i] - sum[j]);
}

double getgradient(int i, int j) {
    double dy = y(i) - y(j);
    double dx = i - j; 
    return dy / dx;
}

int solve(long long x) {
    int l = head, r = tail - 1; int res = l;
    while (l <= r) {
        int m = l + r >> 1;
        if (getgradient(q[m], q[m-1]) < -x) l = m + 1, res = m;
        else r = m - 1;
    }
    return q[res];
}

int main() {
    ios::sync_with_stdio(false);
    cin >> n;  sum[0] = p[0] = 0ll; 
    long long ans = 0ll;
    for (int i = 1; i <= n; ++i) 
        cin >> a[i], sum[i] = sum[i - 1] + a[i], p[i] = p[i - 1] + (long long)i*a[i];
    head = tail = 0; q[tail++] = 0;
    for (int i = 1; i <= n; ++i) {
        int j = solve(sum[i]);
        ans = max(ans, getdp(i, j));
        while (head < tail-1 && getgradient(i, q[tail - 1]) < getgradient(q[tail - 1], q[tail - 2])) --tail;
        q[tail++] = i;
    }
    cout << ans << endl;
    return 0;
}
```
