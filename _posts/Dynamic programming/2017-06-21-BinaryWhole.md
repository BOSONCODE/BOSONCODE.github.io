---
layout: post
title:  BinaryWhole
date:   2017-06-21 01:08:00 +0800
categories: ACM-ICPC-Template
tag: DP
---

```c++
const int maxn = 100005;
const int maxm = 100005;
const int INF = 1e9;
struct query {
	int l, r, k;
	int id, type;
	query() {}
	query(int a, int b, int c, int d, int e) :l(a), r(b), k(c), id(d), type(e) {}
}q[maxn + maxm], q1[maxn + maxm], q2[maxn + maxm];
int cnt = 0, n, m;
int c[maxn + maxm];

void init() {
	cnt = 1;
	memset(c, 0, sizeof c);
}

void add(int id, int x) {
	while (id <= n) {
		c[id] += x;
		id += id&(-id);
	}
}

int sum(int id) {
	int res = 0;
	while (id) {
		res += c[id];
		id -= id&(-id);
	}
	return res;
}

int ans[100005];

void cdq(int ql, int qr, int L, int R) {
	if (ql > qr) return;
	if (L == R) {
		for (int i = ql; i <= qr; ++i)
			if(q[i].type == 2) ans[q[i].id] = L;
		return;
	}
	int m = L + R >> 1;
	int ll = 0, rr = 0;
	for (int i = ql; i <= qr; ++i) {
		if (q[i].type == 1) {
			if (q[i].l <= m) {
				add(q[i].id, 1);
				q1[ll++] = q[i];
			}else q2[rr++] = q[i];
		}
		else {
			int res = sum(q[i].r) - sum(q[i].l -1);
			if (res < q[i].k) q[i].k -= res, q2[rr++] = q[i];
			else q1[ll++] = q[i];
		}
	}
	for (int i = 0; i < ll; ++i) if (q1[i].type == 1) add(q1[i].id, -1);
	for (int i = 0; i < ll; ++i) q[ql + i] = q1[i];
	for (int i = 0; i < rr; ++i) q[ql + ll + i] = q2[i];
	cdq(ql, ql + ll - 1, L, m);
	cdq(ql + ll, qr, m + 1, R);
}

void test() {
	while (scanf("%d%d", &n, &m) == 2) {
		init(); int x;
		for (int i = 0; i < n; ++i) {
			scanf("%d", &x);
			query tmp(x, 1, INF, i + 1, 1);
			q[cnt++] = tmp;
		}
		for (int i = 0; i < m; ++i) {
			int l, r, v;
			scanf("%d%d%d", &l, &r, &v);
			query tmp(l, r, v, i + 1, 2);
			q[cnt++] = tmp;
		}
		cdq(1, cnt - 1, -INF, INF);
		for (int i = 1; i <= m; ++i)
			printf("%d\n", ans[i]);
	}
}

```
