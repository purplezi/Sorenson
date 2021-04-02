### LCA

**1.tarjan**
Tarjan
算法求 LCA 的时间复杂度为 O(n+q)，是一种离线算法，要用到并查集。（注：这里的复杂度其实应该不是 
O(n+q)，还需要考虑并查集操作的复杂度 ，但是由于在多数情况下，路径压缩并查集的单次操作复杂度可以看做 O(1)，所以写成了 O(n+q)）
　　
Tarjan算法基于 dfs ，在 dfs 的过程中，对于每个节点位置的询问做出相应的回答。
dfs 的过程中，当一棵子树被搜索完成之后，就把他和他的父亲合并成同一集合；在搜索当前子树节点的询问时，如果该询问的另一个节点已经被访问过，那么该编号的询问是被标记了的，于是直接输出当前状态下，另一个节点所在的并查集的祖先；如果另一个节点还没有被访问过，那么就做下标记，继续 dfs 。
```
#include <cstring>
#include <cstdio>
#include <algorithm>
#include <cstdlib>
#include <cmath>
using namespace std;
const int N=40000+5;
struct Edge{
    int cnt,x[N],y[N],z[N],nxt[N],fst[N];
    void set(){
        cnt=0;
        memset(x,0,sizeof x);
        memset(y,0,sizeof y);
        memset(z,0,sizeof z);
        memset(nxt,0,sizeof nxt);
        memset(fst,0,sizeof fst);
    }
    void add(int a,int b,int c){
        x[++cnt]=a;
        y[cnt]=b;
        z[cnt]=c;
        nxt[cnt]=fst[a];
        fst[a]=cnt;
    }
}e,q;
int T,n,m,from,to,dist,in[N],rt,dis[N],fa[N],ans[N];
bool vis[N];
void dfs(int rt){
    for (int i=e.fst[rt];i;i=e.nxt[i]){
        dis[e.y[i]]=dis[rt]+e.z[i];
        dfs(e.y[i]);
    }
}
int getf(int k){
    return fa[k]==k?k:fa[k]=getf(fa[k]);
}
void LCA(int rt){
    for (int i=e.fst[rt];i;i=e.nxt[i]){
        LCA(e.y[i]);
        fa[getf(e.y[i])]=rt;
    }
    vis[rt]=1;
    for (int i=q.fst[rt];i;i=q.nxt[i])
        if (vis[q.y[i]]&&!ans[q.z[i]])
            ans[q.z[i]]=dis[q.y[i]]+dis[rt]-2*dis[getf(q.y[i])];
}
int main(){
    scanf("%d",&T);
    while (T--){
        q.set(),e.set();
        memset(in,0,sizeof in);
        memset(vis,0,sizeof vis);
        memset(ans,0,sizeof ans);
        scanf("%d%d",&n,&m);
        for (int i=1;i<n;i++)
            scanf("%d%d%d",&from,&to,&dist),e.add(from,to,dist),in[to]++;
        for (int i=1;i<=m;i++)
            scanf("%d%d",&from,&to),q.add(from,to,i),q.add(to,from,i);
        rt=0;
        for (int i=1;i<=n&&rt==0;i++)
            if (in[i]==0)
                rt=i;
        dis[rt]=0;
        dfs(rt);
        for (int i=1;i<=n;i++)
            fa[i]=i;
        LCA(rt);
        for (int i=1;i<=m;i++)
            printf("%d\n",ans[i]);
    }
    return 0;
}
```

**2.倍增**
```
#include <cstring>
#include <cstdio>
#include <cstdlib>
#include <algorithm>
#include <cmath>
#include <vector>
using namespace std;
const int N=10000+5;
vector <int> son[N];
int T,n,depth[N],fa[N][20],in[N],a,b;
void dfs(int prev,int rt){
    depth[rt]=depth[prev]+1;
    fa[rt][0]=prev;
    for (int i=1;i<20;i++)
        fa[rt][i]=fa[fa[rt][i-1]][i-1];
    for (int i=0;i<son[rt].size();i++)
        dfs(rt,son[rt][i]);
}
int LCA(int x,int y){
    if (depth[x]<depth[y])
        swap(x,y);
    for (int i=19;i>=0;i--)
        if (depth[x]-(1<<i)>=depth[y])
            x=fa[x][i];
    if (x==y)
        return x;
    for (int i=19;i>=0;i--)
        if (fa[x][i]!=fa[y][i])
            x=fa[x][i],y=fa[y][i];
    return fa[x][0];
}
int main(){
    scanf("%d",&T);
    while (T--){
        scanf("%d",&n);
        for (int i=1;i<=n;i++)
            son[i].clear();
        memset(in,0,sizeof in);
        for (int i=1;i<n;i++){
            scanf("%d%d",&a,&b);
            son[a].push_back(b);
            in[b]++;
        }
        depth[0]=-1;
        int rt=0;
        for (int i=1;i<=n&&rt==0;i++)
            if (in[i]==0)
                rt=i;
        dfs(0,rt);
        scanf("%d%d",&a,&b);
        printf("%d\n",LCA(a,b));
    }
    return 0;
}
```

**3.RMQ**
现在来介绍一种 O(nlogn)预处理，O(1)在线查询的算法。
RMQ 的意思大概是“区间最值查询”。顾名思义，用 RMQ 来求 LCA 是通过 RMQ 来实现的。
在 dfs 的过程中，退出一个子树之后就不会再进入了。这是个很好的性质。
所以很显然，一个子树中深度最浅的节点必定是该子树的树根。
显然，两个节点的 LCA 不仅是两个节点的最近公共祖先，而且是囊括这两个节点的最小子树的根，即囊括这两个节点的最小子树中的深度最小的节点。
我们来想一想如何得到这个节点。
现在，我们稍微修改一下 dfs 序，搞一个欧拉序。
欧拉序，就是每次从 
father(x)进入节点 x或者从子节点回溯到 x都要把 x这个编号扔到一个数组的最后。
这样最终会得到一个长度约为 2n的数列。（考虑每一个节点贡献为 2，分别是从其父亲进入该节点，和从该节点回到其父亲）
再注意到，一对点的 LCA 不仅是囊括这两个节点的最小子树中的深度最小的节点，还是连接这对点的简单路径上深度最小的点。
而且从离开 a到进入 b的这段欧拉序必然包括所有这对点之间的简单路径上的所有点，所以我们考虑求得这段欧拉序中所包含的节点中的 深度最小的点即其 LCA 。
从a到 b的这段欧拉序会包含这棵子树中的其他节点，但是不会影响这个最浅点的求得，因为“一对点的 LCA 是囊括这两个节点的最小子树中的深度最小的节点”。
显然，a到 b这段欧拉序是个连续区间。
你可以用线段树维护，但是线段树太 low 了。
现在我们考虑通过预处理来 
O(1)获得这个最浅点。
于是我们要学习一个叫做 ST表 的东西来搞定这个。（和之前倍增中处理的 fa数组差不多）
```
//CodeVS2370
#include <bits/stdc++.h>
#define time _____time
using namespace std;
const int N=50005;
struct Gragh{
    int cnt,y[N*2],z[N*2],nxt[N*2],fst[N];
    void clear(){
        cnt=0;
        memset(fst,0,sizeof fst);
    }
    void add(int a,int b,int c){
        y[++cnt]=b,z[cnt]=c,nxt[cnt]=fst[a],fst[a]=cnt;
    }
}g;
int n,m,depth[N],in[N],out[N],time;
int ST[N*2][20];
void dfs(int x,int pre){
    in[x]=++time;
    ST[time][0]=x;
    for (int i=g.fst[x];i;i=g.nxt[i])
        if (g.y[i]!=pre){
            depth[g.y[i]]=depth[x]+g.z[i];
            dfs(g.y[i],x);
            ST[++time][0]=x;
        }
    out[x]=time;
}
void Get_ST(int n){
    for (int i=1;i<=n;i++)
        for (int j=1;j<20;j++){
            ST[i][j]=ST[i][j-1];
            int v=i-(1<<(j-1));
            if (v>0&&depth[ST[v][j-1]]<depth[ST[i][j]])
                ST[i][j]=ST[v][j-1];
        }
}
int RMQ(int L,int R){
    int val=floor(log(R-L+1)/log(2));
    int x=ST[L+(1<<val)-1][val],y=ST[R][val];
    if (depth[x]<depth[y])
        return x;
    else
        return y;
}
int main(){
    scanf("%d",&n);
    for (int i=1,a,b,c;i<n;i++){
        scanf("%d%d%d",&a,&b,&c);
        a++,b++;
        g.add(a,b,c);
        g.add(b,a,c);
    }
    time=0;
    dfs(1,0);
    depth[0]=1000000;
    Get_ST(time);
    scanf("%d",&m);
    while (m--){
        int x,y;
        scanf("%d%d",&x,&y);
        if (in[x+1]>in[y+1])
            swap(x,y);
        int LCA=RMQ(in[x+1],in[y+1]);
        printf("%d\n",depth[x+1]+depth[y+1]-depth[LCA]*2);
    }
    return 0;
}
```
