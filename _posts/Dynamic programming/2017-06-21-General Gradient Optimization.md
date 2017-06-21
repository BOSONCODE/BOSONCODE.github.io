<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
---
layout: post
title:  General Gradient Optimization
date:   2017-06-21 01:08:00 +0800
categories: ACM-ICPC-Template
tag: DP
---

```c++
��Ŀ���ӣ� http://codeforces.com/contest/660/problem/F

��ƪ���ͽ���ͦ��ϸ�ģ� ������˵�Ķ��ǽ������Ӳ����е�һЩ Notations��

http://blog.csdn.net/kg20006/article/details/51333093

 

������������Ҷ�б���Ż�����ֽ�һ���ˡ� 

֮ǰ��б���Ż�����֪��������  http://www.cnblogs.com/ka200812/archive/2012/08/03/2621345.html

��ƪ������˵�ģ� ������һ�� sum[i]���ǵ����ģ� ����˵������б���Ż���һ���ƹ���ʽ

 

Core: dp�Ż�����ȥ��û�õĵ㣬 ֮ǰ��������б���Ż���ά�����׵ģ�  ������Ϊsum[i] �ǵ����ģ����Զ��ڶ����е�

Ԫ���� (k < j < i) �� gradient(j, k) < sum[j], gradient(i, j) < sum[i], �Ӷ���i��j�ţ�j��k�ţ� ��Ϊsum[i] > sum[j] �������gradient(i, j) > sum[i]

��ô��Ȼ gradient(i, j) > sum[j] �����ܼ��뵽������ȥ�� ���Զ�����б���ǵ����ģ� ���Զ����еĶ���ǰһ���Ⱥ�һ�����ţ� �Ӷ����ǿ���ά������������ֵ��

�ⶼ�Ǻ���Ȼ�ĵ��Ƕ��ǽ�����sum�����ǵ���������¡�

����һ�����sum���鲻�ǵ����ģ��������Ǿ�û�а취ȥά���������š����������ǿ��ܻ��ɾ��һЩ��

���ݶ��еĵ����ԣ����ǿ��Եó����������ͼ��Ϊ���庯����(y = x^2)   �Ҿ���y = x^2����������ٸ�����˵��Ϊɶ����ܶ�ɾ����

��k < j < i  ��ôб���������� gradient(i, j) > max{gradient(i, k), gradient(j, k)} ����֮ǰ����������ô�Ϳ���ȥ��k���ˣ� ���Ǻ���

�п��ܻ����һ����mʹ�� gradient(m, k) > gradient(i, j) ��������k�Ѿ���ɾ���ˣ� �Ӷ������˽���Ĵ��� �������ﲻ�ܹ�ɾ��

�ܹ�ɾ����ֻ��http://blog.csdn.net/kg20006/article/details/51333093��ƪ������˵��������� 

���ڵ��庯������������ȥ������ļ�ֵ����y(x)��ֵ  �ö��ֿ�������Ӧ���±�x = argmax(f(x)) ԭ���������б�ʶ����ǵ����ĸ��ݷ�������f'(x) = 0 ��Ӧ�ż�ֵ

����gradient��ӽ�sum[r] �� �������ֵ��ȡֵ�±�

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