---
layout: post
title: '小a与星际探索'
date: 2019-01-24
tags: 背包
---

##题目描述
小a正在玩一款星际探索游戏，小a需要驾驶着飞船从1号星球出发前往n号星球。其中每个星球有一个能量指数p。星球i能到达星球j当且仅当
pi>pj。同时小a的飞船还有一个耐久度t，初始时为1号点的能量指数，若小a前往星球j，那么飞船的耐久度会变为$t^pj$(即t异或pj，关于其定义请自行百度)小a想知道到达n号星球时耐久度最大为多少，注意：对于每个位置来说，从它出发可以到达的位置仅与两者的p有关，与下标无关.$1⩽n,∀pi⩽3000$
##思路
答案一定包括第一个星球和最后一个星球的p值的异或，重点则是对于中间星球的组合选择，因为出发和到达的位置只与p值有关，所以中间星球可以有任意的组合方式然后会有一个异或值，解题思路则是得到所有组合的异或值与第一个以及最后一个星球的异或值，最大的即为答案。这道题就转变成中间星球的组合异或问题，我们用背包的思路可以以O(n^2)得到所有组合的异或值。
##代码
```clike
#include <bits/stdc++.h>
using namespace std;
#define rep(i,a,n) for (int i=a;i<n;i++)
#define clr(a, x) memset(a, x, sizeof(a))
#define pb push_back
#define mp make_pair
#define INF 0x3f3f3f3f
typedef vector<int> VI;
typedef long long ll;
typedef pair<int,int> PII;
const ll MOD = 1e9+7;
const int maxn = 1e4 + 5;
// head

int p[maxn];
int vis[maxn];
int a[maxn];

int main() 
{
#ifndef ONLINE_JUDGE
    //freopen("in.txt", "r", stdin);
#endif

    int n;
    cin >> n;
    for(int i = 0; i < n; i++)
        scanf("%d", &p[i+1]);
    int cnt = 0;
    for(int i = 1; i <= n; i++)
    {
        if(p[i] > p[n] && p[i] < p[1])
            a[cnt++] = p[i];
    }
    vis[0] = 1;
    for(int i = 0; i < cnt; i++)
    {
        for(int j = 0; j < maxn; j++)
        {
            if(vis[j])
                vis[j^a[i]] = 1;
        }
    }
    int ans = 0;
    for(int i = 0; i < maxn; i++)
        if(vis[i])
            ans = max(ans, i ^ p[1] ^ p[n]);
    if(ans == 0)
        cout << "-1" << endl;
    else
        cout << ans << endl;
    return 0;
}
```