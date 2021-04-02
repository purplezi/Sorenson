### 树上差分

```
//差分数组：O(1)修改区间,O(n)查询，差分数组的前缀和数组即为原数组
//树上差分维护的差分对应的链为x到根节点的链，树上差分的查询即针对子树和的搜索查询
//关于点的差分：所有路径某点被经过的次数、修改两点间路径所有点的权值


//题目描述
//松鼠的新家是一棵树，前几天刚刚装修了新家，新家有n个房间，并且有n - 1根树枝连接，每个房间都可以相互到达，且俩个房间之间的路线都是唯一的。天哪，他居然真的住在”树“上。
//松鼠想邀请小熊维尼前来参观，并且还指定一份参观指南，他希望维尼能够按照他的指南顺序，先去a1，再去a2，......，最后到an，去参观新家。
//可是这样会导致维尼重复走很多房间，懒惰的维尼不停地推辞。可是松鼠告诉他，每走到一个房间，他就可以从房间拿一块糖果吃。
//维尼是个馋家伙，立马就答应了。现在松鼠希望知道为了保证维尼有糖果吃，他需要在每一个房间各放至少多少个糖果。
//因为松鼠参观指南上的最后一个房间an是餐厅，餐厅里他准备了丰盛的大餐，所以当维尼在参观的最后到达餐厅时就不需要再拿糖果吃了。
//输入格式
//第一行一个整数n，表示房间个数第二行n个整数，依次描述a1 - an
//接下来n - 1行，每行两个整数x，y，表示标号x和y的两个房间之间有树枝相连。
//输出格式
//一共n行，第i行输出标号为i的房间至少需要放多少个糖果，才能让维尼有糖果吃。
//
//输入：
//5
//1 4 5 3 2
//1 2
//2 4
//2 3
//4 5
//
//输出：
//1
//2
//1
//2
//1


//#pragma warning(disable:4996)
//#include<iostream>
//#include <cstdio>
//#include <cstring>
//#include<algorithm>
//using namespace std;
//struct node {
//	int next, to;
//}; node edge[600010];
//int n, cnt;
//int a[300010], head[600010], deep[300010], p[300010][25], sum[300010];
//
//void change(int &a, int &b)
//{
//	int t = a; a = b; b = t;
//}
//
//void add(int a, int b)//链式前向星加边
//{
//	edge[++cnt].to = b;
//	edge[cnt].next = head[a];
//	head[a] = cnt;
//}
//
//void dfs(int a, int fa)
//{
//	deep[a] = deep[fa] + 1;//深度
//	p[a][0] = fa;
//	for (int i = 1; (1 << i) <= deep[a]; i++)
//		p[a][i] = p[p[a][i - 1]][i - 1];//RMQ预处理以找LCA
//	for (int i = head[a]; i; i = edge[i].next)
//	{
//		int v = edge[i].to;
//		if (v != fa)
//			dfs(v, a);
//	}
//}
//
//int lca(int a, int b)
//{
//	if (deep[a] > deep[b]) change(a, b);
//	for (int i = 20; i >= 0; i--)
//	{
//		if (deep[a] <= deep[b] - (1 << i))
//		{
//			//printf("a=%d %d %d\n",a,b,i);
//			b = p[b][i];//同一高度
//		}
//	}
//	//printf("%d %d\n",a,b);
//	if (a == b) return a;
//	for (int i = 20; i >= 0; i--)
//	{
//		if (p[a][i] != p[b][i])
//			a = p[a][i], b = p[b][i];
//	}
//	//printf("a=%d\n",a);
//	return p[a][0];
//}
//void search(int a)
//{
//	for (int i = head[a]; i; i = edge[i].next)
//	{
//		int v = edge[i].to;
//		if (v == p[a][0]) continue;
//		search(v);
//		sum[a] += sum[v];//子树和
//	}
//}
//int main()
//{
//	scanf("%d", &n);
//	for (int i = 1; i <= n; i++)
//		scanf("%d", &a[i]);
//	for (int i = 1; i <= n - 1; i++)
//	{
//		int u, v;
//		scanf("%d%d", &u, &v);
//		add(u, v); 
//		add(v, u);
//	}
//	dfs(1, 0);//预处理倍增数组
//	for (int i = 1; i <= n - 1; i++)
//	{
//		int x = a[i], y = a[i + 1], LCA = lca(x, y);//表示从x走到y的路径
//		//printf("lca=%d\n",LCA);
//		sum[x]++;
//		sum[y]++;
//		sum[LCA]--;
//		sum[p[LCA][0]]--;
//	}//点差分核心代码：起点终点++，公共祖先及祖先父亲--
//	search(1);//求出各点子树和
//	for (int i = 2; i <= n; i++)
//		sum[a[i]]--;//每次计算新的路径，除了第一个路径的起点，其他路径的起点和上一次路径的终点都重复计算了一次，应该--
//	for (int i = 1; i <= n; i++)
//		printf("%d\n", sum[i]);
//	system("pause");
//	return 0;
//}


//题目大意：给出一棵树，n(n<=5w)个节点，k(k<=10w)次修改，每次给定s和t，把s到t的路径上的点权+1，问k次操作后最大点权。
//FJ给他的牛棚的N(2≤N≤50,000)个隔间之间安装了N-1根管道，隔间编号从1到N。所有隔间都被管道连通了。
//FJ有K(1≤K≤100, 000)条运输牛奶的路线，第i条路线从隔间si运输到隔间ti。
//一条运输路线会给它的两个端点处的隔间以及中间途径的所有隔间带来一个单位的运输压力，你需要计算压力最大的隔间的压力是多少。
//对于每次修改，给s和t的点权 + 1，给lca(s, t)和lca(s, t)的父亲的点权 - 1，每一个点的权就是它与它的子树权和
//The first line of the input contains N and K
//The next N - 1 lines each contain two integers x and y（x≠y) describing a pipe between stalls x and y
//The next K lines each contain two integers s and tdescribing the endpoint
//stalls of a path through which milk is being pumped.
//ouput:An integer specifying the maximum amount of milk pumped through any stall in the barn.
//输入：
//5 10
//3 4
//1 5
//4 2
//5 4
//5 4
//5 4
//3 5
//4 3
//4 3
//1 3
//3 5
//5 4
//1 5
//3 4
//
//输出：
//9
//#pragma warning(disable:4996)
//#include<iostream>
//#include<cstdio>
//#include<cstring>
//#include<algorithm>
//#define fsb(a,b,c) for (int a=b;a<=c;a++)
//#define maxn 50100
//#define maxm 100100
//#define mem(a,b) memset(a,b,sizeof(a))
//#define max(a,b) ((a)>(b)?(a):(b))
//using namespace std;
//
//struct edge 
//{
//	int v, next;
//}a[maxn * 2];
//int n, m, x, y, head[maxn], cnt = 0, cntb = 0, headb[maxn], fa[maxn], vis[maxn], dlt[maxn], faa[maxn], maxx = 0;
//
//struct qus 
//{
//	int v, next, mark;
//}b[maxm * 2];
//
//struct ablca 
//{
//	int x, y, z;
//}ans[maxm];
//
//inline void adda(int x, int y)//链式前向星加边
//{
//	a[++cnt].v = y;
//	a[cnt].next = head[x]; 
//	head[x] = cnt;
//}
//
//inline void addb(int x, int y, int z) 
//{
//	b[++cntb].v = y; b[cntb].mark = z; b[cntb].next = headb[x]; headb[x] = cntb;
//}
//
//inline int getf(int x) {
//	return x == fa[x] ? x : fa[x] = getf(fa[x]);
//}
//inline void dfs1(int u, int f) 
//{//tarjan求lca
//	faa[u] = f;
//	for (int t = headb[u]; t != -1; t = b[t].next) {
//		int v = b[t].v;
//		if (vis[v]) ans[b[t].mark].z = getf(v);
//	}
//	// printf("%d ",u);
//	vis[u] = 1;
//	for (int t = head[u]; t != -1; t = a[t].next) {
//		int v = a[t].v; if (v == f) continue;
//		dfs1(v, u);
//		int fau = getf(u), fav = getf(v);
//		if (fau != fav) fa[fav] = fau;
//	}
//}
//
//inline int dfs2(int u) {
//	int now = dlt[u], ans = 0;
//	for (int t = head[u]; t != -1; t = a[t].next) {
//		int v = a[t].v; if (v == faa[u]) continue;
//		now += dfs2(v);//now记录点u的修改后点权
//	}
//	maxx = max(maxx, now);
//	return now;
//}
//int main() {
//	// freopen("std.in","r",stdin);
//	scanf("%d%d", &n, &m);
//	mem(head, 255);
//	fsb(i, 1, n - 1) 
//	{
//		scanf("%d%d", &x, &y);
//		adda(x, y);
//		adda(y, x);
//	}
//	mem(headb, 255);
//	fsb(i, 1, m) 
//	{
//		scanf("%d%d", &ans[i].x, &ans[i].y);
//		addb(ans[i].x, ans[i].y, i); 
//		addb(ans[i].y, ans[i].x, i);
//	}
//	fsb(i, 1, n) fa[i] = i; mem(vis, 0);
//	dfs1(1, 0); mem(dlt, 0);
//	fsb(i, 1, m) 
//	{
//		dlt[ans[i].x]++; 
//		dlt[ans[i].y]++; 
//		dlt[ans[i].z]--; 
//		dlt[faa[ans[i].z]]--;
//	}
//	// printf("!!!\n");
//	n = dfs2(1);
//	printf("%d\n", maxx);
//	return 0;
//}


//边差分：找所有路径共同覆盖的边
//公元2044年，人类进入了宇宙纪元。
//L 国有n个星球，还有n-1条双向航道，每条航道建立在两个星球之间，这n-1条航道连通了L国的所有星球。
//小 P 掌管一家物流公司， 该公司有很多个运输计划，每个运输计划形如：有一艘物流飞船需要从ui号星球沿最快的宇航路径飞行到vi号星球去。
//显然，飞船驶过一条航道是需要时间的，对于航道j, 任意飞船驶过它所花费的时间为tj, 并且任意两艘飞船之间不会产生任何干扰。
//为了鼓励科技创新，L国国王同意小P的物流公司参与L	国的航道建设，即允许小P把某一条航道改造成虫洞，飞船驶过虫洞不消耗时间。
//在虫洞的建设完成前小P的物流公司就预接了m个运输计划。在虫洞建设完成后，这m个运输计划会同时开始，所有飞船一起出发。当这m个运输计划都完成时，小p的物流公司的阶段性工作就完成了。
//如果小P 可以自由选择将哪一条航道改造成虫洞， 试求出小P的物流公司完成阶段性工作所需要的最短时间是多少？
//第一行包括两个正整数n,m, 表示L 国中星球的数量及小P公司预接的运输计划的数量，星球从1到n编号
//接下来n−1 行描述航道的建设情况，其中第i 行包含三个整数ai,bi和ti，表示第i 条双向航道修建在ai与bi两个星球之间，任意飞船驶过它所花费的时间为ti。
//数据保证1≤ai, bi≤n 且0≤ti≤1000。
//接下来m 行描述运输计划的情况，其中第j 行包含两个正整数uj和vj，表示第j 个运输计划是从uj号星球飞往vj号星球。数据保证1≤ui, vi≤n
//输出：一个整数，表示小p的物流公司完成阶段性工作所需要的最短时间。
//
//输入：
//6 3
//1 2 3
//1 6 4
//3 1 7
//4 3 6
//3 5 5
//3 6
//2 5
//4 5
//输出：
//11

#pragma warning(disable:4996)
#include<algorithm>
#include<iostream>
#include<stdio.h>
#include<cstring>
using namespace std;

struct node 
{
	int next, to, val;
}edge[600010];

struct truck 
{
	int s, t, lca;
}; truck node[300010];

int n, m, p, flag, cnt, maxn;
int head[300010], deep[300010], f[300010][25], dis[300010], pre[300010], sum[300010];

void change(int &a, int &b)
{
	int t = a; a = b; b = t;
}

void add(int a, int b, int c)//链式前向星加边
{
	edge[++p].to = b;
	edge[p].next = head[a];
	edge[p].val = c;
	head[a] = p;
}

void dfs(int a, int fa)//预处理倍增数组
{
	deep[a] = deep[fa] + 1;
	f[a][0] = fa;
	for (int i = 1; i <= 20; i++)
		f[a][i] = f[f[a][i - 1]][i - 1];
	for (int i = head[a]; i; i = edge[i].next)
	{
		int v = edge[i].to;
		if (v == fa) continue;
		dis[v] = dis[a] + edge[i].val;
		pre[v] = edge[i].val;
		dfs(v, a);
	}
}

int getlca(int a, int b)
{
	if (deep[a] > deep[b]) change(a, b);
	for (int i = 20; i >= 0; i--)
	{
		if (deep[a] <= deep[f[b][i]])
			b = f[b][i];
	}
	if (a == b) return a;
	for (int i = 20; i >= 0; i--)
		if (f[a][i] != f[b][i])
		{
			a = f[a][i];
			b = f[b][i];
		}
	return f[a][0];
}

int judge(int a, int fa, int cnt, int maxn)//子树求和
{
	int nsum = sum[a];
	for (int i = head[a]; i; i = edge[i].next)
	{
		int v = edge[i].to;
		if (v == fa) continue;
		nsum += judge(v, a, cnt, maxn);
	}//存在当前对应边数经过的次数应该大于所有超时的路径数！！！！！！！！！
	if (nsum >= cnt && pre[a] >= maxn) //a对应的边的时间大于不够的时间，变成虫洞后即可省掉这么多时间，返回真值
		flag = 1;
	return nsum;
}

int check(long long limit)//判断当前设的时间是否能完成所有任务
{
	memset(sum, 0, sizeof(sum));
	cnt = 0, flag = 0, maxn = 0;
	for (int i = 1; i <= m; i++)
		if (dis[node[i].s] + dis[node[i].t] - 2 * dis[node[i].lca] > limit)
		{
			sum[node[i].s]++, sum[node[i].t]++, sum[node[i].lca] -= 2;//边差分处理核心代码
			cnt++;
			maxn = max(maxn, dis[node[i].s] + dis[node[i].t] - 2 * dis[node[i].lca]);
		}
	if (cnt == 0) return 1;//时间太长，必定完成
	int wsb = judge(1, 0, cnt, maxn - limit);//判断不够的时间能否通过虫洞改变而使其在当前时间内完成任务
	return flag;
}

int main()
{
	scanf("%d%d", &n, &m);
	for (int i = 1; i <= n - 1; i++)
	{
		int a, b, c;
		scanf("%d%d%d", &a, &b, &c);
		add(a, b, c);
		add(b, a, c);
	}
	dfs(1, 0);
	for (int i = 1; i <= m; i++)
	{
		scanf("%d%d", &node[i].s, &node[i].t);
		node[i].lca = getlca(node[i].s, node[i].t);
	}
	long long l = 0, r = 3000000000;
	while (l + 1 < r)
	{
		int mid = (l + r) / 2;
		if (check(mid))
			r = mid;
		else
			l = mid;
	}
	if (check(l)) printf("%lld\n", l);
	else printf("%lld\n", r);
	return 0;
}
```