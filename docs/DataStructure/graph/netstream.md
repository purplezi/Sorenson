### 网络流最大流
```
#include<iostream>
#include<stdio.h>
#include<cstring>
#include<string>
#include<vector>
#include<math.h>
#include<queue>
#include<set>
#include<algorithm>
#include<map>
#define MS(x) memset(x,0,sizeof(x));
#define MS1(x) memset(x,-1,sizeof(x));
using namespace std;
const int maxN = 200;//点数
const int maxM = 1e6;//边数必须得是给定边数的两倍 因为还有反向边

const int inf = 0x3f3f3f3f;


class Graph
{
	//先Graph G 生成一个类
	//然后G.init(n) n是点的个数
	//然后addedge加边
	//记得设置源点汇点 set_st(start,end);
	//最后直接跑Dinic() 返回的就是最大流
private:
	int s, t;//源点和汇点
	int n;//点的个数
	int cnt;//前向星
	int Head[maxN];//每个点最后一条边的编号
	int Next[maxM];//指向对应点的前一条边
	int W[maxM];//每一条边的残量
	int V[maxM];//每一条边指向的点
	int Depth[maxN];//分层图的深度
	int cur[maxN];//cur就是记录当前点u循环到了哪一条边
public:
	void init(int n)//源点 汇点 点的个数
	{
		cnt = -1;
		memset(Head, -1, sizeof(Head));
		memset(Next, -1, sizeof(Next));
		this->n = n;
	}
	void set_st(int source, int terminal)//设置源点 汇点
	{
		s = source;
		t = terminal;
	}
	void _Add(int u, int v, int w)//前向星
	{
		cnt++;
		Next[cnt] = Head[u];
		Head[u] = cnt;
		V[cnt] = v;
		W[cnt] = w;
	}
	void Add_Edge(int u, int v, int w)//前向星
	{
		_Add(u, v, w);
		_Add(v, u, 0);
	}
	int dfs(int u, int flow)//u是当前节点,flow是当前流量
	{
		if (u == t)//达到汇点 直接返回
			return flow;
		for (int& i = cur[u]; i != -1; i = Next[i])//注意这里的&符号，这样i增加的同时也能改变cur[u]的值，达到记录当前弧的目的
		{
			if ((Depth[V[i]] == Depth[u] + 1) && (W[i] != 0))//如果满足分层图条件并且参量不为0
			{
				int di = dfs(V[i], min(flow, W[i]));//向下增广
				if (di > 0)//增广成功
				{
					W[i] -= di;//正向边减
					W[i ^ 1] += di;//反向边加
					return di;//返回流量
				}
			}
		}
		return 0;//增广失败 返回0
	}
	bool bfs()
	{
		queue<int> Q;
		while (!Q.empty()) Q.pop();
		MS(Depth);
		Depth[s] = 1;//源点的深度为1
		Q.push(s);
		while (!Q.empty())
		{
			int u = Q.front();
			Q.pop();
			for (int i = Head[u]; i != -1; i = Next[i])
				if ((Depth[V[i]] == 0) && (W[i] > 0))//深度等于0并且残量大于0
				{
					Depth[V[i]] = Depth[u] + 1;//它就是下一层
					Q.push(V[i]);
				}
		}
		if (!Depth[t]) return 0;//如果汇点的深度为0 也就是不存在增广路
		return 1;//汇点深度不为0 存在增广路
	}
	int Dinic()
	{
		int Ans = 0;//纪录最大流量
		while (bfs())//存在增广路
		{
			for (int i = 1; i <= n; i++)//当前弧优化
				cur[i] = Head[i];
			while (int d = dfs(s, inf))//源点汇入inf的流量 当还能增广时 一直增加流量
			{
				Ans += d;
			}
		}
		return Ans;//返回流量
	}
}G;
```

### 最小费用最大流1
```
#pragma warning(disable:4996)
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;
const int maxn = 1e4 + 10;
const int maxm = 1e5 + 10;
const int maxt = 2139062143;
inline int min_(int x, int y) { return x < y ? x : y; }
int n, m, s, t, nflow, nfee, flow, fee;
int a, b, c, d;
int h[maxn], hs = 1;
int e_q[maxm], e_z[maxm], e_n[maxm], e_w[maxm], e_f[maxm];
void add(int q, int z, int k, int w, int f) { e_q[k] = q, e_z[k] = z, e_n[k] = h[q], e_w[k] = w, e_f[k] = f, h[q] = k; }
int q[maxm], head, tail;
int w[maxn], p[maxn];
bool v[maxn];
int ap(int k, int v) {
	if (k == s) return v;
	int ret = ap(e_q[p[k]], min_(v, e_w[p[k]]));
	if (!e_f[p[k] ^ 1]) add(k, e_q[p[k]], p[k] ^ 1, 0, -e_f[p[k]]);
	e_w[p[k]] -= ret, e_w[p[k] ^ 1] += ret;
	return ret;
}
void Mcmf() {
	while (1) {
		memset(w, 0x7f, sizeof(w));
		memset(v, 0, sizeof(v));
		head = tail = w[s] = 0;
		q[head++] = s, v[s] = 1;
		while (head > tail) {
			a = q[tail++], v[a] = 0;
			for (int i = h[a]; i; i = e_n[i])
				if (0ll + e_f[i] + w[a] < w[e_z[i]] && e_w[i]) {
					p[e_z[i]] = i;
					w[e_z[i]] = e_f[i] + w[a];
					if (!v[e_z[i]]) q[head++] = e_z[i], v[e_z[i]] = 1;
				}
		}
		if (w[t] == maxt) break;
		nflow = ap(t, maxt);
		flow += nflow;
		fee += nflow * w[t];
	}
}


```

### 最小费用最大流2
```
#include <iostream>
#include<bits/stdc++.h>
using namespace std;
#define INF 0x3f3f3f3f
const int maxn = 5000 + 7;
/**********邻接表数组形式***********/
struct Edge {/***表边**/
	int from, to;/****起点终点***/
	int next;/*****相同起点 的下一条边***/
	int cap, flow;/*****该边 的 容量 和流量***/
	int cost;/****花费**/
}edge[maxn * 20];
int head[maxn];/****表头***/
int pre[maxn];
int dist[maxn], n, m, tot, s, t;
bool vis[maxn];

/***加边函数********/
void addEdge(int a, int b, int c, int cost) {//注意反向弧的负权值
	edge[tot].from = a; edge[tot].to = b;/*****a到 b****/
	edge[tot].next = head[a];/*****他的下一条边 是 之前最后一条 以 a为起点的边******/
	edge[tot].cap = c; edge[tot].flow = 0;/*******容量 流量 流量初始化 为 0*******/
	edge[tot].cost = cost; head[a] = tot++;/***他成为 目前 以a 为起点的最后一条边****/

	/*****反向边的建立 反向边的容量 初始化为 0******/
	edge[tot].from = b; edge[tot].to = a; edge[tot].next = head[b]; edge[tot].cap = 0; edge[tot].flow = 0; edge[tot].cost = -cost; head[b] = tot++;
}
bool SPFA() {//SPFA求增广路，dist[t]保存最小花费  ，
	memset(dist, INF, sizeof(dist));
	memset(vis, 0, sizeof(vis));
	queue<int> que;
	dist[s] = 0;
	vis[s] = 1;
	que.push(s);
	while (!que.empty()) {
		int u = que.front();
		que.pop();
		vis[u] = 0;
		for (int i = head[u]; i != -1/**可以 用～i*/; i = edge[i].next) {
			int v = edge[i].to;
			if (edge[i].cap > edge[i].flow && dist[v] > dist[u] + edge[i].cost) {
				dist[v] = dist[u] + edge[i].cost;
				pre[v] = i;
				if (!vis[v]) {
					vis[v] = 1;
					que.push(v);
				}
			}
		}
	}
	if (dist[t] != INF)return true;
	return false;
}

int CostFlow(int &flow) {//EK算法
	int mincost = 0;
	while (SPFA()) {//能找到增广路
		int Min = INF;
		for (int i = t; i != s; i = edge[pre[i]].from)
		{//寻找最小流
			Min = min(Min, edge[pre[i]].cap - edge[pre[i]].flow);
		}
		for (int i = t; i != s; i = edge[pre[i]].from) {//处理所有边
			edge[pre[i]].flow += Min;
			edge[pre[i] ^ 1].flow -= Min;
		}
		flow += Min;
		mincost += (dist[t] * Min);//累和最小花费
	}
	return mincost;
}
int main()
{

	int T;
	scanf("%d", &T);
	while (T--) {
		tot = 0;
		memset(head, -1, sizeof(head));
		scanf("%d%d%d%d", &n, &m, &s, &t);
		for (int i = 0; i < m; i++) {
			int a, b, c, cost;
			scanf("%d%d%d%d", &a, &b, &c, &cost);
			addEdge(a, b, c, cost);
		}
		int MaxFlow = 0;
		int MinCost = CostFlow(MaxFlow);
		printf("%d %d\n", MaxFlow, MinCost);
	}
	return 0;
}
```

### 最小割
给定一个网络G=(V,E),源点为S,汇点为T,若一个边集E'被删掉后，源点S和汇点T不再连通，则称该边集为网络的割。边的容量之和最小的割称为网络的最小割。
* 最大流最小割定理：任何一个网络的最大流量等于最小割中边的容量之和

eg.给定一张无向图，求最少去掉多少个点，可以使图不连通。点数N<=50
若无向图不连通，则图中必有两个点不连通，但这两个点是未知的。可以枚举这两个点S和T，然后在剩余的N-2个节点中最少去掉多少个，可以使S和T不连通（S和T不是直接相连的）
构建点边转换网络：
(1)把原来无向图的每个点x，拆成x和x'=x+N两个点
(2)对$\forall x \neq S,x \neq T$,连有向边（x,x')，容量为1
(3)对于原无向图的每条边(x,y)，在网路中连有向边(x',y)，(y',x)，容量为$\infty$
(4)以S'为网络的源点，T为网络的汇点，求最小割（最大流），即可知道最少需要获得的点数

若求全局最小割，则需要枚举源汇点来跑网络流，时间复杂度较高，一般使用stoer-wagner算法
```
//poj2914

/*

translation:

	给出一副无源汇的无向图，求其最小割。

solution:

	原本的想法是枚举源汇，然后DINIC最大流。然而这样必然超时。所以必须有一种办法能够快速求出

	全局最小割，这种算法就是stoer-wagner算法。
	
*/

#include <iostream>

#include <cstdio>

#include <cstring>

 

using namespace std;

const int maxn = 500 + 5;

const int INF = 0x3f3f3f3f;

 

int wage[maxn], n, m;

bool in_set[maxn], vis[maxn];

int G[maxn][maxn];

 

int find_min_cut(int& S, int& T)

{

	memset(wage, 0, sizeof(wage));

	memset(vis, 0, sizeof(vis));

 

	S = T = -1;

	int min_cut, u = 0;

	for(int i = 0; i < n; i++) {

		int mx = -INF;

		for(int j = 0; j < n; j++) {

			if(!in_set[j] && !vis[j] && mx < wage[j]) {

				mx = wage[j];

				u = j;

			}

		}

		if(u == T)	return min_cut;

		S = T;	T = u;

		min_cut = mx;

		vis[u] = true;

		for(int v = 0; v < n; v++) {

			if(!in_set[v] && !vis[v]) {

				wage[v] += G[u][v];

			}

		}

	}

	return min_cut;

}

 

int stoer_wagner()

{

	int S, T;

	int ans = INF, min_cut;

	for(int i = 1; i < n; i++) {

		min_cut = find_min_cut(S, T);

		ans = min(ans, min_cut);

		if(ans == 0)	return ans;

 

		in_set[T] = true;

		for(int j = 0; j < n; j++) {

			if(!in_set[j] && j != S) {

				G[S][j] += G[T][j];

				G[j][S] += G[j][T];		//缩点操作，将T节点的信息附加到S上面

			}

		}

	}

	return ans;

}

 

int main()

{

	//freopen("in.txt", "r", stdin);

    while(~scanf("%d%d", &n, &m)) {

		memset(in_set, 0, sizeof(in_set));

		memset(G, 0, sizeof(G));

 

		int u, v, c;

		for(int i = 0; i < m; i++) {

			scanf("%d%d%d", &u, &v, &c);

			G[u][v] += c;

			G[v][u] += c;

		}

 

		printf("%d\n", stoer_wagner());

    }

    return 0;

}

```

### 最大权闭合子图
定义：有一个有向图，每一个点都有一个权值(正负皆可),选择一个权值和最大的子图，**使得每个点的后继都在子图里面**，这个子图就叫最大权闭合子图
解法：将其转化为最小割问题
(1)从源点s向每个正权点连一条容量为权值的边，每个负权点向汇点t连一条容量为权值绝对值的边，有向图原来的边容量全部为无限
(2)求它的最小割，割掉后，与源点s连通的点构成最大权闭合子图，权值为（正权值之和-最小割）
由于原图的边都是无穷大，那么割边一定是与源点s或汇点t相连的。
割掉s与i的边，表示不选择i点作为子图的点； 
割掉i与t的边，表示选择i点为子图的点。
如果s与i有边，表示i存在子图中； 
如果i与t有边，表示i不存在于子图中。

```
#include <bits/stdc++.h>
using namespace std;

typedef struct Edge {
       int u, v, w, next;
}Edge;
   
 const int inf = 0x7f7f7f7f;
 const int maxn = 500;
  
 int cnt, dhead[maxn];
 int cur[maxn], dd[maxn];
 Edge dedge[maxn*maxn];
 int S, T, N;
 int n, m;
  
 void init() {
     memset(dhead, -1, sizeof(dhead));
     for(int i = 0; i < maxn; i++) dedge[i].next = -1;
     cnt = 0;
 }
  
 void adde(int u, int v, int w, int c1) {
     dedge[cnt].u = u; dedge[cnt].v = v; dedge[cnt].w = w; 
     dedge[cnt].next = dhead[u]; dhead[u] = cnt++;
     dedge[cnt].u = v; dedge[cnt].v = u; dedge[cnt].w = c1; 
     dedge[cnt].next = dhead[v]; dhead[v] = cnt++;
 }
  
 bool bfs(int s, int t, int n) {
     queue<int> q;
     for(int i = 0; i < n; i++) dd[i] = inf;
     dd[s] = 0;
     q.push(s);
     while(!q.empty()) {
         int u = q.front(); q.pop();
         for(int i = dhead[u]; ~i; i = dedge[i].next) {
             if(dd[dedge[i].v] > dd[u] + 1 && dedge[i].w > 0) {
                 dd[dedge[i].v] = dd[u] + 1;
                 if(dedge[i].v == t) return 1;
                 q.push(dedge[i].v);
             }
         }
     }
     return 0;
 }
  
 int dinic(int s, int t, int n) {
     int st[maxn], top;
     int u;
     int flow = 0;
     while(bfs(s, t, n)) {
         for(int i = 0; i < n; i++) cur[i] = dhead[i];
         u = s; top = 0;
         while(cur[s] != -1)
		  {
             if(u == t) {
                 int tp = inf;
                 for(int i = top - 1; i >= 0; i--) {
                     tp = min(tp, dedge[st[i]].w);
                 }
                 flow += tp;
                 for(int i = top - 1; i >= 0; i--) {
                     dedge[st[i]].w -= tp;
                     dedge[st[i] ^ 1].w += tp;
                     if(dedge[st[i]].w == 0) top = i;
                 }
                 u = dedge[st[top]].u;
             }
             else if(cur[u] != -1 && dedge[cur[u]].w > 0 && dd[u] + 1 == dd[dedge[cur[u]].v]) 
			 {
                 st[top++] = cur[u];
                u = dedge[cur[u]].v;
             }
             else {
                    while(u != s && cur[u] == -1) {
                        u = dedge[st[--top]].u;
                    }
                    cur[u] = dedge[cur[u]].next;
                  }
           }
       }
    return flow;
 }
 
 int main() {
    // freopen("in", "r", stdin);
     int k, u, v, w;
     while(~scanf("%d %d", &n, &m)) {
         init(); S = 0; T = n + m + 1; N = T + 1;
         int pos = 0;
         for(int i = 1; i <= m; i++) {
             scanf("%d", &w);
             if(w != 0) adde(n+i, T, w, 0);
             else adde(n+i, T, inf, 0);
         }
         for(int i = 1; i <= n; i++) {
             scanf("%d %d", &w, &k);
             pos += w;
             if(w != 0) adde(S, i, w, 0);
            else adde(S, i, inf, 0);
            for(int j = 0; j < k; j++) {
                scanf("%d", &v);
                adde(i, n+v, inf, 0);
            }
        }
        printf("%d\n", pos - dinic(S,T,N));
    }
}
```