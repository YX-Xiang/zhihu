#! https://zhuanlan.zhihu.com/p/687933236
# Codeforces Round 935 (Div. 3) A-B (贪心) C (搜索) D (dp+贪心) E (二分+枚举)
难得有一场中国友好时间的cf了！虽然很久没刷cf了，还是来凑了个热闹。这场是很经典的cf div3风格，思维量略大，但算法含量略低。
（以及终于学会用vscode写知乎文章了！md语法写起来太舒服了）

## A. Setting up Camp
https://codeforces.com/contest/1945/problem/A

### 题意
现有 $a$ 个i人，$b$ 个e人和 $c$ 个中间人。i人要求独自居住一间帐篷，e人必须要求有两个室友一起住帐篷，中间人都行。每间帐篷最多居住3个人。问最少需要多少帐篷？

### 思路
i人最好处理，不需要考虑室友。贪心地可以先将e人三个一组。如果多出来1-2个e人，看是否能和中间人拼盘住。拼不了则证明不存在解。剩下的中间人也三人一组，住不满一间帐篷也无所谓。

### 代码
```c++
int t;cin>>t;
while(t--) {
    ll a,b,c, ans=0;
    cin>>a>>b>>c;
    if(b%3 && b%3+c<3) cout<<-1<<endl;
    else {
        ans+=a;
        ans+=b/3;
        ans+=(b%3+c+2)/3;
        cout<<ans<<endl;
    }
}
```

## B. Fireworks
https://codeforces.com/contest/1945/problem/B

### 题意
一个人拥有 $m+1$ 分钟去欣赏烟花。已知第一种烟花每隔 $a$ 分钟绽放，即会在 $a, 2a, 3a, \ldots $ 分钟的时候绽放；第二种烟花每隔 $b$ 分钟绽放。问这个人最多能看见多少次烟花？

### 思路
假设此人停留的时间为 $x$ 到 $x+m$。

$m+1$ 分钟内能欣赏到的第一种烟花最多是 $\lfloor \frac{m}{a}\rfloor + 1$ 次，即到的第 $x$ 分钟刚好欣赏到一次绽放，接下来的 $m$ 分钟可以欣赏 $\lfloor \frac{m}{a}\rfloor$ 次。第二种烟花同理。

所以最理想的情况应该是此人一到，就能立即欣赏到两个烟花一起绽放，也就是在 $\gcd(a, b)$ 时刻到达。所以答案就是 $\lfloor \frac{m}{a}\rfloor + \lfloor \frac{m}{b}\rfloor + 2$。

### 代码
```c++
#define ll long long
int main() {
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
    int t;cin>>t;
    while(t--) {
        ll a,b,m,ans=0;
        cin>>a>>b>>m;
        ans += m/a + 1;
        ans += m/b + 1;
        cout<<ans<<endl;
    }
    return 0;
}
```

## C. Left and Right Houses
https://codeforces.com/contest/1945/problem/C

### 题意
要在 $n (\sum n\leq 3\cdot 10^5)$ 个房子间建一堵墙。现征求 $n$ 个业主的意见，想住在墙的左侧还是右侧。最后墙的位置 $i$ 需要分别满足墙两侧至少一半居民的意见（假设一侧有 $x$ 个居民，需要满足至少 $\lceil\frac{x}{2}\rceil$ 个居民的意见；另一侧需要满足 $\lceil\frac{n-x}{2}\rceil$ 个居民意见）。最小化 $|\frac{n}{2}-i|$。若存在多个 $i$，则输出最小的 $i$。

### 思路
用最优可行解的思路去看这个问题。首先这个问题的解空间的范围是确定的：$0\leq i\leq n$。这意味着最多只需要 $O(n)$ 的时间暴力搜索答案。优化策略无非是最优解里找可行解或者可行解里找最优解。

问题一定存在可行解：$i=0$ 或 $n$。因为一定存在大于等于半数的居民想住在墙左边或者右边，那么把墙放在最右或最左即可。最优解就是 $i=\lfloor\frac{n}{2}\rfloor$，但未必可行。

从最优里找可行可以让这个问题的优化过程是连续的。因此只需要枚举 $\lfloor\frac{n}{2}\rfloor \pm j( j\geq 0)$。

而由于奇偶数存在差异，在枚举 $j$ 的时候要注意左右顺序。具体看代码吧。

### 代码

```c++
#include<bits/stdc++.h>

int sum[300005], n;

bool f(int x) {
    int l = x - sum[x], r = sum[n] - sum[x];
    if(l >= (x+1)/2 && r >= (n-x+1)/2) return 1;
    else return 0;
}

int main() {
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
    int t;cin>>t;
    while(t--) {
        int l;string s;
        cin>>n>>s;
        l = s.size();
        for(int i=1;i<=l;i++) {
            if(s[i-1]=='1') {
                sum[i] = sum[i-1] + 1;
            } else {
                sum[i] = sum[i-1];
            }
        }
        int mid  = n / 2, x = mid;
        if(n%2) {
            if(!f(mid)) {
                for(int i = 1;i <= mid+1; i++) {
                    if(mid + i <= n) {
                        x = mid + i;
                        if(f(x)) break;
                    }
                    if(mid - i >= 0) {
                        x = mid - i;
                        if(f(x)) break;
                    }
                }
            }
        } else {
            if(!f(mid)) {
                for(int i = 1;i <= mid+1; i++) {
                    if(mid - i >= 0) {
                        x = mid - i;
                        if(f(x)) break;
                    }
                    if(mid + i <= n) {
                        x = mid + i;
                        if(f(x)) break;
                    }
                }
            }
        }
        cout<<x<<endl;
    }
    return 0;
}
```

## D. Seraphim the Owl
https://codeforces.com/contest/1945/problem/D

### 题意
$n$ 个人在队伍中排在Kirill的前面。假设他位于位置 $j$（$j$ 一开始为 $n+1$），他可以做无限次如下操作：和队伍中第 $i$ 个人交换位置，付出的代价为 $a[i] + \sum_{k=i+1}^{j-1}b[k]$。

问Kirill想要挤进队伍的前 $m(1\leq m\leq n\leq 2\cdot 10^5)$ 个位置中的任意一个需要付出的最小代价。

### 思路
先考虑状态转移，会发现很好列出来。令 $dp[i]$ 表示到达第 $i$ 个位置的最小花费：

$$ dp[i] = \min \{dp[j] + a[i] + \sum_{k=i+1}^{j-1}b[k] | j\in[i+1, n+1]\}.$$

但在进一步思考状态转移的过程中，你会发现，假设最终要抵达 $i$ 号位置，那么 $k\in [i+1,j-1]$ 区间内的点要么选择代价 $a[k]$，要么选择代价 $b[k]$。因此在 $[m+1, n]$ 上，我们选择的代价一定是 $\min(a,b)$。

$$dp[m+1] = \sum_{i=m+1}^{n} \min(a[i], b[i]).$$

而最后在 $[1, m]$ 的落脚点（即选择a作为代价，并终止操作的地方）不确定，但肯定只存在一个。因此实际上最小化的目标是：

$$\min_{i\in[1, m]} a[i] + \sum_{j=i+1}^{m} b[j].$$

式子后半部分可以用前缀和 $O(n)$ 求解。因此只需要扫描一遍 $i$，求最优解就可以。

### 代码
```c++
#include<bits/stdc++.h>
#define ll long long
#define U unsigned
#define endl '\n'
#define sqr(x) ((x)*(x))
#define mem(x,y) memset(x,y,sizeof(x))
#define PII pair<int,int>
using namespace std;
const double eps=1e-8;
const double pi=acos(-1.0);
const int INF=0x3f3f3f3f;
const int MOD=998244353;
ll gcd(ll a,ll b){return b?gcd(b,a%b):a;}
int lcm(int a,int b){return a/gcd(a,b)*b;}
ll powmod(ll a,ll b,ll MOD){ll ans=1;while(b){if(b&1)ans=ans*a%MOD;a=a*a%MOD;b>>=1;}return ans;}

ll a[200005], b[200005], sumb[200005];

int main() {
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
    int t;cin>>t;
    while(t--) {
        int n,m,p;
        ll ans=0, minx;
        cin>>n>>m;
        p=n+1;
        for(int i=1;i<=n;i++) {
            cin>>a[i];
        }
        for(int i=1;i<=n;i++) {
            cin>>b[i];
            sumb[i]=sumb[i-1]+b[i];
        }
        for(int i=m+1;i<=n;i++) {
            if(a[i]<=b[i]) {
                p=i;
                break;
            }
        }
        for(int i=n;i>=p;i--) {
            ans+=min(a[i], b[i]);
        }
        minx = a[m] + sumb[p-1] - sumb[m];
        for(int i=1;i<m;i++) {
            minx = min(minx, a[i] + sumb[p-1] - sumb[i]);
        }
        ans+=minx;
        cout<<ans<<endl;
    }
    return 0;
}
```

## E. Binary Search
https://codeforces.com/contest/1945/problem/E

### 题意
某人写了个二分搜索在长度为 $n$ 的排列 $p$（数 $1$ ~ $n$ 各出现一次）中搜索 $x(1\leq x\leq n\leq 2\cdot 10^5)$，但搜索前数组未排序。

二分伪代码如下：
```
l = 1, r = n + 1
while( r-l > 1 ) do
    m = (r - l) // 2
    if p_m <= x:
        l = m
    else 
        r = m
end
```

问如何调换排列中两个数的位置，可以最终找到 $x$。仅允许调换两次。

### 思路
以下思路仅限于他写的二分代码是不会死循环的二分的前提下展开的（这一点我没仔细验证，应该不会死循环）：

假设二分程序通过一系列判断操作最终锁定了数字 $y$。此时分三种情况：

1. $y=x$：不需要任何操作。

2. $p_{m_i}$ 从未等于 $x$：那么直接交换 $x$ 和 $y$。不会对之前的操作产生影响。

3. 某一步判断中 $p_{m_i} = x$。若 $y<x$，可以直接交换 $x$ 和 $y$；否则需要找到一个比 $x$ 小的元素 $z$ 把 $x$ 先置换出去，满足 $z=p'_{m_i}<p_{m_i}\leq x$，最后 $x$ 才能与 $y$ 进行交换。

第三种情况存在一种可能：如果 $x$ 是最小的元素怎么办？

> 若 $x=1$ 且 $p_{m_i} = x$，则 $y=x$。

口胡证明：因为其余元素都大于 $x$，因此在 $p_{m_i} = x$ 前，$m$ 会使区域不断向左收缩，形成 $[1,m_{i-1}]$。而 $p_{m_i} = x$ 时，$l:1\to m_i$。此后也只存在 $r$ 的变化。因此最终一定停在 $p_{l} = x$。

### 代码
```c++
#include<bits/stdc++.h>
#define ll long long
#define U unsigned
#define endl '\n'
#define sqr(x) ((x)*(x))
#define mem(x,y) memset(x,y,sizeof(x))
#define PII pair<int,int>
using namespace std;
const double eps=1e-8;
const double pi=acos(-1.0);
const int INF=0x3f3f3f3f;
const int MOD=998244353;
ll gcd(ll a,ll b){return b?gcd(b,a%b):a;}
int lcm(int a,int b){return a/gcd(a,b)*b;}
ll powmod(ll a,ll b,ll MOD){ll ans=1;while(b){if(b&1)ans=ans*a%MOD;a=a*a%MOD;b>>=1;}return ans;}

int p[200005];
bool vis[200005];

int main() {
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
    int t;cin>>t;
    while(t--) {
        int n,x,pos;
        cin>>n>>x;
        for(int i=1;i<=n;i++) {
            cin>>p[i];
            vis[i]=0;
            if(p[i]==x) pos=i;
        }
        int l = 1, r = n + 1, mid, f=0;
        while(r - l > 1) {
            mid = (r+l)/2;
            vis[mid]=1;
            if(p[mid]==x) f=1;
            if(p[mid]<=x) l=mid;
            else r=mid;
        }
        if(p[l] == x) cout<<0<<endl;
        else if(f==0) {
            cout<<1<<endl;
            cout<<min(pos, l)<<" "<<max(pos, l)<<endl;
        } else {
            if(p[l]<x) {
                cout<<1<<endl;
                cout<<min(pos, l)<<" "<<max(pos, l)<<endl;
            } else {
                cout<<2<<endl;
                cout<<min(pos, l)<<" "<<max(pos, l)<<endl;
                int pos2;
                for(int i=1;i<=n;i++) {
                    if(!vis[i] && p[i]<x) {
                        pos2 = i;
                        break;
                    }
                }
                cout<<min(pos, pos2)<<" "<<max(pos, pos2)<<endl;
            }
        }
    }
    return 0;
}
```

## T. B. C.