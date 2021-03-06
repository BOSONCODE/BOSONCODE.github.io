---
layout: post
title:  Suffix Automation
date:   2017-06-03 01:08:00 +0800
categories: ACM-ICPC-Template
tag: String
---

	#pragma comment(linker, "/STACK:10240000,10240000") 
	#include <iostream>
	#include <string.h>
	#include <string>
	#include <algorithm>
	#include <stdio.h>
	#include <set>
	#include <queue>
	#include <vector>
	#include <functional>
	#include <map>
	#include <math.h>
	using namespace std;

	/*template <class T>
	inline void scan_d(T &ret) {
		char c;
		ret = 0;
		while ((c = getchar()) < '0' || c > '9');
		while (c >= '0' && c <= '9') {
			ret = ret * 10 + (c - '0'), c = getchar();
		}
	}*/

	#define maxn 2000005

	namespace SuffixAutomation {
		struct sam {
			int fa, go[26];
			int len, r, lst;
			void init() {
				fa = len = r = lst = 0;
				memset(go, 0, sizeof go);
			}
		}node[maxn];
		int rt, last, tot;
		void init() {
			rt = last = tot = 1;
			node[0].init();
			node[1].init();
		}
		int newnode() {
			node[++tot].init();
			return tot;
		}
		void extend(int c) {
			int p = last, np = newnode();
			node[np].len = node[p].len + 1; node[np].r = 1;
			while (p && !node[p].go[c]) node[p].go[c] = np, p = node[p].fa;
			if (!p) node[np].fa = rt;
			else {
				int q = node[p].go[c];
				if (node[p].len + 1 == node[q].len) node[np].fa = q;
				else {
					int nq = newnode();
					node[nq].len = node[p].len + 1;
					memcpy(node[nq].go, node[q].go, sizeof(node[q].go));
					node[nq].fa = node[q].fa; node[np].fa = node[q].fa = nq;
					while (p && node[p].go[c] == q)
						node[p].go[c] = nq, p = node[p].fa;
				}
			}
			last = np;
		}

		void debug() {
			for (int i = 1; i <= tot; ++i) {
				printf("id=%d, fa=%d, step=%d, go=[ ", i, node[i].fa, node[i].len);
				for (int j = 0; j < 26; ++j) {
					if (node[i].go[j])
						printf("%c,%d ", j + 'a', node[i].go[j]);
				}
				puts("]");
			}
		}

		char buf[maxn], top = 0;

		void dfs(int u) {						
			if (top && node[u].lst) {cout << buf << endl;return;}
			for (int i = 0; i < 26; ++i) {
				if (node[u].go[i]) {
					buf[top++] = i + 'a';
					buf[top] = 0;
					dfs(node[u].go[i]);
					--top;
				}
			}
		}

		void print() {
			top = 0;
			for (int i = tot; i; node[i].lst = 1, i = node[i].fa);
			dfs(1);
		}
		string str, s;

		void lcs() {
			init();
			cin >> str; int len = str.length(); //cin >> query;
			for (int i = 0; i < len; ++i) extend(str[i] - 'a');
			cin >> str; len = str.length();
			int res = 0, cnt = 0, p = rt;
			for (int i = 0; i < len; ++i) {
				int id = str[i] - 'a';
				if (node[p].go[id]) ++cnt, p = node[p].go[id];
				else {
					while (p && !node[p].go[id]) p = node[p].fa;
					if (!p) p = rt, cnt = 0;
					else {
						cnt = node[p].len + 1;
						p = node[p].go[id];
					}
				}
				res = res < cnt ? cnt : res;
			}
			cout << res << endl;
		}
	}
	using namespace SuffixAutomation;

	int query;
	int vis[maxn], v[maxn], q[maxn];

	void topsort() {
		memset(v, 0, sizeof v);
		for (int i = 1; i <= tot; ++i) ++v[node[i].len];
		for (int i = 1; i <= tot; ++i) v[i] += v[i - 1];
		for (int i = tot; i; --i) q[v[node[i].len]--] = i;
		for (int i = tot; i; --i) node[node[q[i]].fa].r += node[q[i]].r;
	}

	int solve(const string &str, int k) {
		int len = str.length();
		int res = 0, cnt = 0, p = rt;
		for (int i = 0; i < len; ++i) {
			int id = str[i] - 'a';
			if (node[p].go[id]) ++cnt, p = node[p].go[id];
			else {
				while (p && !node[p].go[id]) p = node[p].fa;
				if (!p) p = rt, cnt = 0;
				else {
					cnt = node[p].len + 1;
					p = node[p].go[id];
				}
			}
		}
		while (node[p].fa && node[node[p].fa].len >= len) p = node[p].fa, cnt = node[p].len;
		if (cnt >= len && vis[p] != k) vis[p] = k, res += node[p].r;
		for (int i = 0; i < len - 1; ++i) {
			int id = str[i] - 'a';
			if (node[p].go[id]) p = node[p].go[id], ++cnt;
			else {
				while (p && !node[p].go[id]) p = node[p].fa;
				if (!p) p = rt, cnt = 0;
				else {
					cnt = node[p].len + 1;
					p = node[p].go[id];
				}
			}
			while (node[p].fa && node[node[p].fa].len >= len) p = node[p].fa, cnt = node[p].len;
			if (cnt >= len && vis[p] != k) vis[p] = k, res += node[p].r;
		}
		return res;
	}

	int main() {
		ios::sync_with_stdio(false);
		init();
		memset(vis, 0, sizeof vis);
		cin >> str; int len = str.length(); cin >> query;
		for (int i = 0; i < len; ++i) extend(str[i] - 'a');
		print();
		topsort();
		for (int i = 1; i <= query; ++i) {
			cin >> s; cout << solve(s, i) << endl;
		}
		return 0;
	}
