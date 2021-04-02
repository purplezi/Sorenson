### 关于区间操作和求解的各种算法与数据结构

**1.分块与莫队算法**
莫队算法属于离线优雅暴力法，其优化方面主要体现在区间排序、快输入出、运算符优化和常数优化

核心：
* 分块处理
* 区间排序
* 移针计数

**tip:莫队能够处理的数据量较少，遇到大数据（1e6）还是容易T**

模版：一段n个元素的序列，m个询问区间，每一次询问输出区间数的种类
```
#pragma warning(disable:4996)
#include<iostream>
#include<cstdio>
#include<cstring>
#include<string>
#include<algorithm>
#include<cmath>
using namespace std;
const int maxn = 1e6 + 10;
int n, m,a[maxn],belong[maxn],cnt[maxn],ans[maxn];

int read() {//快输入
	int res = 0;
	char c = getchar();
	while (!isdigit(c)) c = getchar();
	while (isdigit(c)) res = (res << 1) + (res << 3) + c - 48, c = getchar();
	return res;
}

void printi(int x) {//快输出
	if (x / 10) printi(x / 10);
	putchar(x % 10 + '0');
}


struct node
{
	int l, r, id;//区间左端点、右端点、离线标号
}q[maxn];

bool cmp(node a,node b)//核心比较函数
{
	return (belong[a.l] ^ belong[b.l]) ? belong[a.l] < belong[b.l] : (belong[a.l] & 1) ? a.r<b.r : a.r>b.r;
}
int main()
{
	scanf("%d", &n);
	for (int i = 1; i <= n; ++i)
	{
		a[i] = read();
	}
	//分块处理
	int siz = sqrt(n),bnum=ceil((double)n/siz);
	for (int i = 1; i <= bnum; ++i)
	{
		for (int j = (i - 1)*siz; j <= i * siz; j++)
		{
			belong[j] = i;
		}
	}

	//区间排序
	scanf("%d", &m);
	for (int i = 1; i <= m; ++i)
	{
		q[i].l = read();
		q[i].r = read();
		q[i].id = i;
	}
	sort(q + 1, q + 1 + m, cmp);

	//移针计数
	int l = 1, r = 0,now=0;
	for (int i = 1; i <= m; ++i)
	{
		int ql = q[i].l, qr = q[i].r;
		while (l < ql) now -= !--cnt[a[l++]];
		while (l > ql) now += !cnt[a[--l]]++;
		while (r < qr) now += !cnt[a[++r]]++;
		while (r > qr) now -= !--cnt[a[r--]];
		ans[q[i].id] = now;
	}
	for (int i = 1; i <= m; ++i)
	{
		printi(ans[i]);
		putchar('\n');
	}
	system("pause");
	return  0;
}
```

**2.树状数组**
[treearray_example](treearray.png)
核心：lowbit操作（取二进制最后一个1出现的位置代表的数）
如图所示
* 修改操作向上更新，则不断i+=lowbit(i)直到n
* 查询操作向左查询，则不断i-=lowbit(i)直到0(**树状数组的区间查询都是基于前缀和相减的思想**)

树状数组比线段树简单，但也只局限完成一定的区间修改和查询（logn)
树状数组直接能解决单点修改和单点查询的问题
* 区间修改时，需要转换成差分数组的单点修改
增加原数组(l,r),则是单点修改差分数组的l和r+1
* 区间查询时，需要转换成差分数组的区间查询（即前缀和查询）
例如求和（l,r),则是运用前缀和相减求区间和：(a为原数组，d为差分数组)
$\sum_{i=1}^{n}a[i]=n*\sum_{i=1}^{n}d[i]-\sum_{i=1}^{n}(d[i]*(i-1))$

**树状数组的最常功能：单点更新+区间查询**（可用来求区间种类，逆序对数等）

模板1：区间加和区间求和
```
	#pragma warning(disable:4996)
	#include<iostream>
	#include<cstdio>
	#include<cstring>
	#include<string>
	#include<algorithm>
	using namespace std;
	const int maxn = 1e5 + 10;
	int n,m;
	long long a[maxn], d[maxn],sum1[maxn],sum2[maxn];//原数组，差分数组，树状数组
	//树状数组递算关系
	int lowbit(int x)
	{
		return x & (-x);
	}

	//区间相加，则变成差分数组的单点增加
	void add(int i,long long k)//第i个元素加k
	{
		int x = i;
		while (i <= n)
		{
			sum1[i] += k;
			sum2[i] += k * (x - 1);
			i += lowbit(i);
		}
	}

	//区间求和。利用差分数组的求和公式(利用前缀和相减得出区间和)
	long long get_sum(int i)//指1-i的前缀和
	{
		long long res = 0, x = i;
		while (i > 0)
		{
			res += x * sum1[i] - sum2[i];
			i -= lowbit(i);
		}
		return res;
	}

	int main()
	{
		scanf("%d%d", &n, &m);
		for (int i = 1; i <= n; ++i)
		{
			scanf("%lld", &a[i]);
			add(i, a[i] - a[i - 1]);
		}
		for (int i = 1; i <= m; ++i)
		{
			getchar();
			char op;
			scanf("%c", &op);
			if (op == 'Q')
			{
				int l, r;
				scanf("%d%d", &l, &r);
				printf("%lld\n", get_sum(r) - get_sum(l - 1));
			}
			else
			{
				int l, r;
				long long k;
				scanf("%d%d%lld", &l, &r, &k);
				add(l, k);
				add(r + 1, -k);
			}
		}
		//system("pause");
		return  0;
	}
```

模版2：一段n个元素的序列，m个询问区间，每一次询问输出区间数的种类
与莫队相比，可以处理更长的数据，而且求种类数恰好用到前缀和的思想
对于种类数需要关注右端点，若是线段树求解则需要多棵树（即持久化），若是用树状数组来解只需要离线按右端点排列区间然后单点修改区间查询（即前缀和）即可
```
	#pragma warning(disable:4996)
	#include<iostream>
	#include<cstdio>
	#include<cstring>
	#include<string>
	#include<algorithm>
	using namespace std;
	const int maxn = 1e6 + 10;
	int a[maxn], c[maxn],visit[maxn],ans[maxn];
	int n, m,nxt;

	int lowbit(int x)
	{
		return x & (-x);
	}

	void add(int i, int k)
	{
		while (i <= n)
		{
			c[i] += k;
			i += lowbit(i);
		}
	}

	int get_sum(int i)
	{
		int res = 0;
		while (i > 0)
		{
			res += c[i];
			i -= lowbit(i);
		}
		return res;
	}

	struct node
	{
		int l, r, id;
	}q[maxn];

	bool cmp(node a, node b)
	{
		if (a.r == b.r)
		{
			return a.l < b .l;
		}
		return a.r < b.r;
	}
	int main()
	{
		scanf("%d", &n);
		for (int i = 1; i <= n; i++)
		{
			scanf("%d", &a[i]);
		}
		scanf("%d", &m);
		for (int i = 1; i <= m; i++)
		{
			scanf("%d%d", &q[i].l, &q[i].r);
			q[i].id = i;
		}
		sort(q + 1, q + 1 + m, cmp);
		nxt = 1;
		for (int i = 1; i <= m; i++)
		{
			int ql = q[i].l, qr = q[i].r;
			for (int j = nxt; j <= qr; j++)
			{
				int k = a[j];
				if (!visit[k])
				{
					add(j, 1);
					visit[k] = j;
				}
				else if(visit[k]<j)
				{
					add(visit[k], -1);
					add(j, 1);
					visit[k] = j;
				}
			}
			ans[q[i].id] = get_sum(qr)-get_sum(ql-1);
			nxt = qr + 1;
		}
		for (int i = 1; i <= m; i++)
		{
			printf("%d\n", ans[i]);
		}
		system("pause");
		return  0;
	}


```

模板3：利用树状数组求逆序对
核心：遍历到原数列的每一个数，用它的当前位置减去原位置树状数组已经插入过的数量，剩下的就是逆序的
```
#pragma warning(disable:4996)
#include<iostream>
#include<cstdio>
#include<cstring>
#include<string>
#include<algorithm>
#include<map>
using namespace std;
const int maxn = 1e6 + 10;
int n,c[maxn];

int lowbit(int x)
{
	return x & (-x);
}

void add(int i, int k)
{
	while (i <= n)
	{
		c[i] += k;
		i += lowbit(i);
	}
}

int get_sum(int i)
{
	int res = 0;
	while (i > 0)
	{
		res += c[i];
		i -= lowbit(i);
	}
	return res;
}


long long a[maxn], b[maxn];


map<int, int> mp;
int main()
{
	scanf("%d", &n);
	for (int i = 1; i <= n; ++i)
	{
		scanf("%lld", &a[i]);
		b[i] = a[i];
	}
	sort(a + 1, a + 1 + n);
	for (int i = 1; i <= n; ++i)
	{
		mp[a[i]] = i;
	}
	int ans = 0;
	for (int i = 1; i <= n; ++i)
	{
		add(mp[b[i]], 1);
		ans += i - get_sum(mp[b[i]]);
	}
	printf("%d\n", ans);
	system("pause");
	return  0;
}



```

tip:
当涉及**逐个插入和持久化查询区间和**的时候（即**单点更新和区间查询**），树状数组就有极大的用武之地

**3.线段树与扫描线**
线段树是一种用数组表示的二叉树
线段树四大函数：pushup、pushdown、update、query

以最简单的区间更新和区间求和模板为例(一点三数——l,r,rt)
```
void pushup(int rt)
{
	sum[rt]=(sum[rt<<1]+sum[rt<<1|1])%mod;
}

void pushdown(int rt,int m)//m为该点区间长度,lazy表示自己已处理但孩子未处理
{
	if(lazt[rt])
	{
		lazy[rt<<1]+=lazy[rt];
		lazy[rt<<1|1]+=lazy[rt];
		sum[rt<<1]+=lazy[rt]*(m-m>>1);
		sum[rt<<1]%mod;
		sum[rt<<1|1]+=lazy[rt]*(m>>1);
		sum[rt<<1|1]%mod;
		lazy[rt]=0;	
	}
}

void build(int l,int r,int rt)
{
	lazy[rt]=0;
	if(l==r)
	{
		sum[rt]=wt[l];//
		sum[rt]%mod;
		return;
	}
	int m=(l+r)>>1;
	build(l,m,rt<<1);
	build(m+1,r,rt<<1|1);
	pushup(rt);
}

void query(int L,int R,int l,int r,int rt)//L,R始终不变，目的是求出组合出它的各区间节点
{
	if(L<=l && R>=r)
	{
		res+=sum[rt];
		res%mod;
		return;
	}
	pushdown(rt,r-l+1);
	int m=(l+r)>>1;
	if(L<=m)
	{
		query(L,R,l,m,rt<<1);
	}
	if(R>m)
	{
		query(L,R,m+1,r,rt<<1|1);
	}
}
```


线段树+扫描线求覆盖面积
```
#pragma warning(disable:4996)
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<map>
#include<cstring>
using namespace std;
int n;
map<double,int> ymp;//离散化处理
double raw[20005];

struct F//竖直扫描线沿水平方向扫描
{
	double x,y1,y2;//竖直扫描线的横坐标，上端坐标和下端坐标
	int flag;//标记扫描时的入边（1）和出边（-1）
}four[20005];

bool cmp(F a, F b)
{
	return a.x<b.x;
}

struct T
{
	double len;//len维护区间覆盖一次以上的长度
	int cnt;//与出入边辅助计算+1-1
}tree[20005<<2];	

void build(int rt,int l,int r)
{
	if(l==r)
	{
		
	}
}
int main()
{

	return 0;
}
```
**4.主席树**


**5.树链剖分**
```
//题目描述 
//
//
//如题，已知一棵包含N个结点的树（连通且无环），每个节点上包含一个数值，需要支持以下操作：
//操作1： 格式： 1 x y z 表示将树从x到y结点最短路径上所有节点的值都加上z
//操作2： 格式： 2 x y 表示求树从x到y结点最短路径上所有节点的值之和
//操作3： 格式： 3 x z 表示将以x为根节点的子树内所有节点值都加上z
//操作4： 格式： 4 x 表示求以x为根节点的子树内所有节点值之和
//
//
//输入格式：
//
//
//第一行包含4个正整数N、M、R、P，分别表示树的结点个数、操作个数、根节点序号和取模数（即所有的输出结果均对此取模）。
//
//接下来一行包含N个非负整数，分别依次表示各个节点上初始的数值。
//
//接下来N - 1行每行包含两个整数x、y，表示点x和点y之间连有一条边（保证无环且连通）
//
//接下来M行每行包含若干个正整数，每行表示一个操作，格式如下：
//
//操作1： 1 x y z
//
//操作2： 2 x y
//
//操作3： 3 x z
//
//操作4： 4 x
//
//
//
//
//输出格式：
//
//
//输出包含若干行，分别依次表示每个操作2或操作4所得的结果（对P取模）
//
//
//
//
//输入样例#1
//
//5 5 2 24
//7 3 7 8 0
//1 2
//1 5
//3 1
//4 1
//3 4 2
//3 2 2
//4 5
//1 5 1 3
//2 1 3
//1234567891011
//
//
//
//输出样例#1
//
//2
//21

#pragma warning(disable:4996)
#include<iostream>
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;
#define mem(a, b) memset(a, b, sizeof(a))
#define lson l, m, rt << 1
#define rson m + 1, r, rt << 1 | 1
const int N = 2e5 + 10;
int sum[N << 2], lazy[N << 2]; //线段树求和
int n, m, r, mod;              //节点数，操作数，根节点，模数
int head[N], tot;             //邻接表
//分别为:重儿子，每个节点新编号(dfs序），父亲，编号，深度，子树个数，所在重链的顶部（轻链则为本身）
int son[N], id[N], fa[N], cnt, dep[N], siz[N], top[N];
int w[N], wt[N]; //初始点权,新编号点权
int res = 0;     //查询答案

struct edge
{
	int v, next;
} e[N];

void add_edge(int u, int v)//链式前向星存图加边
{
	e[tot].v = v;
	e[tot].next = head[u];
	head[u] = tot++;
}

void init()//初始化
{
	mem(head, -1);
	tot = 0;
	cnt = 0;
}

int pushup(int rt)//求和的标记上移
{
	sum[rt] = (sum[rt << 1] + sum[rt << 1 | 1]) % mod;
}

void pushdown(int rt, int m) //下放lazy标记
{
	if (lazy[rt])
	{
		lazy[rt << 1] += lazy[rt];                 //给左儿子下放lazy
		lazy[rt << 1 | 1] += lazy[rt];             //给右儿子下放lazy
		sum[rt << 1] += lazy[rt] * (m - (m >> 1)); //更新sum
		sum[rt << 1] %= mod;
		sum[rt << 1 | 1] += lazy[rt] * (m >> 1);
		sum[rt << 1 | 1] %= mod;
		lazy[rt] = 0;
	}
}

void build(int l, int r, int rt)
{
	lazy[rt] = 0;
	if (l == r)
	{
		sum[rt] = wt[l]; //新的编号点权
		sum[rt] %= mod;
		return;
	}
	int m = (l + r) >> 1;
	build(lson);
	build(rson);
	pushup(rt);
}

void update(int L, int R, int c, int l, int r, int rt)
{
	if (L <= l && r <= R)
	{
		lazy[rt] += c;
		sum[rt] += c * (r - l + 1);
		sum[rt] %= mod;
		return;
	}
	pushdown(rt, r - l + 1);
	int m = (l + r) >> 1;
	if (L <= m)
		update(L, R, c, lson);
	if (R > m)
		update(L, R, c, rson);
	pushup(rt);
}

void query(int L, int R, int l, int r, int rt)
{
	if (L <= l && r <= R)
	{
		res += sum[rt];
		res %= mod;
		return;
	}
	pushdown(rt, r - l + 1);
	int m = (l + r) >> 1;
	if (L <= m)
		query(L, R, lson);
	if (R > m)
		query(L, R, rson);
}
//----------------------------------------------------------------
//处理出fa[],dep[],siz[],son[]
void dfs1(int u, int f, int deep)
{
	dep[u] = deep;   //标记深度
	fa[u] = f;       //标记节点的父亲
	siz[u] = 1;      //记录每个节点子树大小
	int maxson = -1; //记录重儿子数量
	for (int i = head[u]; ~i; i = e[i].next)
	{
		int v = e[i].v;
		if (v == f)
			continue;
		dfs1(v, u, deep + 1);
		siz[u] += siz[v];//子树数量
		if (siz[v] > maxson) //儿子里最多siz就是重儿子
		{
			son[u] = v; //标记u的重儿子为v
			maxson = siz[v];
		}
	}
}

//处理出top[],wt[],id[]
void dfs2(int u, int topf)
{
	id[u] = ++cnt;  //每个节点的新编号
	wt[cnt] = w[u]; //新编号的对应权值
	top[u] = topf;  //标记每个重链的顶端
	if (!son[u])    //没有儿子时返回
		return;
	dfs2(son[u], topf); //搜索下一个重儿子
	for (int i = head[u]; ~i; i = e[i].next)
	{
		int v = e[i].v;
		if (v == fa[u] || v == son[u]) //处理轻儿子
			continue;
		dfs2(v, v); //每一个轻儿子都有一个从自己开始的链，起点就是自己
	}
}

void updrange(int x, int y, int k)
{
	k %= mod;
	while (top[x] != top[y])//不在同一条重链
	{
		if (dep[top[x]] < dep[top[y]]) //使x深度较大
			swap(x, y);//选择链头深度大的往上跳，是链头深度大的！！！！
		update(id[top[x]], id[x], k, 1, n, 1);
		x = fa[top[x]];
	}//循环完后在一条重链上了，实际上剩下的就是lca到另一个点的未处理部分
	if (dep[x] > dep[y]) //使x深度较小
		swap(x, y);
	update(id[x], id[y], k, 1, n, 1);
}

int qrange(int x, int y)
{
	int ans = 0;
	while (top[x] != top[y]) //当两个点不在同一条链上
	{
		if (dep[top[x]] < dep[top[y]]) //使x深度较大
			swap(x, y);
		res = 0;
		query(id[top[x]], id[x], 1, n, 1);
		//ans加上x点到x所在链顶端这一段区间的点权和
		ans += res;
		ans %= mod;
		x = fa[top[x]]; //x跳到x所在链顶端的这个点的上面一个点
	}
	//当两个点处于同一条链
	if (dep[x] > dep[y]) //使x深度较小
		swap(x, y);
	res = 0;
	query(id[x], id[y], 1, n, 1);
	ans += res;
	return ans % mod;
}

void upson(int x, int k)
{
	update(id[x], id[x] + siz[x] - 1, k, 1, n, 1); //子树区间右端点为id[x]+siz[x]-1
}

int qson(int x)
{
	res = 0;
	query(id[x], id[x] + siz[x] - 1, 1, n, 1);
	return res;
}

int main()
{
	// freopen("in.txt", "r", stdin);
	int u, v;
	scanf("%d%d%d%d", &n, &m, &r, &mod);
	init();
	for (int i = 1; i <= n; i++)
		scanf("%d", &w[i]);
	for (int i = 1; i <= n - 1; i++)
	{
		scanf("%d%d", &u, &v);
		add_edge(u, v);
		add_edge(v, u);
	}
	dfs1(r, 0, 1);
	dfs2(r, r);
	build(1, n, 1); //用新点权建立线段树
	while (m--)
	{
		int op, x, y, z;
		scanf("%d", &op);
		if (op == 1)
		{
			scanf("%d%d%d", &x, &y, &z);
			updrange(x, y, z);
		}
		else if (op == 2)
		{
			scanf("%d%d", &x, &y);
			printf("%d\n", qrange(x, y));
		}
		else if (op == 3)
		{
			scanf("%d%d", &x, &z);
			upson(x, z);
		}
		else if (op == 4)
		{
			scanf("%d", &x);
			printf("%d\n", qson(x));
		}
	}
	return 0;
}

```

**4.伸展树**

```
//伸展树SplayTree,可以实现线段树功能和其他区间翻转、环流、旋转、排序等等操作的恐怖数据结构
#pragma warning(disable:4996)
#include <iostream>
#include <cstdio>
#include <cstring>
#include <stack>
#include <vector>
#include <algorithm>
#include <queue>
#define rep(i) for (int i=0; i<n; i++)
#pragma comment(linker, "/STACK:1024000000,1024000000")
using namespace std;
typedef long long ll;
const int N = 100005, inf = 0x3f3f3f3f;

typedef struct splaynode* node;
struct splaynode {
	node pre, ch[2];//父节点，左右儿子节点
	ll value, lazy, maxormin, sum;
	int size, rev;
	void init(int _value) {
		pre = ch[0] = ch[1] = NULL;
		maxormin = value = sum = _value;
		lazy = rev = 0;
		size = 1;
	}
}mem[N];
int memtop;

stack<node> S;
node root;

inline int getsize(node &x) {
	return x ? x->size : 0;
}

void pushdown(node &x) {
	if (!x) return;
	if (x->lazy) {
		ll w = x->lazy;
		x->value += w;
		if (x->ch[0]) {
			x->ch[0]->lazy += w;
			x->ch[0]->maxormin += w;
			x->ch[0]->sum += w * getsize(x->ch[0]);
		}
		if (x->ch[1]) {
			x->ch[1]->lazy += w;
			x->ch[1]->maxormin += w;
			x->ch[1]->sum += w * getsize(x->ch[1]);
		}
		x->lazy = 0;
	}
	if (x->rev) {
		node t = x->ch[0];
		x->ch[0] = x->ch[1];
		x->ch[1] = t;
		x->rev = 0;
		if (x->ch[0]) x->ch[0]->rev ^= 1;
		if (x->ch[1]) x->ch[1]->rev ^= 1;
	}
}

void update(node &x) {
	if (!x) return;
	x->size = 1;
	x->maxormin = x->value;
	x->sum = x->value;
	if (x->ch[0]) {
		x->sum += x->ch[0]->sum;
		x->maxormin = min(x->maxormin, x->ch[0]->maxormin);
		x->size += x->ch[0]->size;
	}
	if (x->ch[1]) {
		x->sum += x->ch[1]->sum;
		x->maxormin =min(x->maxormin, x->ch[1]->maxormin);
		x->size += x->ch[1]->size;
	}
}

void rotate(node &x, int d) {//普旋
	node y = x->pre;
	pushdown(y);
	pushdown(x);
	pushdown(x->ch[d]);
	y->ch[!d] = x->ch[d];
	if (x->ch[d] != NULL) x->ch[d]->pre = y;
	x->pre = y->pre;
	if (y->pre != NULL)
		if (y->pre->ch[0] == y) y->pre->ch[0] = x; else y->pre->ch[1] = x;
	x->ch[d] = y;
	y->pre = x;
	update(y);
	if (y == root) root = x;
}

void splay(node &src, node &dst) {//双旋
	pushdown(src);
	while (src != dst) {
		if (src->pre == dst) {
			if (dst->ch[0] == src) rotate(src, 1); else rotate(src, 0);
			break;
		}
		else {
			node y = src->pre, z = y->pre;
			if (z->ch[0] == y) {
				if (y->ch[0] == src) {
					rotate(y, 1);
					rotate(src, 1);
				}
				else {
					rotate(src, 0);
					rotate(src, 1);
				}
			}
			else {
				if (y->ch[1] == src) {
					rotate(y, 0);
					rotate(src, 0);
				}
				else {
					rotate(src, 1);
					rotate(src, 0);
				}
			}
			if (z == dst) break;
		}
		update(src);
	}
	update(src);
}

void select(int k, node &f) {
	int tmp;
	node t = root;
	while (1) {
		pushdown(t);
		tmp = getsize(t->ch[0]);
		if (k == tmp + 1) break;
		if (k <= tmp) t = t->ch[0];
		else {
			k -= tmp + 1;
			t = t->ch[1];
		}
	}
	pushdown(t);
	splay(t, f);
}

inline void selectsegment(int l, int r) {//通过旋转选择区间
	select(l, root);
	select(r + 2, root->ch[1]);
}

void insert(int pos, int value) {  //在pos位置后面插入一个新值value
	selectsegment(pos + 1, pos);
	node t;
	node x = root->ch[1];
	pushdown(root);
	pushdown(x);
	if (!S.empty()) {
		t = S.top();
		S.pop();
	}
	else {
		t = &mem[memtop++];
	}
	t->init(value);
	t->ch[1] = x;
	x->pre = t;
	root->ch[1] = t;
	t->pre = root;
	splay(x, root);
}

void add(int a, int b, int value) {  //区间[a,b]中的数都加上value
	selectsegment(a, b);
	node x = root->ch[1]->ch[0];
	pushdown(x);
	update(x);
	x->maxormin += value;
	x->lazy += value;
	splay(x, root);
}

void reverse(int a, int b) {   //区间[a,b]中的数翻转
	selectsegment(a, b);
	root->ch[1]->ch[0]->rev ^= 1;
	node x = root->ch[1]->ch[0];
	splay(x, root);
}

void revolve(int a, int b, int t) { //区间[a,b]中的数向后循环移t位
	node p1, p2;
	selectsegment(a, b);
	select(b + 1 - t, root->ch[1]->ch[0]);
	p1 = root->ch[1]->ch[0];
	pushdown(p1);
	p2 = p1->ch[1];
	p1->ch[1] = NULL;

	select(a + 1, root->ch[1]->ch[0]);
	p1 = root->ch[1]->ch[0];
	pushdown(p1);
	p1->ch[0] = p2;
	p2->pre = p1;

	splay(p2, root);
}

ll getmaxormin(int a, int b) {   //取[a,b]中最小的值
	selectsegment(a, b);
	node x = root->ch[1];
	pushdown(x);
	x = x->ch[0];
	pushdown(x);
	update(x);
	return x->maxormin;
}

ll getsum(int a, int b) {//取[a,b]的和
	selectsegment(a, b);
	node x = root->ch[1];
	pushdown(x);
	x = x->ch[0];
	pushdown(x);
	update(x);
	return x->sum;
}

void erase(int pos) {               //抹去第pos个元素
	selectsegment(pos, pos);
	pushdown(root->ch[1]);
	S.push(root->ch[1]->ch[0]);        //回收内存
	root->ch[1]->ch[0] = NULL;
	node x = root->ch[1];
	splay(x, root);
}


void cutandmove(int a, int b, int c)//移动区间[l,r]到位置c后
{
	selectsegment(a, b);
	node CutRoot = root->ch[1]->ch[0];
	CutRoot->pre = NULL;
	root->ch[1]->size -= CutRoot->size;
	root->ch[1]->ch[0] = NULL;

	selectsegment(c + 1, c);

	CutRoot->pre = root->ch[1];
	root->ch[1]->ch[0] = CutRoot;
	root->ch[1]->size += CutRoot->size;
}

void cut(int a, int b)//删除区间[l.r]
{
	selectsegment(a, b);
	node CutRoot = root->ch[1]->ch[0];
	CutRoot->pre = NULL;
	root->size -= CutRoot->size;
	root->ch[1]->size -= CutRoot->size;
	root->ch[1]->ch[0] = NULL;
}

vector<int> ans;
void inorder(node x)
{
	if (!x) return;
	pushdown(x);
	inorder(x->ch[0]);
	if (x->value != inf) ans.push_back(x->value);
	inorder(x->ch[1]);
}

void initsplaytree(ll *a, int n) {
	memtop = 0;
	root = &mem[memtop++];
	root->init(inf);
	root->ch[1] = &mem[memtop++];
	root->ch[1]->init(inf);
	while (!S.empty()) S.pop();//清空回收队列
	rep(i) insert(i, a[i]);
}


/*----------Splay Template Over----------*/
ll v[N];
int main() {
	int n, m;
	scanf("%d", &n);
	rep(i) scanf("%d", &v[i]);
	initsplaytree(v, n);
	scanf("%d", &m);
	while (m--) {
		char s[50];
		scanf("%s", s);
		if (s[0] == 'A') {
			int l, r, d;
			scanf("%d%d%d", &l, &r, &d);
			add(l, r, d);
		}
		if (s[0] == 'R') {
			int l, r;
			scanf("%d%d", &l, &r);
			if (s[3] == 'E') reverse(l, r);
			else {
				int k;
				scanf("%d", &k);
				int tn = r - l + 1;
				k = (k%tn + tn) % tn;
				if (l == r || k == 0) continue;
				revolve(l, r, k);
			}
		}
		if (s[0] == 'I') {
			int x, d;
			scanf("%d%d", &x, &d);
			insert(x, d);
		}
		if (s[0] == 'D') {
			int x;
			scanf("%d", &x);
			erase(x);
		}
		if (s[0] == 'M') {
			int l, r;
			scanf("%d%d", &l, &r);
			printf("%d\n", getmaxormin(l, r));
		}
	}
	system("pause");
	return 0;
}

```

