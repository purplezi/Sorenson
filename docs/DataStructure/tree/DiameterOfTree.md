### 树的直径
```c++
#include<cstdio>
#include<cstring>
#include<algorithm>


方法：先从任意一点P出发，找离它最远的点Q，再从点Q出发，找离它最远的点W，W到Q的距离就是是的直径
using namespace std;
const int N = 100005;
int n, m, t, p, ans;
int d[N], first[N], v[N], w[N], nxt[N];
void add(int x, int y, int z)//链式前向星
{
	t++;
	nxt[t] = first[x];
	first[x] = t;
	v[t] = y;
	w[t] = z;
}

void dfs(int x, int father)

{

	int i, j;

	if (ans < d[x])

	{

		ans = d[x];

		p = x;

	}

	for (i = first[x]; i; i = nxt[i])

	{

		j = v[i];

		if (j == father)

			continue;

		d[j] = d[x] + w[i];

		dfs(j, x);

	}

}

void find(int x)//两次dfs求直径

{

	ans = 0;

	d[x] = 0;

	dfs(x, 0);

}

int main()

{

	int x, y, z, i;

	scanf("%d%d", &n, &m);

	for (i = 1; i <= m; ++i)

	{

		scanf("%d%d%d", &x, &y, &z);

		add(x, y, z);

		add(y, x, z);

	}

	find(1);

	find(p);

	printf("%d", ans);

	return 0;

}



树形DP求直径
对于每个节点我们要记录两个值：

f1[i] 表示以 i 为根的子树中，i 到叶子结点距离的最大值

f2[i] 表示以 i 为根的子树中，i 到叶子结点距离的次大值

对于一个节点，它到叶子结点距离的最大值和次大致所经过的路径肯定是不一样的

若j是i的儿子，那么（下面的 w[i][j] 表示 i 到 j 的路径长度）：

若 f1[i] < f1[j] + w[i][j]，f2[i] = f1[i]，f1[i] = f1[j] + w[i][j]；
否则，若 f2[i] < f1[j] + w[i][j]，f2[i] = f1[j] + w[i][j]；

理解：这样做就是，先看能否更新最大值，若能，它的次大值就是原先的最大值，再更新它的最大值；若不能，就看能不能更新次大值，若能，就更新，不能就不管它
这样的话，最后的答案 answer = max{ f1[i] + f2[i] }
	
const int N = 100005;

int n, m, t, ans;

int f1[N], f2[N];

int first[N], v[N], w[N], nxt[N];

void add(int x, int y, int z)

{

	t++;

	nxt[t] = first[x];

	first[x] = t;

	v[t] = y;

	w[t] = z;

}

void dp(int x, int father)

{

	int i, j;

	for (i = first[x]; i; i = nxt[i])

	{

		j = v[i];

		if (j == father)

			continue;

		dp(j, x);

		if (f1[x] < f1[j] + w[i])

		{

			f2[x] = f1[x];

			f1[x] = f1[j] + w[i];

		}

		else if (f2[x] < f1[j] + w[i])

			f2[x] = f1[j] + w[i];

		ans = max(ans, f1[x] + f2[x]);

	}

}

int main()

{

	int x, y, z, i;

	scanf("%d%d", &n, &m);

	for (i = 1; i <= m; ++i)

	{

		scanf("%d%d%d", &x, &y, &z);

		add(x, y, z);

		add(y, x, z);

	}

	dp(1, 0);

	printf("%d", ans);

	return 0;

}

```