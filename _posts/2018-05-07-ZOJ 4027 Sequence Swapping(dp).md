---
layout: post
title: 'ZOJ 4027 Sequence Swapping(dp)'
date: 2018-05-07
author: Renl1001
tags: dp
---

## 题目大意
[题目链接](http://acm.zju.edu.cn/onlinejudge/showProblem.do?problemCode=4027)

给定一个由左右括号组成的字符串，每个位置都有一个权值v($-10^3 \leq v \leq 10^3$)。

然后对于这个字符串，如果第$k(1 \leq k < n)$个位置是'('并且第$k+1$个位置是')'，那么可以选择将这两个位置交换，交换后会得到这两个权值相乘的分数

可以进行无数次交换，问能得到的最大的权值是多少

## 分析
题目要求只能是相邻的左右括号才能交换，所以在所有的交换过程中，左括号之间的相对位置不会发生改变，右括号也是一样。
用$dp[i][j]$表示第i个左括号移动到第j个位置能获得的最大分数。
从右到左枚举左括号

第i个左括号移动到j的所能得到的分数=直接将i一步一步移动到j的位置得到的分数+i后面的左括号移动到j位置得到的分数

$$
dp[i][j] = a[i]*a[j]+dp[i][j-1];
dp[i][j] += dp[i-1][j];
$$

## 代码

```clike
#include <bits/stdc++.h>
using namespace std;
#define LL long long
#define INF 0x3f3f3f3f
const double eps = 1e-8;
const LL MOD = 1e9+7;
const int MAXN = 1e3 + 5;

char s[MAXN];
LL a[MAXN];
LL dp[MAXN][MAXN];

int main() 
{
#ifndef Accepted
    //freopen("in.txt", "r", stdin);
#endif

    int T;
    scanf("%d", &T);
    while(T--)
    {
        int n;
        scanf("%d%s", &n, s+1);
        for(int i = 1; i <= n; i++)
            scanf("%lld", &a[i]);
        int cnt = 0;
        memset(dp,0,sizeof(dp));
        LL ans = 0;
        for(int i = n; i >= 1; i--)
        {
            if(s[i] == '(')
            {
                cnt++;  // 当前左括号的个数
                for(int j = i+1; j <= n; j++)
                {
                    if(s[j] == '(')
                        dp[cnt][j] = dp[cnt][j-1];
                    else
                        dp[cnt][j] = a[i]*a[j]+dp[cnt][j-1];
                    ans = max(ans, dp[cnt][j]+dp[cnt-1][j]);
                }
                for(int j = i; j <= n; j++)
                    dp[cnt][j] += dp[cnt-1][j];
                for(int j = n; j > 1; j--)
                    dp[cnt][j-1] = max(dp[cnt][j-1],dp[cnt][j]);
            }
        }
        printf("%lld\n", ans);
    }
    return 0;
}
```
