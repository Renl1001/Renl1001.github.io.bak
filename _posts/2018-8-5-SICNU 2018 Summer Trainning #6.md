---
layout: post
title: 'SICNU 2018 Summer Training #6 部分题解'
date: 2018-08-05
author: Renl1001
tags: 训练赛
---

## A题
### 题目大意
[题目链接](https://cn.vjudge.net/contest/243410#problem/A)

给定$n$个括号串，问能否将括号串按照一定顺序排列，使得得到的串是一个合法的序列，如果是合法的，还需要按顺序打印括号串的编号

### 分析
首先对于每个括号串，利用栈将已经能匹配的括号删掉，然后所有括号串都能得到类似“))))((((((”的串，这时候就可以将左右括号数存起来。然后贪心排序，具体贪心见代码。

### 代码

```clike
#include <bits/stdc++.h>
using namespace std;
const int maxn = 3e5 + 5;
// head

struct kh
{
    int l, r, ind;
    bool operator < (const kh& b) const
    {
        if(l <= r && b.l > b.r) return 0;  // 右括号比左括号多的放前面
        else if(l > r && b.l <= b.r) return 1;  
        if(r >= l && b.r >= b.l) return l > b.l;  // 同样是右括号比左括号多的时候右括号少的放前面
        return r < b.r;
    }
};

kh a[maxn];

int main() 
{
    ios::sync_with_stdio(false);
    int n;
    cin >> n;
    for(int i = 1; i <= n; i++)
    {
        string s;
        cin >> s;
        stack<char> st;
        for(int j = 0; j < s.size(); j++)
        {
            if(!st.empty() && st.top() == '(' && s[j] == ')') 
                st.pop();    // 匹配
            else st.push(s[j]);
        }
        int l = 0, r = 0;
        while(!st.empty())
        {
            char c = st.top();
            st.pop();
            if(c == '(') a[i].l++;
            else a[i].r++;
        }
        a[i].ind = i;
    }
    sort(a+1, a+n+1);
    int l = 0, r = a[1].r;
    int flag = 0;
    for(int i = 1; i <= n; i++)
    {
        if(a[i].r > l)
        {
            flag = 1;
            break;
        }
        l = l - a[i].r + a[i].l;
    }    
    if(l != 0) flag = 1;
    if(flag) cout << "NO" << endl;
    else
    {
        cout << "YES" << endl;
        for(int i = 1; i <= n; i++) 
            cout << a[i].ind << " \n"[i==n];
    }
    return 0;
}
```
## B题
### 题目大意
[题目链接](https://cn.vjudge.net/contest/243410#problem/B)

给定一个字符串$s$，问能否交换两个字母，使得$s$中不含有$happiness$

### 分析
水题，$happiness$个数小于$3$个的时候，都可以通过交换使得没有$happiness$

注意$happiness$为0的时候，要判断下，交换后会不会产生$happiness$

### 代码

```clike
#include <bits/stdc++.h>
using namespace std;
// head

int main() 
{
    string sub = "happiness";
    string s;
    cin >> s;
    int ind = s.find(sub);
    if(ind == -1)  // 0
    {
        printf("YES\n");
        string ss = "ahppiness";
        string sss = "appiness";
        if(s.find(ss) == 0)
            printf("2 3\n");
        else if(s[0] == 'h' && s.find(sss) == 2)
            printf("2 3\n");
        else
            printf("1 2\n");
    }
    else // 1
    {
        int a1 = ind;
        int a = ind+4, b = ind+7;
        ind = s.find(sub, ind+1);
        if(ind == -1)  // 1
        {
            printf("YES\n");
            printf("%d %d\n", a, b);
        }
        else // 2
        {
            int a2 = ind;
            ind = s.find(sub, ind+1);
            if(ind == -1) // 2
            {
                printf("YES\n");
                printf("%d %d\n", a1+1, a2+2);
            }
            else  // 3
                printf("NO\n");
        }
    }
    return 0;
}
```

## C题
### 题目大意
[题目链接](https://cn.vjudge.net/contest/243410#problem/C)

有$a$个红球$b$个绿球和$c$个不知道什么颜色的球，问最多取出多少个球，可以保证红球不超过$n$个，绿球不超过$m$个

### 分析
如果$a+c>n$则最多只能拿$n$个球，如果$b+c>m$则最多只能拿$m$个球，否则就全拿，找最小的值就行了

### 代码

```clike
#include <bits/stdc++.h>
using namespace std;
// head

int main()
{
    long long a, b, c, n, m;
    cin >> a >> b >> c;
    cin >> n >> m;
    long long ans = a + b + c;
    if(a + c > n) ans = min(ans, n);
    if(b + c > m) ans = min(ans, m);
    cout << ans << endl;
    return 0;
}
```

## D题
### 题目大意
[题目链接](https://cn.vjudge.net/contest/243410#problem/D)

给定每次能移动的距离，问能否到达某个点

### 分析
求到所有数据的$gcd$，就得到了最小的移动的间隔，然后判断$n$是否能整除$gcd$就行了

### 代码

```clike
#include <bits/stdc++.h>
using namespace std;
const int maxn = 2e5 + 5;
// head

int a[maxn];

int main() 
{
    int n, x;
    cin >> n >> x;
    int gcd;
    for(int i = 0; i < n; i++)
    {
        scanf("%d", &a[i]);
        if(i == 0) gcd = a[i];
        else gcd = __gcd(gcd,a[i]);
    }
    if(x < 0) x = -x;
    if(x%gcd == 0) printf("YES\n");
    else printf("NO\n");
    return 0;
}
```

## E题
### 题目大意
[题目链接](https://cn.vjudge.net/contest/243410#problem/E)

有$n$个传送阵，$m$个奖励点，传送阵之间移动不消耗时间，问拿到所有的奖励点，需要多少时间

### 分析
传送阵端点两侧的奖励点只能直接走过去，每两个传送阵之间的奖励点可以通过从一个移动到另外一个，也可以拿一部分，然后回去通过传送阵到另外一边再拿剩下的，只需要找到奖励点之间的最大间隔，就能计算时间，然后和直接移动比较，选择短的时间

### 代码

```clike
#include <bits/stdc++.h>
using namespace std;
#define rep(i,a,n) for (int i=a;i<n;i++)
typedef long long ll;
const int maxn = 2e5 + 5;

ll a[maxn], b[maxn], ans;

int main() 
{
    int n, m;
    cin >> n >> m;
    rep(i, 0, n) scanf("%lld", &a[i]);
    rep(i, 0, m) scanf("%lld", &b[i]);

    int ind = 0;
    if(b[0] < a[0]) ans = 2*(a[0]-b[0]);
    while(ind < m && b[ind] <= a[0]) ind++;
    rep(i, 0, n-1)
    {
        ll l = a[i], sub = 0;
        while(ind < m && b[ind] < a[i+1])
        {
            sub = max(sub, b[ind] - l);
            l = b[ind];
            ind++;
        }
        while(ind < m && b[ind] <= a[i+1]) ind++;
        if(l == a[i]) continue;
        sub = max(sub, a[i+1]-l);
        ans += min(2*(a[i+1]-a[i])-2*sub, a[i+1]-a[i]);
    }
    if(ind < m) ans += 2*(b[m-1]-a[n-1]);
    printf("%lld\n", ans);    
    return 0;
}
```
## F题
### 题目大意
[题目链接](https://cn.vjudge.net/contest/243410#problem/F)

$m$次操作，每次将$a_j$的名字变成$I$_$love$_$b_j$,问操作结束后，第一个人的名字叫什么

### 分析
反向查找，找到第一个人经历了几次变化，以及最后一次变化成的名字

### 代码

```clike
#include <bits/stdc++.h>
using namespace std;
#define rep(i,a,n) for (int i=a;i<n;i++)
const int maxn = 2e5 + 5;
// head

string name[maxn];
int u[maxn], v[maxn];

int main() 
{
    ios::sync_with_stdio(false);
    int n, m, sum = 0, rt = 1;
    cin >> n;
    rep(i,1,n+1)
        cin >> name[i];
    cin >> m;
    rep(i, 1, m+1)
        cin >> u[i] >> v[i];
    for(int i = m; i >= 0; i--)
    {
        if(u[i] == rt)
        {
            sum++;
            rt = v[i];
        }
    }
    rep(i, 0, sum)
        cout << "I_love_";
    cout << name[rt] << endl;
    return 0;
}
```
## G题
### 题目大意
[题目链接](https://cn.vjudge.net/contest/243410#problem/G)

给定一个矩阵，可以删掉某个点的横纵坐标上面的所有数字，问删掉哪个点，能让剩下的点最大值最小

### 分析
通过前缀和得到每个点的左上、右上、左下、右下的最大值，然后遍历每个点，找到最小的最大值的点

### 代码

```clike
#include <bits/stdc++.h>
using namespace std;
#define INF 0x3f3f3f3f
const int maxn = 1e3 + 5;
// head

int a[maxn][maxn];
int zs[maxn][maxn], ys[maxn][maxn];
int zx[maxn][maxn], yx[maxn][maxn];

int main() 
{
    int n, m;
    cin >> n >> m;
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= m; j++)
            scanf("%d", &a[i][j]);

    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= m; j++)
            zs[i][j] = max(a[i-1][j-1], max(zs[i][j-1], zs[i-1][j]));
    for(int i = 1; i <= n; i++)
        for(int j = m; j >= 1; j--)
            ys[i][j] = max(a[i-1][j+1], max(ys[i][j+1], ys[i-1][j]));
    for(int i = n; i >= 1; i--)
        for(int j = 1; j <= m; j++)
            zx[i][j] = max(a[i+1][j-1], max(zx[i][j-1], zx[i+1][j]));
    for(int i = n; i >= 1; i--)
        for(int j = m; j >= 1; j--)
            yx[i][j] = max(a[i+1][j+1], max(yx[i][j+1], yx[i+1][j]));
        
    int minn = INF, r, c;
    for(int i = 1; i <= n; i++)
        for(int j = 1; j <= m; j++)
        {
            int maxx = max(zs[i][j], zx[i][j]);
            maxx = max(maxx,max(ys[i][j], yx[i][j]));
            if(maxx < minn) minn = maxx, r = i, c = j;
        }
    cout << r << " " << c << endl;
    return 0;
}
```

## H题
### 题目大意
[题目链接](https://cn.vjudge.net/contest/243410#problem/H)

给定三个$ n * n $的矩阵，问 $ a*b == c?$

### 分析
由于n比较大，直接计算a*b会超时，而且只需要判断是否相等，所以可以随机生成一个 $1 * n$的矩阵$m$通过$m * a * b == m * c?$来确定答案，将负责度降到$O(n^2)$

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
const int maxn = 1e3 + 5;
// head

ll a[maxn][maxn], b[maxn][maxn], c[maxn][maxn];
ll l[maxn];
ll la[maxn], lab[maxn], lc[maxn];

int main() 
{
#ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
#endif

    srand(time(NULL));
    int n;
    cin >> n;
    rep(i, 0, n) 
        rep(j, 0, n) scanf("%lld", &a[i][j]);
    rep(i, 0, n) 
        rep(j, 0, n) scanf("%lld", &b[i][j]);
    rep(i, 0, n) 
        rep(j, 0, n) scanf("%lld", &c[i][j]);

    rep(i, 0, n) l[i] = rand();
    
    rep(i, 0, n)
        rep(j, 0, n) la[i] = (la[i] + l[j] * a[j][i]) % MOD;
    rep(i, 0, n)
        rep(j, 0, n) lab[i] = (lab[i] + la[j] * b[j][i]) % MOD;
    
    rep(i, 0, n)
        rep(j, 0, n) lc[i] = (lc[i] + l[j] * c[j][i]) % MOD;
    
    bool flag = 0;
    rep(i, 0, n) if(lab[i] != lc[i]) flag = 1;

    if(flag == 0)
        printf("YES\n");
    else
        printf("NO\n");
    return 0;
}
```

## I题
### 题目大意
[题目链接](https://cn.vjudge.net/contest/243410#problem/I)

两个人在一颗无根树上面抓另外一个人，被抓的人知道两个人的路线，问那两个人能否抓到

### 分析
如果只有一个人，要抓到犯人，就需要树是一条链，那么两个人的时候，只需要某个人守住某个点，除掉这个点以外的每个子树都是一条链就能够抓到。

可以依次删掉每个度数小于等于2的叶子节点，最后会剩下的节点如果是一条链，就能够抓到

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
const int maxn = 2e5 + 5;
// head

VI vi[maxn];
int in[maxn], vis[maxn];

void dfs(int u)
{
    if(vis[u] || vi[u].size() > 2) return;
    vis[u] = 1;
    rep(i, 0, vi[u].size()) 
    {
        in[vi[u][i]]--;
        dfs(vi[u][i]);    
    }
}

int main() 
{
#ifndef ONLINE_JUDGE
    freopen("in.txt", "r", stdin);
#endif

    int n, u, v;
    cin >> n;
    rep(i, 1, n)
    {
        scanf("%d%d", &u, &v);
        vi[u].pb(v), vi[v].pb(u);
        in[u]++, in[v]++;
    }
    rep(i, 1, n+1) if(vi[i].size() == 1) dfs(i);
    bool flag = 0;
    rep(i, 1, n+1) if(!vis[i] && in[i] > 2) flag = 1;
    if(!flag) printf("YES\n");
    else printf("NO\n");
    return 0;
}
```

## J题
### 题目大意
[题目链接](https://cn.vjudge.net/contest/243410#problem/J)

n个人玩杀人游戏，死了不能复活，最后得到每个人杀人的数，问这个杀人数是否是真实的，并且按照时间顺序打印杀人记录

### 分析
累加和如果大于n-1的话就表示这个杀人数是假的，然后每次选择一个杀人数不为零的杀掉一个杀人数为0的人打印出来

### 代码

```clike
#include <bits/stdc++.h>
using namespace std;
#define rep(i,a,n) for (int i=a;i<n;i++)
#define pb push_back
#define mp make_pair
#define INF 0x3f3f3f3f
typedef long long ll;
typedef pair<int,int> PII;
const int maxn = 2e5 + 5;
// head

int a[maxn];
vector<PII> ans;

int main() 
{
    int n;
    cin >> n;
    ll sum = 0;
    rep(i, 1, n+1)
    {
        scanf("%d", &a[i]);
        sum += a[i];
    }
    if(sum > n-1) printf("NO\n");
    else
    {
        int l = n, r = n;
        for(l = n; l >= 1; l--) if(a[l] != 0) break;
        int flag = 0;
        while(sum--)
        {
            ans.pb(mp(l,r));
            a[l]--;
            if(a[l] == 0) l--;
            r--;
            if(a[r] != 0)
            {
                flag = 1;
                break;
            }
        }
        if(flag) printf("NO\n");
        else
        {
            printf("YES\n");
            rep(i,0,ans.size())
                printf("%d %d\n", ans[i].first, ans[i].second);
        }
    }
    return 0;
}
```