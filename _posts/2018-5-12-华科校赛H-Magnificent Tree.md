---
layout: post
title: '[cdq分治]华科校赛H-Magnificent Tree'
date: 2018-05-08
author: Renl1001
tags: cdq分治
---

## 题意
[题目链接](https://www.nowcoder.com/acm/contest/106/H)

给定一个$w*w$的矩阵，初始全为0，然后进行n次操作，每次操作有两种情况：  
1. $(x,y)$坐标的位置上加上$val$
2. 查询$(x1,y1)$到$(x2,y2)$这个矩阵里面所有值的和

## 分析
比赛的时候没看$w$的范围，直接用二维树状数组做，然后那空间，编译都通不过。。。

赛后看了下正解是cdq分治，学习了一波。将点的修改表示成一个三元组(时间,x,y)，将查询分解成四个前缀和查询，也就是四个三元组，并且添加附加信息（该前缀和是-1还是+1）。这样就转化成了三维偏序问题，第一维时间默认已经排好序，分治过程中按照第二维$x$从小到大排序，然后用树状数组维护第三维$y$的信息。

## 代码

```clike
#include <bits/stdc++.h>
using namespace std;
#define LL long long
#define INF 0x3f3f3f3f
const LL MOD = 1e9+7;
const int MAXN = 4e5 + 5;

struct node
{
    int x, y, v, tp, id;
    node(){}
    node(int x, int y, int v, int tp, int id): x(x), y(y), v(v), tp(tp), id(id){}
    bool operator <(const node &b) const
    {
        return x < b.x;
    }
}a[MAXN];
int n, w;
int bit[MAXN];
int ans[MAXN];
int lowbit(int x)
{
    return x & (-x);
}

void add(int x, int val)
{
    while(x <= w)
    {
        bit[x] += val;
        x += lowbit(x);
    }
}

int sum(int x)
{
    int ans = 0;
    while(x > 0)
    {
        ans += bit[x];
        x -= lowbit(x);
    }
    return ans;
}

void cdq(int l, int r)
{
    if(l == r)
        return ;
    int m = (l+r)/2;
    cdq(l,m), cdq(m+1,r);
    sort(a+l, a+m+1);
    sort(a+m+1, a+r+1);
    int j = l;
    for(int i = m+1; i <= r; i++)
    {
        while(j <= m && a[j].x <= a[i].x)
        {
            if(a[j].tp==1) add(a[j].y, a[j].v);
            j++;
        }
        if(a[i].tp==2) ans[a[i].id]+=a[i].v*sum(a[i].y);
    }
    for(int i = l; i < j; i++)
        if(a[i].tp == 1) add(a[i].y, -a[i].v);
}

int main() 
{
#ifndef ONLINE_JUDGE
    //freopen("in.txt", "r", stdin);
#endif

    cin >> n >> w;
    int q = 0;
    int cnt = 0;
    while(n--)
    {
        int o;
        scanf("%d", &o);
        if(o == 1)
        {
            int x, y, h;
            scanf("%d%d%d", &x, &y, &h);
            a[++cnt] = node(x,y,h,1,0);
        }
        else
        {
            q++;
            int x1, y1, x2, y2;
            scanf("%d%d%d%d", &x1, &y1, &x2, &y2);
            a[++cnt] = node(x1-1,y1-1,1,2,q);
            a[++cnt] = node(x2,y2,1,2,q);
            a[++cnt] = node(x1-1,y2,-1,2,q);
            a[++cnt] = node(x2,y1-1,-1,2,q);
        }
    }
    cdq(1,cnt);
    for(int i = 1; i <= q; i++)
        printf("%d\n", ans[i]);
    return 0;
}
```
