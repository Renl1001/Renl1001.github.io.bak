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

## C题
### 题目大意
[题目链接](https://vjudge.net/contest/238810#problem/C)

有$n$个人,$m$个提交记录，每个提交上面记录了哪个人什么时候过了一道题，输出每个事件后，第一个人的排名

排名先按照题数排序，相同的题数按照罚时排序，相同的罚时按照序号排序

### 分析
第1个人过题后，排名会上涨，其他人过题的时候，如果过题的那个人之前排名比第1个人低，过题后比第1个人高，那么排名会减一

维护一个优先队列（优先弹出排名低的）来存比第一个人排名高的人。然后如果第一个人过题了，就依次和队列里面的数比较，如果排名比队列里面的人高，就出队。如果其他人过题了，过题前排名比第一个人低，过题后比第一人个排名高，排名--

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
const int maxn = 1e5 + 5;
// head

struct pp
{
    int num;
    int second;
    int ind;
    bool operator <(const pp & a) const // pp a < pp b 表示a的排名比b的排名高（排名低的先出队）
    {
        if(num == a.num)
            return second < a.second;
        return num > a.num;
    }
};
pp p[maxn];

priority_queue<pp > pq;

int main() 
{
#ifndef ONLINE_JUDGE
    // freopen("in.txt", "r", stdin);
#endif

    int n, m;
    cin >> n >> m;
    int pm = 1;  //排名
    rep(i,1,n+1)
        p[i].ind = i;
    while(m--)
    {
        int t, c;
        scanf("%d%d", &t, &c);
        if(t == 1)
        {
            p[t].num++;
            p[t].second += c;
            while(!pq.empty())
            {
                pp top = pq.top();
                int ind = top.ind;
                if(top.num != p[ind].num) // 第ind个人过题了，队列里面没更新，需要更新
                {
                    pq.pop();
                    pq.push(p[ind]);
                    continue;
                }
                if(top < p[1])
                    break;
                else
                    pq.pop();
            }
            pm = pq.size()+1;
        }
        else
        {
            if(!(p[t] < p[1]))
            {
                p[t].num++;
                p[t].second += c;
                if(p[t] < p[1])
                {
                    pq.push(p[t]);
                    pm++;
                }
            }
            else
            {
                p[t].num++;
                p[t].second += c;
            }
        }
        printf("%d\n", pm);
    }
    return 0;
}

```

## D题
### 题目大意
[题目链接](https://vjudge.net/contest/238810#problem/D)

给一颗树，每次删除节点编号最小的叶子，然后记录删除的叶子的邻接节点。通过记录的节点来计算树的邻接表

### 分析
可以通过每条记录，得到每个节点的邻接节点个数，用d[i]保存，然后遍历每条记录，从d[i]==1中找到编号最小的节点j，删除该节点（d[i]-=1,d[j]-=1)，模拟一下就好了。

可以通过优先队列来记录d[i]等于1的节点（代码里面写的0），注意输入格式

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
const int maxn = 7500 + 5;
// head

VI vi;
int d[maxn];
priority_queue<int, vector<int>, greater<int> > pq;
std::vector<int> v[maxn];

int main() 
{
#ifndef ONLINE_JUDGE
    // freopen("in.txt", "r", stdin);
#endif

    int a;
    while(~scanf("%d", &a))
        vi.pb(a);
    int n = vi.size()+1;
    rep(i,0,vi.size())
        d[vi[i]]++;   
    rep(i, 1, n+1)    
        if(d[i] == 0)
            pq.push(i);
    rep(i,0,vi.size())
    {
        int top = pq.top();
        pq.pop();
        v[vi[i]].pb(top);
        v[top].pb(vi[i]);
        d[vi[i]]--;
        if(d[vi[i]] == 0)
            pq.push(vi[i]);
    }
    rep(i,1,n+1)
        sort(v[i].begin(), v[i].end());
    rep(i,1,n+1)
    {
        printf("%d:", i);
        rep(j,0,v[i].size())
            printf(" %d", v[i][j]);
        printf("\n");
    }
    return 0;
}

```

## G题
### 题目大意
[题目链接](https://vjudge.net/contest/238810#problem/G)

输入两个矩形，面积分别是s1, s2 输出 $(s1 \cap s2) / (s1 \cup s2)$

### 分析
签到题，算出重合的面积就行了，注意输出格式，没有面积输出“0.00”

### 代码

```clike
#include <iostream>
#include <cmath>
#include <cstdio>
using namespace std;
#define rep(i,a,n) for (int i=a;i<n;i++)
#define clr(a, x) memset(a, x, sizeof(a))
#define pb push_back
#define mp make_pair
#define INF 0x3f3f3f3f
// typedef vector<int> VI;
typedef long long ll;
// typedef pair<int,int> PII;
const ll MOD = 1e9+7;
const int maxn = 1e5 + 5;
// head

int main() 
{
#ifndef ONLINE_JUDGE
    // freopen("in.txt", "r", stdin);
#endif

    int T;
    scanf("%d", &T);
    while(T--)
    {
        ll x1,y1,w1,h1;
        ll x2,y2,w2,h2;
        cin >> x1 >> y1 >> w1 >> h1;
        cin >> x2 >> y2 >> w2 >> h2;
        ll dx, dy;
        if(x1 < x2)
            dx = min(x1+w1, x2+w2)-x2;
        else
            dx = min(x1+w1, x2+w2)-x1;
        if(y1 < y2)
            dy = min(y1+h1,y2+h2)-y2;
        else
            dy = min(y1+h1,y2+h2)-y1;
        if(dx <= 0 || dy <= 0)
        {
            printf("0.00\n");
            continue;
        }
        printf("%.2f\n", 1.0*(dy*dx)/(w1*h1+w2*h2-dy*dx));
    }
    return 0;
}

```

## H题
### 题目大意
[题目链接](https://vjudge.net/contest/238810#problem/H)

模拟6种操作，def,add,sub,mul,div,mod

### 分析
用map直接模拟就行了，注意mul的时候，直接乘会炸longlong，把乘法转化成多次相加就行了

### 代码

```clike
#include <string>
#include <map>
#include <iostream>
using namespace std;
#define rep(i,a,n) for (int i=a;i<n;i++)
#define clr(a, x) memset(a, x, sizeof(a))
#define pb push_back
#define mp make_pair
#define INF 0x3f3f3f3f
// typedef vector<int> VI;
typedef long long ll;
// typedef pair<int,int> PII;
const ll MOD = (1ll<<47);
const int maxn = 1e5 + 5;
// head

map<string, ll> mm;
string a,b;
ll c;
ll qmul(ll a, ll b)
{
    ll ans = 0;
    while(b)
    {
        if(b & 1)
            ans = (ans+a)%MOD;
        a = (a+a)%MOD;
        b >>= 1;
    }
    return ans;
}

int main() 
{
#ifndef ONLINE_JUDGE
    // freopen("in.txt", "r", stdin);
#endif

    while(cin >> a)
    {
        if(a == "def")
        {
            cin >> b >> c;
            mm[b] = c;
            cout << b << " = " << c << endl;
        }
        else if(a == "add")
        {
            cin >> a >> b;
            mm[a] = mm[a] + mm[b];
            mm[a] %= MOD;
            cout << a << " = " << mm[a] << endl;
        }
        else if(a == "sub")
        {
            cin >> a >> b;
            mm[a] = mm[a] - mm[b];
            if(mm[a] < 0)
                mm[a] += MOD;
            mm[a] %= MOD;
            cout << a << " = " << mm[a] << endl;
        }
        else if(a == "mul")
        {
            cin >> a >> b;
            mm[a] = qmul(mm[a],mm[b]);
            mm[a] %= MOD;
            cout << a << " = " << mm[a] << endl;
        }
        else if(a == "div")
        {
            cin >> a >> b;
            mm[a] = mm[a] / mm[b];
            cout << a << " = " << mm[a] << endl;
        }
        else if(a == "mod")
        {
            cin >> a >> b;
            mm[a] = mm[a] % mm[b];
            cout << a << " = " << mm[a] << endl;
        }
    }
    return 0;
}
```

## I题
### 题目大意
[题目链接](https://vjudge.net/contest/238810#problem/I)

有编号为1...n的n个人，你有m个礼物可以给m个人，n个人是有关联的，当你把礼物给编号为c的人的时候，那么编号小于等于c的人都会成为你的追随者。求追随者个数的期望

### 分析
m个礼物送给最大编号的人的编号是i的个数有：$C_{i-1}^{m-1}$,所以可以得到期望的公式
$$ ans &= \frac{\sum_{i=m}^{n}i*{C_{i-1}^{m-1}}}{C_n^m} $$

$$
\begin{align}
\sum_{i=m}^{n}i*{C_{i-1}^{m-1}} &= \sum_{i=m}^{n}i*{\frac{(i-1)!}{(m-1)!(i-m)!}} \\
&= \sum_{i=m}^{n}{\frac{i!}{(m-1)!(i-m)!}} \\
&= m*\sum_{i=m}^{n}{\frac{i!}{m!(i-m)!}} \\
&= m * \sum_{i=m}^{n}{C_i^m} \\
&= m * (C_m^m+C_{m+1}^m+C_{m+2}^m+...+C_n^m) \\
&= m * (C_{m+2}^{m+1}+C_{m+2}^m+...+C_n^m) \\
&= m * C_{n+1}^{m+1} \\
&= m * C_{n+1}^{m+1}
\end{align}
$$ 

$$
\begin{align}
ans &= \frac{m * C_{n+1}^{m+1}}{C_n^m} \\
&= \frac{m*(n+1)}{m+1}
\end{align}
$$ 


> 辣鸡Windows测评机

### 代码

```clike
#include <iostream>
using namespace std;
typedef long long ll;
const ll MOD = 1e9+7;
// head

ll qpow(ll a, ll b)
{
    ll ret = 1;
    while(b)
    {
        if(b & 1)
            ret = ret * a % MOD;
        a = a * a % MOD;
        b >>= 1;
    }
    return ret;
}

ll inv(ll a)
{
    return qpow(a,MOD-2);
}

int main() 
{
    ios::sync_with_stdio(false);
    int T;
    cin >> T;
    while(T--)
    {
        ll n, m;
        cin >> n >> m;
        cout << m*(n+1)%MOD*inv(m+1)%MOD << endl;
    }
    return 0;
}
```