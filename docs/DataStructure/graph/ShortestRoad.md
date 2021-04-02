### 图的最短路径算法
**核心：逐点松弛**
当需要涉及到求图的最短路相关算法时
一般都是对点操作
由于复杂度一般为指数
故这类问题数据范围一般在1000点以内
常用邻接矩阵存储图
而SPAF需要储存拓扑关系，故用链式前向星存图！  
#### 1.Dijstra(一般求单源点最短距O($n^2$),所有点时为O($n^3$))
不能求含负权边的图
```
void Dijkstra(int s)//输入参数为源点
{
    memset(vis, 0, sizeof(vis));//visit数组标记松弛集合中的点	
    for (int i = 1; i <= n; i++)
    {
        sum[i] = inf;//求单源点时，sum为一维
    }
    sum[s]=0;
    vis[s] = 1;
    for (int i = 1; i <= n; i++)//n次松弛
    {
        int minn = inf,pos;
        for (int j = 1; j <= n; j++)//从未松弛集合中擂选最小
        {
            if (sum[j] < minn && !vis[j])
            {
                pos = j;
                minn = sum[j];
            }
        }		
        vis[pos] = 1;//以该点对其他点进行松弛
        for (int j = 1; j <= n; j++)
        {
            if (sum[pos] + dist[pos][j] < sum[j] && !vis[j])//这里的松弛条件因题而变
            {
                 sum[j] = sum[pos] + dist[pos][j];
            }
        }
    }
｝
```


---

#### 2.Florid(任意两点最短路径，直接暴力逐点松弛，O($n^3$))
```
void Floryd()
{
	for (int k = 1; k <= n; k++)//从插第一点到插第1-n点
	{
		for (int i = 1; i <= n; i++)
		{
			for (int j = 1; j <= n; j++)
			{
				dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);//这里的松弛条件因题而变
			}
		}
	}
}
```

#### 3.Spfa(单源最短路径，可以解决负权边，时间复杂度O(nm),空间复杂度O(m))
spfa用类bfs算法，需要提供下一个进队的点，用链式前向星存储
spfa法之所以可以处理负权，而Dijstra和Floryd不能，主要是因为
Dijstra和Floryd没有记录直接拓扑关系，单纯所有点松弛所有点，以Dijstra为例，
若是松弛中使用visit，则会少算其他点到已松弛集合点的负权而使最短路径变大
若是松弛中不使用visit，则会重复走一条负权的边使得最短路径更短
而SPFA采用直接拓扑关系（即链式前向星）进行bfs，避免了这种问题
SPFA还可以用来判断是否存在负环：若一个点入队了两次，即记录源点到每一点经过的点数（cnt[v]=cnt[u]+1），若cnt[v]>=n(点数)，则说明存在负环
```
struct Edge
{
    int from, to, weight, next;
    //Edge(int from,int to,int dd,int next):from(from),to(to),dd(dd),next(next){}
}

Edge edge[MAXN*MAXN];
int head[MAXN], edgenum;
bool vis[MAXN];
int dist[MAXN][MAXN];

void init()
{
	edgenum = 0;
	memset(head, -1, sizeof(head));
}

void addEdge(int u, int v, int dist)
{
    Edge E;
    E.from=u;
    E.to=v;
    E.weight=dist;
    E.next=head[u];
    edge[edgenum] = E;
    head[u] = edgenum++;
}

void Spfa(int s)
{
    queue<int> q;
    memset(vis,0,sizeof(vis));
    dist[s]=0;
    vis[s]=1;
    q.push(s);
    while(!q.empty())
    {
        int u=q.front();
        q.pop();
        visit[u]=1;
        for(int i=head[u];i!=-1;i=edge[i].next)
        {
            Edge E = edge[i];
            if (dist[E.to] > dist[u] + E.weight)//松弛所有连着的点，但只把未入过队的入队
			{
				dist[E.to] = dist[u] + E.weight;
				if (!vis[E.to])
				{
					vis[E.to] = 1;
					Q.push(E.to);
				}
			}
        }        
    }
}
```
spfa被称为"队列优化的Bellman-Ford算法"
Bellman-Ford流程如下：
(1)扫描所有边(x,y,z)，若dist[y]>dist[x]+z,则用dist[x]+z更新dits[y]
(2)重复上述步骤，直到没有更新操作发生（O(nm)）
(3)若需要判负环，只需要看迭代次数。若在n-1轮迭代内结束（所有边仍满足三角不等式），则图中无负环；若经过n轮迭代，算法仍未结束（仍有可能产生更新的边），则图中存在负环
### Tip:
attention:
* 这三种方法可都对应有向图和无向图，无向图只需双向建边即可

最短路径算法的应用：

* 逐点松弛的算法，可以更改维护比较的目标（最短路）
* 通过最短路径可以求最小环（枚举消边跑n次Dijstra或一次Floryd内再加上对已插入点（即k）的最小环路维护），普通的判环只需要拓扑排序即可（环上的点入度为1，无法入队）
```
void Floryd()
{
    ans=inf;
	for (int k = 1; k <= n; k++)//从插第一点到插第1-n点
	{
        for(int i=1;i<k;i++)
        {
            for(int j=1;j<i;j++)
            {
                ans=min(ans,a[i][k]+a[k][j]+dist[i][j]);//a为原图单边长度
            }
        }
		for (int i = 1; i <= n; i++)
		{
			for (int j = 1; j <= n; j++)
			{
				dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);//这里的松弛条件因题而变
			}
		}
	}
}
```