## 1.普通莫队
```
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
using namespace std;

#define maxn 1010000
#define maxb 1010
int aa[maxn], cnt[maxn], belong[maxn];
int n, m, size, bnum, now, ans[maxn];
struct query {
    int l, r, id;
} q[maxn];

int cmp(query a, query b) {
    return (belong[a.l] ^ belong[b.l]) ? belong[a.l] < belong[b.l] : ((belong[a.l] & 1) ? a.r < b.r : a.r > b.r);
}
#define isdigit(x) ((x) >= '0' && (x) <= '9')
int read() {
    int res = 0;
    char c = getchar();
    while(!isdigit(c)) c = getchar();
    while(isdigit(c)) res = (res << 1) + (res << 3) + c - 48, c = getchar();
    return res;
}
void printi(int x) {
    if(x / 10) printi(x / 10);
    putchar(x % 10 + '0');
}

int main() {
    scanf("%d", &n);
    size = sqrt(n);
    bnum = ceil((double)n / size);
    for(int i = 1; i <= bnum; ++i) 
        for(int j = (i - 1) * size + 1; j <= i * size; ++j) {
            belong[j] = i;
        }
    for(int i = 1; i <= n; ++i) aa[i] = read(); 
    m = read();
    for(int i = 1; i <= m; ++i) {
        q[i].l = read(), q[i].r = read();
        q[i].id = i;
    }
    sort(q + 1, q + m + 1, cmp);
    int l = 1, r = 0;
    for(int i = 1; i <= m; ++i) {
        int ql = q[i].l, qr = q[i].r;
        while(l < ql) now -= !--cnt[aa[l++]];
        while(l > ql) now += !cnt[aa[--l]]++;
        while(r < qr) now += !cnt[aa[++r]]++;
        while(r > qr) now -= !--cnt[aa[r--]];
        ans[q[i].id] = now;
    }
    for(int i = 1; i <= m; ++i) printi(ans[i]), putchar('\n');
    return 0;
}
```


## 2.带修莫队
```
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
using namespace std;
#define maxn 50500
#define maxc 1001000
int a[maxn], cnt[maxc], ans[maxn], belong[maxn];
struct query {
    int l, r, time, id;
} q[maxn];
struct modify {
    int pos, color, last;
} c[maxn];
int cntq, cntc, n, m, size, bnum;
int cmp(query a, query b) {
    return (belong[a.l] ^ belong[b.l]) ? belong[a.l] < belong[b.l] : ((belong[a.r] ^ belong[b.r]) ? belong[a.r] < belong[b.r] : a.time < b.time);
}
#define isdigit(x) ((x) >= '0' && (x) <= '9')
inline int read() {
    int res = 0;
    char c = getchar();
    while(!isdigit(c)) c = getchar();
    while(isdigit(c)) res = (res << 1) + (res << 3) + (c ^ 48), c = getchar();
    return res;
}
int main() {
    n = read(), m = read();
    size = pow(n, 2.0 / 3.0);
    bnum = ceil((double)n / size);
    for(int i = 1; i <= bnum; ++i) 
        for(int j = (i - 1) * size + 1; j <= i * size; ++j) belong[j] = i;
    for(int i = 1; i <= n; ++i) 
        a[i] = read();
    for(int i = 1; i <= m; ++i) {
        char opt[100];
        scanf("%s", opt);
        if(opt[0] == 'Q') {
            q[++cntq].l = read();
            q[cntq].r = read();
            q[cntq].time = cntc;
            q[cntq].id = cntq;
        }
        else if(opt[0] == 'R') {
            c[++cntc].pos = read();
            c[cntc].color = read();
        }
    }
    sort(q + 1, q + cntq + 1, cmp);
    int l = 1, r = 0, time = 0, now = 0;
    for(int i = 1; i <= cntq; ++i) {
        int ql = q[i].l, qr = q[i].r, qt = q[i].time;
        while(l < ql) now -= !--cnt[a[l++]];
        while(l > ql) now += !cnt[a[--l]]++;
        while(r < qr) now += !cnt[a[++r]]++;
        while(r > qr) now -= !--cnt[a[r--]];
        while(time < qt) {
            ++time;
            if(ql <= c[time].pos && c[time].pos <= qr) now -= !--cnt[a[c[time].pos]] - !cnt[c[time].color]++;
            swap(a[c[time].pos], c[time].color);
        }
        while(time > qt) {
            if(ql <= c[time].pos && c[time].pos <= qr) now -= !--cnt[a[c[time].pos]] - !cnt[c[time].color]++;
            swap(a[c[time].pos], c[time].color);
            --time;
        }
        ans[q[i].id] = now;
    }
    for(int i = 1; i <= cntq; ++i) 
        printf("%d\n", ans[i]);
    return 0;
}
```

## 3.树上莫队
需要用到欧拉序
```
#include <cstdio>
#include <cstring>
#include <cmath>
#include <algorithm>
using namespace std;
#define maxn 200200
#define isdigit(x) ((x) >= '0' && (x) <= '9')
inline int read() {
    int res = 0;
    char c = getchar();
    while(!isdigit(c)) c = getchar();
    while(isdigit(c)) res = (res << 1) + (res << 3) + (c ^ 48), c = getchar();
    return res;
}
int aa[maxn], cnt[maxn], first[maxn], last[maxn], ans[maxn], belong[maxn], inp[maxn], vis[maxn], ncnt, l = 1, r, now, size, bnum; //莫队相关
int ord[maxn], val[maxn], head[maxn], depth[maxn], fa[maxn][30], ecnt;
int n, m;
struct edge {
    int to, next;
} e[maxn];
void adde(int u, int v) {
    e[++ecnt] = (edge){v, head[u]};
    head[u] = ecnt;
    e[++ecnt] = (edge){u, head[v]};
    head[v] = ecnt;
}
void dfs(int x) {
    ord[++ncnt] = x;
    first[x] = ncnt;
    for(int k = head[x]; k; k = e[k].next) {
        int to = e[k].to;
        if(to == fa[x][0]) continue;
        depth[to] = depth[x] + 1;
        fa[to][0] = x;
        for(int i = 1; (1 << i) <= depth[to]; ++i) fa[to][i] = fa[fa[to][i - 1]][i - 1];
        dfs(to);
    }
    ord[++ncnt] = x;
    last[x] = ncnt;
}
int getlca(int u, int v) {
    if(depth[u] < depth[v]) swap(u, v);
    for(int i = 20; i + 1; --i) 
        if(depth[u] - (1 << i) >= depth[v]) u = fa[u][i];
    if(u == v) return u;
    for(int i = 20; i + 1; --i)
        if(fa[u][i] != fa[v][i]) u = fa[u][i], v = fa[v][i];
    return fa[u][0];
}
struct query {
    int l, r, lca, id;
} q[maxn];
int cmp(query a, query b) {
    return (belong[a.l] ^ belong[b.l]) ? (belong[a.l] < belong[b.l]) : ((belong[a.l] & 1) ? a.r < b.r : a.r > b.r);
}
void work(int pos) {
    vis[pos] ? now -= !--cnt[val[pos]] : now += !cnt[val[pos]]++;
    vis[pos] ^= 1;
}
int main() {
    n = read(); m = read();
    for(int i = 1; i <= n; ++i) 
        val[i] = inp[i] = read();
    sort(inp + 1, inp + n + 1);
    int tot = unique(inp + 1, inp + n + 1) - inp - 1;
    for(int i = 1; i <= n; ++i)
        val[i] = lower_bound(inp + 1, inp + tot + 1, val[i]) - inp;
    for(int i = 1; i < n; ++i) adde(read(), read());
    depth[1] = 1;
    dfs(1);
    size = sqrt(ncnt), bnum = ceil((double) ncnt / size);
    for(int i = 1; i <= bnum; ++i)
        for(int j = size * (i - 1) + 1; j <= i * size; ++j) belong[j] = i;
    for(int i = 1; i <= m; ++i) {
        int L = read(), R = read(), lca = getlca(L, R);
        if(first[L] > first[R]) swap(L, R);
        if(L == lca) {
            q[i].l = first[L];
            q[i].r = first[R];
        }
        else {
            q[i].l = last[L];
            q[i].r = first[R];
            q[i].lca = lca;
        }
        q[i].id = i;
    }
    sort(q + 1, q + m + 1, cmp);
    for(int i = 1; i <= m; ++i) {
        int ql = q[i].l, qr = q[i].r, lca = q[i].lca;
        while(l < ql) work(ord[l++]);
        while(l > ql) work(ord[--l]);
        while(r < qr) work(ord[++r]);
        while(r > qr) work(ord[r--]);
        if(lca) work(lca);
        ans[q[i].id] = now;
        if(lca) work(lca);
    }
    for(int i = 1; i <= m; ++i) printf("%d\n", ans[i]);
    return 0;
}
```


## 4.回滚莫队
```
#include <cstdio>
#include <algorithm>
#include <cstring>
#include <cmath>
using namespace std;
#define maxn 100100
#define maxb 5050
#define ll long long
int aa[maxn], typ[maxn], cnt[maxn], cnt2[maxn], belong[maxn], lb[maxn], rb[maxn], inp[maxn];
ll ans[maxn];
struct query {
    int l, r, id;
} q[maxn];
int n, m, size, bnum;
#define isdigit(x) ((x) >= '0' && (x) <= '9')
inline int read() {
    int res = 0;
    char c = getchar();
    while(!isdigit(c)) c = getchar();
    while(isdigit(c)) res = (res << 1) + (res << 3) + (c ^ 48), c = getchar();
    return res;
}
int cmp(query a, query b) {
    return (belong[a.l] ^ belong[b.l]) ? belong[a.l] < belong[b.l] : a.r < b.r; 
}
int main() {
    n = read(), m = read();
    size = sqrt(n);
    bnum = ceil((double) n / size);
    for(int i = 1; i <= bnum; ++i) {
        lb[i] = size * (i - 1) + 1;
        rb[i] = size * i;
        for(int j = lb[i]; j <= rb[i]; ++j) belong[j] = i;
    }
    rb[bnum] = n;
    for(int i = 1; i <= n; ++i) inp[i] = aa[i] = read();
    sort(inp + 1, inp + n + 1);
    int tot = unique(inp + 1, inp + n + 1) - inp - 1;
    for(int i = 1; i <= n; ++i) typ[i] = lower_bound(inp + 1, inp + tot + 1, aa[i]) - inp;
    for(int i = 1; i <= m; ++i) {
        q[i].l = read(), q[i].r = read();
        q[i].id = i;
    }
    sort(q + 1, q + m + 1, cmp);
    int i = 1;
    for(int k = 0; k <= bnum; ++k) {
        int l = rb[k] + 1, r = rb[k];
        ll now = 0;
        memset(cnt, 0, sizeof(cnt));
        for( ; belong[q[i].l] == k; ++i) {
            int ql = q[i].l, qr = q[i].r;
            ll tmp;
            if(belong[ql] == belong[qr]) {
                tmp = 0;
                for(int j = ql; j <= qr; ++j) cnt2[typ[j]] = 0;
                for(int j = ql; j <= qr; ++j) {
                    ++cnt2[typ[j]]; tmp = max(tmp, 1ll * cnt2[typ[j]] * aa[j]);
                }
                ans[q[i].id] = tmp;
                continue;
            }
            while(r < qr) {
                ++r; ++cnt[typ[r]]; now = max(now, 1ll * cnt[typ[r]] * aa[r]);
            }
            tmp = now;
            while(l > ql){
                --l; ++cnt[typ[l]]; now = max(now, 1ll * cnt[typ[l]] * aa[l]);
            } 
            ans[q[i].id] = now;
            while(l < rb[k] + 1) {
                --cnt[typ[l]];
                l++;
            }
            now = tmp;
        }
    }
    for(int i = 1; i <= m; ++i) printf("%lld\n", ans[i]);
    return 0;
}
```