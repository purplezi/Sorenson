## 生成树、最小生成树（MST）和最小树形图

* 生成树是针对无向连通图的，只需要n-1条边连上n个点（无环）即可，常用方法是Prim算法或Kruskal算法
* 最小树形图是针对有向连通图的，由于有向，需要考虑父子关系，常用算法是朱刘算法。

**1. 无向图的最小生成树**
①生成树的代价：设G = （V，E）是一个无向连通网，生成树上各边的权值之和称为该生成树的代价。最小生成树的算法属于贪心生成，可分为加点法和加边法。
②最小生成树：在图G所有生成树中，代价最小的生成树称为最小生成树。
最小生成树的概念可以应用到许多实际问题中。 例：在n个城市之间建造通信网络，至少要架设n - 1条通信线路，而每两个城市之间架设通信线路的造价是不一样的，那么如何设计才能使得总造价最小。
③MST性质：假设G = (V, E)是一个无向连通网，U是顶点集V的一个非空子集。若(u, v)是一条具有最小权值的边，其中u∈U，v∈V－U，则必存在一棵包含边(u, v)的最小生成树。

最小生成树：
(1)Prim算法（稀点稠边图）
核心：加点生成，每加一个点更新所有点到已生成树的最短距离，直到加完所有点，**而且容易记录直接拓扑关系**
```
//这里用临接矩阵存图
int prim(int n)
{
	int sum = 0;
	for (int i = 1; i <= n; i++)
		maxdis[i] = mp[1][i];//以第一点为起点生成
	memset(vis, 0, sizeof(vis));//标记已经在树内的点
	vis[1] = 1;
	maxdis[1] = 0;
	for (int i = 2; i <= n; i++)
	{
		int k = 1,min = INF;
		for (int j = 1; j <= n; j++)//擂选最短可达点
			if (!v[j] && min > maxdis[j])
			{
				k = j;
				min = maxdis[j];
			}
		sum += min;
		v[k] = 1;
		for (j = 1; j <= n; j++)//更新未入树点的可达距离
			if (!v[j] && maxdis[j] > mp[k][j])
				maxdis[j] = mp[k][j];
	}
	return sum;
```
（2）Kruskal算法（稀边稠点图）
核心：加边生成，将所有边以长度排序，一旦边两端不同在树内，则加入边，可用并查集判断
```
//建立边的数据结构（两个端点的权值）
struct node
{
	int u;
	int v;
	unsigned long long w;
}edge[100001];
 
bool cmp(node a,node b)//将各边按从小到大的权值排序
{
	if(a.w==b.w)
	{
		return a.u==b.u?(a.v<b.v):(a.u<b.u);
	}
	else
	{
		return a.w<b.w;
	}
}
 
int find(int x)//并查集
{
	return x==fa[x]?x:fa[x]=find(fa[x]);//带修
}
 
int Kruskal()
{
	int cnt=0;
	long long weigh=0,ans=0,s=1;
	sort(edge+1,edge+m+1,cmp);//排序
	for(int i=1;i<=n;i++)
	{
		fa[i]=i;//初始化并查集数组
	}
	for(int i=1;i<=m;i++)
	{
		int x=find(edge[i].u);
		int y=find(edge[i].v);
		if(x==y)
		{
			continue;//两点都已经在树内，不用另外加边
		}
		fa[x]=y;//入树
		cnt++;
		ans+=edge[i].w;加边
		if(cnt==n-1)
		{
			return ans;
		}
	}
}
```
##### tip:
(1)要求次小生成树时可以在prim的最小生成树代码里多维护几个变量（树的父子关系pre[],点到生成树的最大距离等等）(例hdu4081最佳虫洞选择的核心代码)
```
maxdis[k][inset[j]]=max(maxddis[inset[j]][pre[k]],mp[pre[k][k]])//剪掉带k树枝可取的最大边
---
for(int i=1;i<=n;i++)
{
    for(int j=i+1;j<=n;j++)
    {
        if(mst[i][j])
        {
            ans=max(ans,(city[i].p+city[j].p)*1.0)/(sroad-mp[i][j]);
        }
        else
        {
            ans=max(ans,(city[i].p+city[j].p)*1.0/(sroad-maxdis[i][j]));
        }
    }
}
```
在求次小生成树时只需要枚举任意两点的边并进行同样的留边断边操作
<br>
**2. 有向图的最小树形图** 
最小树形图：有向带权图指定根节点，求一棵有向生成树使得其所有边权值最小
**朱刘算法**常规步骤：
（1）求最短弧集合（如果出现除根节点以外的孤立点，则不存在最小树形图）
（2）判断集合中有没有有向环，有转步骤3否则转步骤4
（3）收缩点，把有向环收缩成一个点，并且对图重新构建：
核心：设点u在环中，环中指向u的权值为w，点v在环外，原图为G,缩点后成G1,缩点为i
* 以缩点为边终点，对于G中每一条边mp[v][i]=min{mp[v][u]-w[u]}(容斥，展开缩点会补偿)
* 以缩点为边起点，对于G中缩点有mp[i][v]=min｛mp[u][v]｝

（4）展开收缩点，求得最小树形图（不作要求）
```
struct node
{
    int u,v,val;
}edge[maxn*maxn];
ll Dir_MST(int root, int V, int E)  
{
    ll ans = 0;
    while (true)     //如果还是可行的话
    {
        for (int i = 0; i < V; i++) 
        {
            in[i] = INF; //给予每个点的入边（即父子边）进行初始化
        }
            
        /* (1)、最短弧集合E0 */
        for (int i = 1; i <= E; i++)
        {
            int u = edge[i].u, v = edge[i].v;//终点为子节点
            if (edge[i].val < in[v] && u != v) //顶点v有更小的入边，u!=v是为了确保缩点之后，我们的环将会变成点的形式
            {
                pre[v] = u;     //节点u指向v
                in[v] = edge[i].val;    //最小入边
                if (u == root) pos = i;  //这个点就是实际的起点
            }
        }
        
        /* (2)、检查E0 */
        for (int i = 0; i < V; i++)     //判断是否存在最小树形图
        {
            if (i == root) continue;     //是根节点，不管
            if (in[i] == INF) return -1;     //除了根节点以外，有点没有入边，则根本无法抵达它，说明是独立的点，一定不能构成树形图
        }

        /* (3)、收缩图中的有向环并做贡献 */
        int cnt = 0;    //接下来要去求环，用以记录环的个数
        memset(id, -1, sizeof(id));//环编号，类belong数组
        memset(vis, -1, sizeof(vis));
        in[root] = 0;
        for (int i = 0; i < V; i++)     //标记每个环
        {
            ans += in[i];   //加入每个点的入边（既然是最小入边，所以肯定符合最小树形图的思想）
            int v = i;  //v一开始先从第i个节点进去
            while (vis[v] != i && id[v] == -1 && v != root)  //退出的条件有“形成了一个环”、“到了一个环退出”、“到了根节点退出”
            {
                vis[v] = i;
                v = pre[v];
            }
            if (v != root && id[v] == -1)    //如果v是root就说明是返回到了根节点，是条链，没环；又或者，它已经是进入了对应环的编号了，不需要再跑一趟了
            {
                for (int u = pre[v]; u != v; u = pre[u])   //跑这一圈的环
                {
                	id[u] = cnt;    //标记点u是第几个环
                }
                id[v] = cnt++;  //如果再遇到，就是下个点了
            }
        }
        if (cnt == 0) return ans;    //无环的情况，就说明已经取到了最优解，直接返回，或者说是环已经收缩到没有环的情况了
        for (int i = 0; i < V; i++)
        {
            if (id[i] == -1)
            {
                id[i] = cnt++;   //这些点是环外的点，是链上的点，单独再给他们赋值
            }
        } 
        for (int i = 1; i <= E; i++)     //准备开始建立新图  缩点，重新标记
        {
            int u = edge[i].u, v = edge[i].v;
            edge[i].u = id[u];  edge[i].v = id[v];  //建立新图，以新的点进入
            if (id[u] != id[v]) edge[i].val -= in[v];    //为了不改变原来的式子，使得展开后还是原来的式子,即容斥原理，环中做了贡献新边则减去其值
        }
        V = cnt;    //之后的点的数目
        root = id[root];    //新的根节点的序号，因为id[]的改变，所以根节点的序号也改变了
    }
    return ans;
}
```
<br>

**3. 无向图的生成树** 
求无向图的生成树个数:**基尔霍夫定理**
度数矩阵(n*n) ：D[i][i]为点i的度 其他位置为0  
邻接矩阵(n*n) : a[i][i]=0; a[i][j]=[i与j相连] 
基尔霍夫矩阵 = 度数矩阵 - 邻接矩阵
即C=D-A:C[i][i]=i的度数，C[i][j]=-1(i,j有边)，C[i][j]=0(其他)
Matrix-Tree 定理： 
一个n个点m条边的无向图的生成树总数为其对应的基尔霍夫矩阵的n-1阶余子式(行列式)。
求行列式可用高斯消元化为三角阵求对角积即可
```
#include <iostream>  
#include <cstdio>  
#include <cmath>  
#define LL long long
using namespace std;  

const int MOD = 1e9+7;
LL a[105][105];
int n, m, sign, du[105];

void solve(int N){//高斯消元
    sign = 0; LL ans = 1;
    for(int i=1; i<N; i++){
        for(int j=i+1; j<N; j++){//当前之后的每一行第一个非0数要转化成0
            int x=i, y=j;
            while( a[y][i] ){//利用gcd的方法，不停地进行辗转相除
                LL t = a[x][i] / a[y][i];
                for(int k=i; k<N; k++)
                    a[x][k] = (a[x][k] - a[y][k] * t) % MOD;
                swap(x, y);
            }
            if(x != i){//奇数次交换，则D=-D'整行交换
                for(int k=1; k<N; k++)
                    swap(a[i][k], a[x][k]);
                sign ^= 1;//行列式*-1
            }
        }
        if( !a[i][i] ){//斜对角中有一个0，则结果为0
            printf("0\n");
            return ;
        }
        else ans = ans * a[i][i] % MOD;
    }
    if(sign != 0) ans *= -1;
    if(ans < 0) ans += MOD;//
    printf("%I64d\n", ans);
    return ;
}

int main(){
    freopen ("tcount.in", "r", stdin);
    freopen ("tcount.out", "w", stdout);
    scanf("%d%d", &n, &m);
    for(int i=1; i<=m; i++)
	{
        int x, y; scanf("%d%d", &x, &y);
        a[x][y] = a[y][x] = -1;
        du[x]++; du[y]++;
    }
    for(int i=1; i<=n; i++) 
    {
        a[i][i] = du[i];
    }//a则为基尔霍夫矩阵
    solve(n);
    return 0;
}

```

**小拓展：高斯消元解决n个n元一次方程组**
核心：化左下为0的三角阵，借第i行的数清第i列的（n-i）个0
```
int n, pl;
double a[1001][1001];
int main()
{
	cin >> n;
	for (int i = 1; i <= n; i++)
		for (int j = 1; j <= n + 1; j++)
			cin >> a[i][j];//输入的是n个方程组成的n*(n+1)矩阵（系数矩阵和和矩阵）
	for (int i = 1; i <= n; i++)
	{
		pl = i;
		while (a[pl][i] == 0 && pl <= n)
			pl++;
		// 判断第i列首元素非0的最上行，因为第i行第i列元素不能为0 
		if (pl == n + 1) { cout << "No Solution"; return 0; }
		//一直判到了n+1行，可是一共才只有n行，说明有一列全为0，无解 
		for (int j = 1; j <= n + 1; j++)             //将第i行第i列元素不为0的那一行与当前行交换 
			swap(a[i][j], a[pl][j]);
		double k = a[i][i];                          //让第i行每个元素都除以a[i][i]使得a[i][i]为1 
		for (int j = 1; j <= n + 1; j++)
			a[i][j] = a[i][j] / k;                         //将第i行第i列的元素消成1,注意同行进行同样的操作 
		for (int j = 1; j <= n; j++)
		{
			if (i != j)                        //将第i列除了第i行的元素全消成0 
			{                               //方法是第j行每个元素a[j][m]都减去a[j][1]*a[i][m] 
				double ki = a[j][i];
				for (int m = 1; m <= n + 1; m++)
					a[j][m] = a[j][m] - ki * a[i][m];
			}
		}
	}
	for (int i = 1; i <= n; i++)
		printf("%.2lf\n", a[i][n + 1]);
	return 0;
}
```
同样，高斯消元可以求解矩阵行列式、矩阵的秩等问题