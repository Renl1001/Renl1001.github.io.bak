---
layout: post
title: 'SICNU 2018 Summer Training #3 部分题解'
date: 2018-07-28
author: Renl1001
tags: 训练赛
---

## A题  不会

## B题
### 题目大意
[题目链接](https://vjudge.net/contest/238810#problem/B)

给定一个序列$a[1...n]$，从$[0,2^{60}]$中找出总共有多少个$s$，使得任意$a[i]$ ^ $s \leq a[i+1]$ ^ $s$

### 分析
对于每一对$a[i]$,$a[i+1]$，要使得$a[i]$^$s \leq a[i+1]$^$s$,那么需要$a[i]$和$a[i+1]$的二进制位从最高位开始比较，如果$a[i]$和$a[i+1]$的某一个二进制位不相等，那么要使得$a[i] < a[i+1]$，就需要将当前位固定，然后无论其他位是什么都能保证$a[i] < a[i+1]$（最高位不相同位前面的位都是相同的，和$0,1$异或后也会是相同的）。通过遍历所有的$a[i],a[i+1]$就能得到所有的固定的位置的个数$num$,其他不固定的位置就是0或者1都可以，所以答案就是$ans = 2 ^ {60-num}$

### 代码

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
const int maxn = 5e1 + 5;
// head

ll a[60];
bool bit[60];

void solve(ll x, ll y)
{
    int aa[60];
    int bb[60];
    rep(i,0,60)
        aa[i] = x%2, x /= 2;
    rep(i,0,60)
        bb[i] = y%2, y /= 2;
    for(int i = 59; i >= 0; i--)
    {
        if(aa[i] != bb[i])
        {
            bit[i] = 1;
            break;
        }
    }
}

int main() 
{
#ifndef ONLINE_JUDGE
    //freopen("in.txt", "r", stdin);
#endif

    int n;
    cin >> n;
    rep(i,0,n)
        cin >> a[i];
    rep(i,0,n-1)
        solve(a[i], a[i+1]);
    int sum = 0;
    rep(i,0,60)
        if(bit[i])
            sum++;
    cout << (1ll<<(60-sum)) << endl;
    return 0;
}
```
