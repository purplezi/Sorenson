## 二分图
二分图：对于一个图G = （V, E），若能将其点集分为两个互不相交的两个子集X、Y，使得X∩Y = ∅，且对于G的边集V，若其所有边的顶点全部一侧属于X，一侧属于Y，则称图G为一个二分图。
二分图判定：无奇环，可以用黑白染色判断。
```
//判断图中是否有奇环，即判断是否为二分图

struct Edge
{
    int from, to, next, weigh;
} edge[10*maxn];

void addedge(int u,int v)
{
    Edge e;
    e.from=u;
    e.to=v;
    e.next=head[u];
    edge[enum]=e;
    head[u]=enum++;
}

int n,m,s,t,color[maxn];

int bfs()//bfs染色
{
    color[1]=0;
    queue<int>q;
    q.push(1);
    while(!q.empty()){
        int u=q.front();q.pop();
        for(int i=head[u];i!=-1;i=edge[i].next)
        {
            int v=edge[i].to;
            if(color[v]==-1)
            {
                color[v]=color[u]^1;/未染色则染反色
                q.push(v);
            }
            else if(color[v]==color[u])//已染色判断色异
                return 0;//只有奇环才会出现相邻同色的情况（有奇环，非二分图）
        }
    }
    return 1;
}

```
匹配： 对于一个二分图G的子图M, 若M的边集E的的任意两条边都不连接同一个顶点，则称M为G的一个匹配。
最大匹配：若二分图G的一个匹配M为其边数最多的匹配，则称M为G的最大匹配。
最大匹配的求法：
* 匈牙利算法；
* 网络流：源点到左侧点，左侧点到右侧点，右侧点到汇点间都建流量为1的边，最大流即为最大匹配。
```
//第二次世界大战时期，英国皇家空军从沦陷国征募了大量外籍飞行员。
//由皇家空军派出的每一架飞机都需要配备在航行技能和语言上能互相配合的2名飞行员，其中1名是英国飞行员，另1名是外籍飞行员。
//在众多的飞行员中，每一名外籍飞行员都可以与其他若干名英国飞行员很好地配合。如何选择配对飞行的飞行员才能使一次派出最多的飞机。
//对于给定的外籍飞行员与英国飞行员的配合情况，试设计一个算法找出最佳飞行员配对方案，使皇家空军一次能派出最多的飞机 。
//对于给定的外籍飞行员与英国飞行员的配合情况，编程找出一个最佳飞行员配对方案，使皇家空军一次能派出最多的飞机。 
//Input
//第1行有2个正整数 m 和 n。n 是皇家空军的飞行 员总数(n < 100); 
//m 是外籍飞行员数。
//外籍飞行员编号为 1~m; 
//英国飞行员编号为 m + 1~n。
//接下来每行有 2 个正整数 i 和 j，表示外籍飞行员 i 可以和英国飞行员 j 配合。
//输入最后以 2 个 - 1 结束。
//Output第 1 行是最佳飞行 员配对方案一次能派出的最多的飞机数 M。
//如果所求的最佳飞行员配对方案不存在，则输出‘No Solution!’。
//
//Sample Input
//
//    5 10
//	1 7
//	1 8
//	2 6
//	2 9
//	2 10
//	3 7
//	3 8
//	4 7
//	4 8
//	5 10
//	- 1 - 1
//
//Sample Output
//	4
//匈牙利算法
#pragma warning(disable:4996)
#include<iostream>
#include<cstdio>
#include<cstring>
#include<vector>
using namespace std;
const int N=105;
int n,m;
int link[N];            //link[y]=x,即y与x匹配
bool vis[N];
vector<int>v[N];

bool can(int u)
{    
	for(int i=0;i<v[u].size();i++)
	{        
		int t=v[u][i];        
		if(!vis[t])
		{            
			vis[t]=true;            
			if(link[t]==-1||can(link[t]))
			{ //如果t尚未被匹配，或者link[t]即x可以找到其他能够替代的点,则把t点让给u匹配                
				link[t]=u;                
				return true;            
			}        
		}    
	}    
	return false;
}

int max_match()
{    
	memset(link,-1,sizeof(link));    
	int ans=0;    
	for(int i=1;i<=n;i++)
	{        
		memset(vis,false,sizeof(vis));        
		if(can(i))            
			ans++;    
	}    
	return ans;
}

int main()
{    
	scanf("%d%d",&n,&m);    
	int a,b;    
	while(~scanf("%d%d",&a,&b))
	{        
		if(a==-1&&b==-1)            
			break;        
		v[a].push_back(b);    
	}    
	int t=max_match();    
	if(t==0)        
		puts("No Solution!");    
	else        
		printf("%d\n",t);    
	system("pause");
	return 0;
}
```


其它性质

最小点覆盖集：点覆盖集是指图中的一个点集，且图中所有的边都至少有一个端点在点集中。最小点覆盖集即为点数最小的点覆盖集。
**二分图的最小点覆盖点数 = 二分图的最大匹配（证明见König定理）**

最少边覆盖集：边覆盖集是指图中的一个边集，且图中所有的点都至少为边集中一条边的端点。最小边覆盖集即为边数最少的边覆盖集。
**二分图的最少边覆盖边数 = 点数 - 二分图的最大匹配**

最大独立集：独立集是指图中的一个点集，且这些点之间两两无边相连。最大独立集是指点数最多的独立集。
**二分图的最大独立集点数 = 点数 - 二分图的最大匹配**

最大团(最大完全子图)：团是指图中的一个点集，且这些点之间两两有边相连（完全图）。最大团是指点数最多的团。
**无向图的最大团顶点数量 = 无向图补图的最大独立集顶点数量**

最大团和最大独立集都可以用dfs来求
最大团： V中取K个顶点，两点间相互连接
最大独立集： V中取K个顶点，两点间不连接 
最大团数量 = 补图中最大独立集数

最大团的dfs
```
...
int n,m,mp[105][105];//邻接矩阵存图
int ans,cnt,opt[105],st[105]//opt为最终团数组，st为团集合数组;

void dfs(int x)//参数为点的标号，逐点搜索
{
    if(x>n)//枚举了所有节点
    {
        ans=cnt;
        memcpy(opt,st,sizeof(st));
        return;
    }
    int flag=1;
    for(int i=1;i<x;i++)
    {
        if(st[i] && !mp[i][x])
        {
            flag=0;
            break;
        }
    }
    if(flag)//满足入团条件
    {
        st[x]=1;
        ++cnt;
        dfs(x+1);
        st[x]=0;
        --cnt;
    }
    if(cnt+n-x>ans)//剪枝判定：剩下的点和已在团中的点的和若都比当前搜到的ans的小，就不必进行下一个点的dfs
    {
        dfs(x+1);//这里与上面的深搜不同在于cnt的变化
    }
}
int main()
{
    scanf("%d%d",&n,&m);
    memset
    while(m--)
    {
        int x,y;
        scanf("%d%d",&x,&y);
        mp[x][y]=mp[y][x]=1;
    }
    dfs(1);
}

```

最大独立集的dfs
```
#include <iostream>
#include <cstring>
#include <cstdlib>
#include <cstdio>
#include <algorithm>
#include<vector> 
using namespace std; 
vector<vector<int> > G;//领接表存图
int n, m;
int color[123];
vector<int> rec;
int maxnum;
void dfs(int u,int _count) {
    if (_count > maxnum) {//更新答案
        maxnum = _count;
        rec.clear();
        for (int i = 1;i <= n;++i) {
            if (color[i]) rec.push_back(i);
        }
    }
    if (u == n + 1) return ;
    //用于判断是否可以染成黑色
    bool ok = true;
    for (int i = 0;i < G[u].size();++i) {
        if (color[G[u][i]]) ok = false;
    }
 
    if (ok) {//u节点可以染成黑色
        color[u] = 1;
        dfs(u + 1, _count + 1);
        color[u] = 0;
    }
    dfs(u + 1, _count);//u染成白色－>跳过
}
int main()
{
    int t, u, v;
    cin >> t;
    while(t--) {
        scanf("%d%d", &n, &m);
        G.clear();
        G.resize(n + 2);
        while(m--) {
            scanf("%d%d", &u, &v);
            G[u].push_back(v);
            G[v].push_back(u);
        }
        memset(color, 0,sizeof color);
        maxnum = 0;
        dfs(1, 0);
        printf("%d\n", maxnum);
        for (int i = 0;i < rec.size();++i)
            printf("%d%c", rec[i], i == maxnum - 1?'\n':' ');
    }
    return 0;
}


///////
//也可用补图的最大团个数来求
//邻接矩阵补图构造方法如下：先构造完全图，再把需要连点的边删掉
...
for (int i = 0; i < 105; ++i) 
{
    fill(mp[i], mp[i]+105, 1);//构造完全图
}
while (M--) 
{
    canf("%d %d", &x, &y);
    mp[x][y] = mp[y][x] = 0;//删边
}

```

图的搜索样例：m着色问题
题目描述
        给定无向连通图G=(V, E)和m种不同的颜色，用这些颜色为图G的各顶点着色，每个顶点着一种颜色。是否有一种着色法使G中相邻的两个顶点有不同的颜色?
        这个问题是图的m可着色判定问题。若一个图最少需要m种颜色才能使图中每条边连接的两个顶点着不同颜色，则称这个数m为该图的色数。求一个图的色数m的问题称为图的m可着色优化问题。
        编程计算：给定图G=(V, E)和m种不同的颜色，找出所有不同的着色法和着色总数。

输入
第一行是顶点的个数n（2≤n≤10），颜色数m（1≤m≤n）。
接下来是顶点之间的相互关系：a b
表示a和b相邻。当a，b同时为0时表示输入结束。

输出
输出所有的着色方案，表示某个顶点涂某种颜色号，每个数字的后面有一个空格。最后一行是着色方案总数。

样例输入
5 4
1 3
1 2
1 4
2 3
2 4
2 5
3 4
4 5
0 0
样例输出
1 2 3 4 1 
1 2 3 4 3 
1 2 4 3 1 
1 2 4 3 4 
1 3 2 4 1 
1 3 2 4 2 
1 3 4 2 1 
1 3 4 2 4 
1 4 2 3 1 
1 4 2 3 2 
1 4 3 2 1 
1 4 3 2 3 
2 1 3 4 2 
2 1 3 4 3 
2 1 4 3 2 
2 1 4 3 4 
2 3 1 4 1 
2 3 1 4 2 
2 3 4 1 2 
2 3 4 1 4 
2 4 1 3 1 
2 4 1 3 2 
2 4 3 1 2 
2 4 3 1 3 
3 1 2 4 2 
3 1 2 4 3 
3 1 4 2 3 
3 1 4 2 4 
3 2 1 4 1 
3 2 1 4 3 
3 2 4 1 3 
3 2 4 1 4 
3 4 1 2 1 
3 4 1 2 3 
3 4 2 1 2 
3 4 2 1 3 
4 1 2 3 2 
4 1 2 3 4 
4 1 3 2 3 
4 1 3 2 4 
4 2 1 3 1 
4 2 1 3 4 
4 2 3 1 3 
4 2 3 1 4 
4 3 1 2 1 
4 3 1 2 4 
4 3 2 1 2 
4 3 2 1 4 
Total=48
```
#pragma warning(disable:4996)
#include<iostream>
#include<cstdio>
#include<cstring>
#include<algorithm>
using namespace std;

int n, m;
int mp[15][15],color[15],col_cnt,ans,col_vis[15];

void dfs(int x)
{
	if (x == n+1 )
	{
		if (col_cnt == m)
		{
			ans++;
			for (int i = 1; i <= n; i++)
			{
				if (i != n)
				{
					printf("%d ", color[i]);
				}
				else
				{
					printf("%d\n", color[i]);
				}
			}
		}
		return;
	}
	for (int i = 1; i <= m; i++)
	{
		int flag_color = 1;
		for (int j = 1; j <= n; j++)
		{
			if (mp[x][j] && i == color[j])
			{
				flag_color = 0;
				break;
			}
		}
		if (flag_color)
		{
			color[x] = i;
			if (!col_vis[i])
			{
				col_cnt++;
			}
			col_vis[i]++;
			dfs(x + 1);
			color[x] = -1;
			col_vis[i]--;
			if (!col_vis[i])
			{
				col_cnt--;
			}
		}
		else
		{
			continue;
		}
	}
}
int main()
{
	scanf("%d%d", &n, &m);
	int x, y;
	memset(color, -1, sizeof(color));
	while (~scanf("%d%d", &x, &y))
	{
		if (!x && !y)
		{
			break;
		}
		mp[x][y] = mp[y][x] = 1;
	}
	dfs(1);
	printf("%d\n", ans);
	//system("pause");
	return 0;
}
```
