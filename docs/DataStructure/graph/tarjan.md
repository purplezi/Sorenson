## Tarjan算法

核心：dfn时间戳序和low最早时数组的维护
应用：
* 求解有向图的强连通分量
* 求解无向图的割点和桥
* 求解无向图的双连通分量
* 离线求解lca(补充lca的其他算法)
**tarjan基于dfs算法，故需要链式前向星储存图的直接拓扑关系**

### 1.有向图的强连通分量
强连通：如果两个顶点可以相互通达，则称两个顶点强连通。如果有向图G的每两个顶点都强连通，则G是个强连通图。非强连通图有向图的极大连通子图，称为强连通分量。
```c++
struct Edge
{
    int from,to,weight,next;
}edge[maxm];

int head[maxn],edgenum,dfn[maxn],low[maxn],stack[maxn],vis[maxn],num[maxn],cnt,t,tarjanpoint;

void init()
{
    memset(head,-1,sizeof(head));
    edgenum=0;
}

void addedge(int u,int v,int d)
{
    Edge e;
    e.from=u;
    e.to=v;
    e.weight=d;
    e.next=head[u];
    edge[edgenum]=e;
    head[u]=edgenum++;
}

void tarjan(int now)
{
    dfn[now]=low[now]=++tim;//时间戳序从1开始
    stack[++top]=now;//栈数组，通过t模拟出入栈操作
    vis[now]=1;//标记是否入栈
    for(int i=head[now];i!=-1;i=edge[i].next)
    {
        if(!dfn[edge[i].to])//未被搜索过
        {
            tarjan(edge[i].to);
            low[now]=min(low[now],low[edge[i].to]);//回溯更新最早值
        }
        else//已经搜索过
        {
            if(vis[edge[i].to])//在栈内
            {
                low[now]=min(low[now],dfn[edge[i].to]);//注意是用dfn更新最早值，防止该点在另一个强连通子图中而没出栈，若用low怕走不到
            }
        }
    }
    if(dfn[now]==low[now])//连通量根节点
    {
        int cur;
        tarjanpoint++;//缩点记录
        do
        {
            cur=stack[top--];
            vis[cur]=0;
            belong[cur]=tarjanpoint;//缩点归属数组
            num[tarjanpoint]++;
        }
        while(now!=cur && top);
    }      
}

//图不一定是全连通图，则需要逐点tarjan
for(int i=1;i<=n;i++)
{
    if(!dfn[i])
    {
        tarjan(i);
    }
}
```

tarjan对有向图的缩点算法有很大用处：
（1）缩点后称为无环（无强连通分量）图，可以通过新图的各点度数求解各种约束加减边的操作(例如要加多少条边才能使这个dag图连通:设入度为0的点的数量为n，出度为0的点的数量为m，那需要加的边数就是max（n，m）因为要将入度为0的点和出度为0的点连接起来。)
（2）缩点还可以解决2-SAT（矛盾）问题（缩点+拓扑染色检戳）
一个牧师要给n个婚礼进行一个仪式
给定婚礼开始的时间和结束的时间
仪式要么在婚礼刚刚开始的时候进行，要么进行完婚礼正好结束
问牧师能否参加所有的婚礼
并输出方案
Input
The first line contains a integer N(1 ≤ N ≤ 1000).
The next N lines contain the Si, Ti and Di.Si and Ti are in the format of hh : mm.
Output
The first line of output contains "YES" or "NO" indicating whether John can be present at every special ceremony.If it is "YES", output another N lines describing the staring time and finishing time of all the ceremonies.
Sample Input
2
08 : 00 09 : 00 30
08 : 15 09 : 00 20

Sample Output
YES
08 : 00 08 : 30
08 : 40 09 : 00
```
#include<iostream>
#include<cstdio>
#include<cstring>
#include<cstdlib>
#include<cmath>
#include<vector>
#include<queue>
#include<algorithm>
using namespace std;

const int MAX = 10500;

int Col[MAX], In[MAX], len[MAX], Opp[MAX];
int s[MAX], G[MAX], dfn[MAX], low[MAX];
int group = 0, top = 0, tim = 0;
bool vis[MAX];
int n;
vector<int> E[MAX];//超级点就用vector来存边,方便些 

struct Time
{
	int b, e;//b表示开始时间，e表示结束时间 
}t[MAX];
struct Edge
{
	int from,to,weigh,next;
}edge[MAX*MAX];//跑Tarjan时用来存边的链式前向星

int h[MAX], cnt = 0;//链式前向星存储 

void Add(int u, int v);//连边，从u->v
void Link();//连边 
bool Wrong(int i, int j);//检查两个时间是否矛盾 
void Tarjan(int u);//缩点 
void Top();//拓扑排序 



inline void Add(int u, int v)//连边，从u->v
{
	Edeg e;
	e.from=u;
	e.to=v;
	e.next=head[u];
	edge[cnt]=e;
	head[u]=cnt++;
}

bool Wrong(int i, int j)//判断两个时间是否矛盾 
{
	if (t[i].b >= t[j].e || t[i].e <= t[j].b)
		return false;
	else
		return true;
	//只要某一个结束时间在另一个的开始时间之前则无矛盾
	//反之必定存在矛盾 
}

void Link()//连边 
{
	for (int i = 1; i <= n; ++i)
		for (int j = 1; j <= n; ++j)//枚举任意两组仪式的时间，查看是否存在矛盾
		{
			if (i == j)continue;//自己和自己没有比较的意义 
			if (Wrong(i, j))//开头和开头矛盾
				Add(i, j + n);
			if (Wrong(i, j + n))//开头和结尾矛盾 
				Add(i, j);
			if (Wrong(i + n, j))//结尾和开头矛盾 
				Add(i + n, j + n);
			if (Wrong(i + n, j + n))//结尾和结尾矛盾 
				Add(i + n, j);
		}
}

void Tarjan(int u)//缩点 
{
	dfn[u] = low[u] = ++tim;
	s[++top] = u;
	vis[u] = true;
	int v;
	for (int i = h[u]; i != -1; i = edge[i].next)
	{
		v = e[i].v;
		if (!dfn[v])
		{
			Tarjan(v);
			low[u] = min(low[u], low[v]);
		}
		else
			if (vis[v])
				low[u] = min(low[u], dfn[v]);
	}
	if (low[u] == dfn[u])
	{
		++tarjanpoint;
		do
		{
			v = s[top--];
			vis[v] = false;
			belong[v] = tarjanpoint;
		} while (v != u && top);
	}
}

void Top()//拓扑排序,拓扑检戳 （对缩完点的图的工作，即针对超级点）
{
	memset(In, 0, sizeof(In));//存入度 
	memset(Col, -1, sizeof(Col));//存颜色
	for (int i = 1; i <= 2 * n; ++i)//连接超级点建新图
	{
		for (int j = h[i]; j != -1; j = e[j].next)//枚举邻边 
		{
			int v = edge[j].v;
			if (belong[i] == belong[v])continue;//在同一个强连通分量中无需考虑 
			E[belong[v]].push_back(belong[i]);//边要反着存 （反向染色法）
			In[belong[i]]++;//统计入度 （反向叶子或孤立点入度最小）
		}
	}
	queue<int> Q;//拓扑排序的队列 
	for (int i = 1; i <= tarjanpoint; ++i)
		if (In[i] == 0)Q.push(i);//入度为0的进队 
	while (!Q.empty())
	{
		int v = Q.front();
		Q.pop();
		if (Col[v] == -1)//未染色
		{
			Col[v] = 1;     //染为1 
			Col[Opp[v]] = 0;//对立点染为0 
		}
		for (int i = 0; i < E[v].size(); ++i)//依次减入度 
		{
			In[E[v][i]]--;
			if (In[E[v][i]] == 0)//入度为0 
				Q.push(E[v][i]);
		}
	}
}

int main()
{
	while (cin >> n)
	{
		memset(h, -1, sizeof(h));
		cnt = 0; tim = group = top = 0;
		memset(dfn, 0, sizeof(dfn));
		memset(low, 0, sizeof(low));
		memset(In, 0, sizeof(In));
		memset(vis, 0, sizeof(vis));
		memset(Opp, 0, sizeof(Opp));
		for (int i = 1; i <= n; ++i)
		{
			int a, b, c, d, e;
			scanf("%d:%d %d:%d %d", &a, &b, &c, &d, &len[i]);
			//这里都把时间转化为分钟来计算 
			t[i].b = a * 60 + b;
			t[i + n].e = c * 60 + d;
			t[i].e = t[i].b + len[i];//计算开头的结束时间 
			t[i + n].b = t[i + n].e - len[i];//计算结尾的开始时间 
			//i   表示第i个婚礼的仪式在开始的时候举行
			//i+m 表示第i个婚礼的仪式在结束的时候举行 
		}

		Link();//连边 

		for (int i = 1; i <= 2 * n; ++i)//缩点 
			if (!dfn[i])Tarjan(i);

		for (int i = 1; i <= n; ++i)//判断是否有解 
		{
			if (belong[i] == belong[i + n])//某个婚礼的 开始/结束 在同一个强连通分量中，无解 
			{
				cout << "NO" << endl;
				return 0;
			}
			Opp[belong[i]] = belong[i + n];//存一下超级点的的对立点 
			Opp[belong[i + n]] = belong[i];//反向也要存 
		}

		cout << "YES" << endl;//不存在矛盾，则有解 

		Top();//Top排序 

		for (int i = 1; i <= n; ++i)
		{
			if (Col[beong[i]] == 1)//选择了再婚礼开始时 
				printf("%.2d:%.2d %.2d:%.2d\n", t[i].b / 60, t[i].b % 60, t[i].e / 60, t[i].e % 60);
			else //在婚礼结束时 
				printf("%.2d:%.2d %.2d:%.2d\n", t[i + n].b / 60, t[i + n].b % 60, t[i + n].e / 60, t[i + n].e % 60);
		}
	}

}
*
写在AC后面：
这几道2-sat题真是花式出错
这道的错误是缩点以后，并不是按照超级点来输出(明显会挂呀)
然而。。。查了1小时的错
感叹一句：对拍真是利器也。。。
*/
```
Tip:
(1)检戳往往是还原方案的重要措施：其中像LCS,LIS要输出方案时需要dp过程中采取逆向检戳（详见DP)，而图方案的问题则如上述进行拓扑检戳

### 2.无向图的割点与割边（桥）
**（1）割点**：
割点：在无向连通图中去掉这个点和所有这个点相连的边后，原先连通的块就会相互分离变成至少两个分离的连通块的点。
Tarjan算法求割点：
* 若u为树的根节点，只要u有两个及以上子节点，则u为割点
* 若u不为根节点，v不为u的父节点，当dfn[u]<=low[v]时，u为割点

由此观之，在无向图求割点的tarjan算法里,不需要栈数组
```
//链式前向星建边和各数组初始化与上述相同
void tarjan(int u)
{
	dfn[u]=low[u]=++tim;
	int child=0;
	for(int i=head[u];i!=-1;i=edge[i].next)
	{
		int v=edge[i].to;
		if(!dfn)
		{
			tarjan(v);
			low[u]=min(low[u],low[v]);
			if(u!=root && low[v]>=dfn[u])//非根节点情况
			{
				cut[u]=1;
			}
			if(u==root && ++child>=2)//根节点情况 
			{
				cut[u]=1;
			}
		}
		else
		{
			low[u]=min(low[u],dfn[v]);
		}
	}
}


//在main函数中注意的是双向建边和非连通图的逐点定根和逐根tarjan
int main()
{
	...
	add(x,y),add(y,x);
	...
	for(int i=1;i<=n;i++)
	{
		if(!dfn[i])
		{
			root=i;
			tarjan(i);
		}
	}
}
```
**（2）割边（桥）**
割边（桥）：在无向连通图中去掉这条边后，原先连通的块就会相互分离变成至少两个分离的连通块的边。
核心：倘若顶点u不能回到祖先，也没有另外一条路回到父亲，那么v-u就是割边
由于可能出现重边的情况，所以不能是记录父亲节点，而是改成记录入边的编号
在链式前向星的建图方式中，<x,y>和<y,x>是编号相邻的一对，不妨从2开始编号。一对边编号则有a^1=b,b^1=a
tarjan算法求割边：
当且仅当无向边（u,v)是树枝边的时候，需要满足dfn[u]<low[v]
**注意：没有等号！！！！！**
```
//链式前向星建边和各数组初始化与有一丁点区别
//初始化计边数ecnt=2;

void tarjan(int u,int ein)
{
	dfn[u]=low[u]=++tim;
	for(int i=head[u];i!=-1;i=edge[i].next)
	{
		int v=edge[i].to;
		if(!dfn[v])
		{
			tarjan(v,i);
			low[u]=min(low[u],low[v]);
			if(low[v]>dfn[u])
			{
				bridge[i]=bridge[i^1]=1;
			}
			else if(i!=(ein^1))
			{
				low[u]=min(low[u],dfn[v]);//相当于不用父亲节点来更新自己的low
			}
		}		
	}
}

//在main函数中同样逐根tarjan
```
### 3.无向图的双连通分量
双连通分量：对于一个无向图，其边/点连通度大于1，满足任意两点之间，能通过两条或两条以上没有任何重复边/点的路到达的图，即删掉任意边/点后，图仍是来连通的  
定理：
* 一张无向连通图是点双连通图当且仅当图的顶点数<=2 或 图中任意两点都同时包含在至少一个简单环中，即点双连通分量没有割点
* 一张无向连通图是边双连通图当且仅当任意一条边都包含在至少一个简单环中，即边双连通分量没有桥

**（1）点双连通分量**
核心：求割点
求点双连通分量可以在求割点的同时用栈维护
在搜索图时，每找到一条树枝边或后向边（非横叉边），就把这条边加入栈中。如果遇到满足dfn(u)<=low(v),说明u是一个割点，同时把边从栈顶一个个取出，直到遇到了边(u,v)，取出的这些边与其关联的点。就组成一个点双连通分支。
割点可以属于多个点双连通分支，其余点和每条边只属于且属于一个点双连通分支。
```
//在以上求割点的代码中多个维护栈，可以用数组，也可以直接用STL
int bcc_cnt;//记录点双连通分量个数
int bcc_belong[maxn];//记录点属于的双连通分量
vector<int> bcc[maxn];//记录某双连通分量的所有节点
...

void tarjan(int u)
{
	dfn[u]=low[u]=++tim;
	stk[++top]=u;
	if(u==root && head[u]==0)//判断孤立点
	{
		bcc[++bcc_cnt].push_back(u);
	}
	int child=0;
	for(int i=head[u];i!=-1;i=edge[i].next)
	{
		int v=edge[i].to;
		if(!dfn[v])
		{
			tarjan(v);
			low[u]=min(low[u],low[v]);
			if(low[v]>=dfn[u])
			{
				if(u!=root)
				{
					cut[u]=1;
				}
				if(u==root && ++child>=2)
				{
					cut[u]=1;
				}
				bcc_cnt++;
				bcc[bcc_cnt].clear();
				do
				{
					int z=stk[top--];
					bcc[bcc_cnt].push_back(z);
				}while(z!=v);
				bcc[bcc_cnt].push_back(u);//把该点加入此分量却不弹出栈，是为了回溯碰到下一个割点时再弹，这两个割点又能构成一个点双连通分量，所以说一个割点可能属于多个双连通分量
			}
		}
		else
		{
			low[u]=min(low[u],dfn[v]);
		}
	}
}
```
v-DCC缩点由于一个割点可能在很多个v-DCC中而更加麻烦，方法是：
假设图中有x个割点和y个v-DCC，就直接建一个（x+y)个点的新图
每一个v-DCC和割点都作为新图的节点所在。建完后让每个割点和包含它们的v-DCC连边
```
...
for(int i=1;i<=n;i++)
{
	if(cut[i])
	{
		new_id[i]=++tim;
	}
}
for(int i=1;i<=tim;i++)
{
	for(int j=0;j<bcc[i].size();j++)
	{
		int x=bcc[i][j];
		if(cut[x])
		{
			addedge_c(i,new_id[x]);//超级点的新加边函数
			addedge_c(new_id[x],i);
		}
		else
		{
			new_id[x]=i;
		}
	}
}
```
**（2）边双连通分量**
核心：求割边
求边双连通分量时，需要先求出桥，然后把桥全部去掉，原图变成多个连通块，此时每个连通块就是一个边双连通分量
```
//建图和各数组初始化同求桥代码

void tarjan(int u,int ein)
{
	dfn[u]=low[u]=++tim;
	for(int i=head[u];i!=-1;i=edge[i].next)
	{
		int v=edge[i].to;
		if(!dfn[v])
		{
			tarjan(v,i);
			low[u]=min(low[u],low[v]);
			if(low[v]>dfn[u])
			{
				bridge[i]=bridge[i^1]=1;
			}
		}
		else if(i!=(ein^1))
		{
			low[u]=min(low[u],dfn[v]);
		}
	}
}

void dfs(int u)
{
	dcc_belong[u]=dcc_cnt;
	dcc[dcc_cnt].push_back(u);
	for(int i=head[u];;i!=-1;i=edge[i].next)
	{
		int v=edge[i].to;
		if(dcc_belong[v] || bridge[i])
		{
			continue;
		}
		dfs(y);
	}
}

int main()
{
	...
	for(int i=1;i<=n;i++)
	{
		if(!dcc_belong(i))
		{
			++dcc_cnt;
			dfs(i); 
		}
	}
} 
```
e-DCC缩点:我们把每一个e-DCC都堪称让你过一个节点，把所有桥边（x,y）看成连接编号为dcc_belong[x]和dcc_belong[y]的两个e-DCC的边，这样我们就会得到一棵树或者森林（原图不连通）。
```
...
for(int i=2;i<=ecnt;i++)
{
	int x=edge[i^1].to,y=edge[i].to;
	if(dcc_belong[x]==dcc_belong[y])
	{
		continue;
	}
	addedge_c(dcc_belong[x],dcc_belong[y]);////超级点的新加边函数
}
...
```
### 4.离线求解LCA
tarjan离线求解LCA代码详见lca篇
求lca四种方法
* tarjan离线(O(n+q))
* 倍增(O(n+q)logn)
* RMQ(欧拉序和区间查询)(O(nlogn)预处理，O(1)查询)
* 树链剖分(如果只是简单地求lca不必使用树链剖分)