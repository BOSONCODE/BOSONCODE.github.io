---
layout: post
title:  The Shortest Path viewing edge as node
date:   2017-06-21 13:14:00 +0800
categories: ACM-ICPC-Template
tag: Graph Theory
---

```c++
//http://acm.csu.edu.cn/csuoj/problemset/problem?pid=1808
//map the edges to nodes, then implement dijsktra
#include <stdio.h>
#include <algorithm>
#include <queue>
#include <functional>
#include <string.h>
using namespace std;

#define maxn 200005
struct edge {
	int v, nxt, w;
	int flag;
}e[maxn];
int head[maxn], tot = 0;
int n, m;
long long dist[maxn];
bool vis[maxn];

void addedge(int u, int v, int flag, int w) {
	e[tot].nxt = head[u];
	e[tot].v = v;
	e[tot].w = w;
	e[tot].flag = flag;
	head[u] = tot++;
}

struct node {
	int id;
	long long val;
	node(int _id, long long _val) :id(_id), val(_val) {}
	bool operator < (const node &rhs) const {
		return val > rhs.val;
	}
};

#define INF 1000000000000000000

long long dijsktra(int s) {
	long long ans = INF; 
	for (int i = 0; i <= tot; ++i) dist[i] = INF, vis[i] = false;
	priority_queue<node>q; 
	for (int i = head[s]; ~i; i = e[i].nxt) {
		dist[i] = e[i].w;
		q.push(node(i, dist[i]));
	}
	while (!q.empty()) {
		node x = q.top(); q.pop();
		int u = x.id; 
		if (vis[u]) continue;
		vis[u] = true;
		if (e[u].v == n) ans = min(ans, dist[u]);
		for (int i = head[e[u].v]; ~i; i = e[i].nxt) {
			if (!vis[i] && dist[i] > dist[u] + e[i].w + abs(e[i].flag - e[u].flag)) {
				dist[i] = dist[u] + e[i].w + abs(e[i].flag - e[u].flag);
				q.push(node(i, dist[i]));
			}
		}
	}
	return ans;
}

int main() {
	while (scanf("%d%d", &n, &m) != EOF) {
		memset(head, -1, sizeof head); tot = 0;
		while (m--) {
			int a, b, c, t;
			scanf("%d%d%d%d", &a, &b, &c, &t);
			addedge(a, b, c, t);
			addedge(b, a, c, t);
		}
		printf("%lld\n", dijsktra(1));
	}
	return 0;
}
```