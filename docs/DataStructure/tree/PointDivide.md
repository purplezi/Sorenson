## 点分治核心：找重心
```
void divide(int u){
    ans+=solve(u,0);
    vis[u]=1;
    int totsz=size;
    for(int i=head[u];i;i=Next[i]){
        int v=ver[i];
        if(vis[v]) continue;
        ans-=solve(v,edge[i]);
        mx=inf,rt=0;
        size=sz[v]>sz[u]?totsz-sz[u]:sz[v];//这里应该这样写才是对的 
        findrt(v,0);
        divide(rt);
    }
}
```
---
给一颗n个节点的树，每条边上有一个距离v。定义d(u,v)为u到v的最小距离。给定k值，求有多少点对（u，v）使u到v的距离小于等于k。 
```
//minamoto
#include<bits/stdc++.h>
#define N 40005
#define M 80005
#define ll long long
#define inf 0x3f3f3f3f
using namespace std;
#define getc() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<15,stdin),p1==p2)?EOF:*p1++)
char buf[1<<15],*p1=buf,*p2=buf;
template<class T>inline bool cmax(T&a,const T&b){return a<b?a=b,1:0;}
inline int read(){
    #define num ch-'0'
    char ch;bool flag=0;int res;
    while(!isdigit(ch=getc()))
    (ch=='-')&&(flag=true);
    for(res=num;isdigit(ch=getc());res=res*10+num);
    (flag)&&(res=-res);
    #undef num
    return res;
}
int ver[M],Next[M],head[N],edge[M];
int n,tot,root;ll k;
void add(int u,int v,int e){
    ver[++tot]=v,Next[tot]=head[u],head[u]=tot,edge[tot]=e;
    ver[++tot]=u,Next[tot]=head[v],head[v]=tot,edge[tot]=e;
}
int sz[N],vis[N],mx,size;
ll d[N],q[N],l,r;
void getroot(int u,int fa){
    sz[u]=1;int num=0;
    for(int i=head[u];i;i=Next[i]){
        int v=ver[i];
        if(v==fa||vis[v]) continue;
        getroot(v,u);
        sz[u]+=sz[v];
        cmax(num,sz[v]);
    }
    cmax(num,size-sz[u]);
    if(num<mx) mx=num,root=u;
}
void getdis(int u,int fa){
    q[++r]=d[u];
    for(int i=head[u];i;i=Next[i]){
        int v=ver[i];
        if(v==fa||vis[v]) continue;
        d[v]=d[u]+edge[i];
        getdis(v,u);
    }
}
ll calc(int u,int val){
    r=0;
    d[u]=val;
    getdis(u,0);
    ll sum=0;l=1;
    sort(q+1,q+r+1);
    while(l<r){
        if(q[l]+q[r]<=k) sum+=r-l,++l;
        else --r;
    }
    return sum;
}
ll ans=0;
void dfs(int u){
    ans+=calc(u,0);
    vis[u]=1;
    for(int i=head[u];i;i=Next[i]){
        int v=ver[i];
        if(vis[v]) continue;
        ans-=calc(v,edge[i]);
        size=sz[v];
        mx=inf;
        getroot(v,0);
        dfs(root);
    }
}
int main(){
    //freopen("testdata.in","r",stdin);
    n=read();
    for(int i=1;i<n;++i){
        int u=read(),v=read(),e=read();
        add(u,v,e);
    }
    k=read();
    size=n;
    mx=inf;
    getroot(1,0);
    dfs(root);
    printf("%lld",ans);
    return 0;
}

tree
```
---
给定一棵有n个点的树
询问树上距离为k的点对是否存在。点分治【模板】
```
//minamoto
#include<cstdio>
#include<iostream>
#define inf 0x3f3f3f3f
#define getc() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
char buf[1<<21],*p1=buf,*p2=buf;
template<class T>inline bool cmax(T&a,const T&b){return a<b?a=b,1:0;}
inline int read(){
    #define num ch-'0'
    char ch;bool flag=0;int res;
    while(!isdigit(ch=getc()))
    (ch=='-')&&(flag=true);
    for(res=num;isdigit(ch=getc());res=res*10+num);
    (flag)&&(res=-res);
    #undef num
    return res;
}
const int N=10005;
int ans[10000005];
int ver[N<<1],head[N],Next[N<<1],edge[N<<1];
int sz[N],son[N],st[N];bool vis[N];
int n,m,size,mx,rt,tot,top;
inline void add(int u,int v,int e){
    ver[++tot]=v,Next[tot]=head[u],head[u]=tot,edge[tot]=e;
    ver[++tot]=u,Next[tot]=head[v],head[v]=tot,edge[tot]=e;
}
void getrt(int u,int fa){
    sz[u]=1,son[u]=0;
    for(int i=head[u];i;i=Next[i]){
        int v=ver[i];
        if(vis[v]||v==fa) continue;
        getrt(v,u);
        sz[u]+=sz[v],cmax(son[u],sz[v]);
    }
    cmax(son[u],size-sz[u]);
    if(son[u]<mx) mx=son[u],rt=u;
}
void query(int u,int fa,int d){
    st[++top]=d;
    for(int i=head[u];i;i=Next[i]){
        int v=ver[i];
        if(vis[v]||v==fa) continue;
        query(v,u,d+edge[i]);
    }
}
void solve(int rt,int d,int f){
    top=0;
    query(rt,0,d);
    if(f){
        for(int i=1;i<top;++i)
        for(int j=i+1;j<=top;++j)
        ++ans[st[i]+st[j]];
    }
    else{
        for(int i=1;i<top;++i)
        for(int j=i+1;j<=top;++j)
        --ans[st[i]+st[j]];
    }
}
void divide(int u){
    vis[u]=true;
    solve(u,0,1);
    for(int i=head[u];i;i=Next[i]){
        int v=ver[i];
        if(vis[v]) continue;
        solve(v,edge[i],0);
        mx=inf,rt=0,size=sz[v];
        getrt(v,0);
        divide(rt);
    }
}
int main(){
    n=read(),m=read();
    for(int i=1;i<n;++i){
        int u=read(),v=read(),e=read();
        add(u,v,e);
    }
    rt=0,mx=inf,size=n;
    getrt(1,0),divide(rt);
    while(m--){
        int k=read();
        puts(ans[k]?"AYE":"NAY");
    }
    return 0;
}

```