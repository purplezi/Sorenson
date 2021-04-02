### 树状数组

```
//树状数组是针对空间优化的一种区间修改查询数据结构

#pragma warning(disable:4996)
#include<iostream>
#include<cstdio>
#include<cstring>
#include<string.h>
#include<string>
#include<algorithm>
using namespace std;
#define LL long long
#define MAXN 50005
#define inf 1<<29
int bit[MAXN], n, a[MAXN];

int lowbit(int x)//取二进制中最后一个1出现的位置代表的数字
{
	return x & (-x);
}

void add(int i, int x)//单点修改
{
	while (i <= n)//与该点有关系的都是其祖宗
	{
		bit[i] += x;//修改各个父级节点
		i += lowbit(i);//这是临近且包含自己的节点
	}
}

void sub(int i, int x)
{
	while (i <= n)
	{
		bit[i] -= x;
		i += lowbit(i);
	}
}

int sum(int i)
{
	int s = 0;
	while (i > 0)
	{
		s += bit[i];
		i -= lowbit(i);//这是临近但不包含自己的邻父节点
	}
	return s;
}

int main()
{
	int t;
	scanf("%d", &t);
	for (int tt = 1; tt <= t; tt++)
	{
		printf("Case %d:\n", tt);
		scanf("%d", &n);
		memset(bit, 0, sizeof bit);
		for (int i = 1; i <= n; i++)
		{
			scanf("%d", &a[i]);
			add(i, a[i]);//单点修改的代码也可以用来初始化建树
		}
		char str[10];
		while (scanf("%s", str) && str[0] != 'E')
		{
			if (str[0] == 'Q')
			{
				int u, v;
				scanf("%d%d", &u, &v);
				printf("%d\n", sum(v) - sum(u - 1));
			}
			else if (str[0] == 'A')
			{
				int u, v;
				scanf("%d%d", &u, &v);
				add(u, v);
			}
			else if (str[0] == 'S')
			{
				int u, v;
				scanf("%d%d", &u, &v);
				sub(u, v);
			}
		}
	}
	return 0;
}


//区间更新，单点查询：只需要将原数组a变成差分数组，区间更新时对于差分数组的树状数组只需要更新两点值
//updata(x,k);    //A[x] - A[x-1]增加k
//updata(y + 1, -k);        //A[y+1] - A[y]减少k

//区间更新，区间查询
//∑（i = 1-n）A[i] = ∑(i = 1-n) ∑(j = 1-i)D[j];
//则A[1] + A[2] + ... + A[n]
//
//= (D[1]) + (D[1] + D[2]) + ... + (D[1] + D[2] + ... + D[n])
//
//= n * D[1] + (n - 1)*D[2] + ... + D[n]
//
//= n * (D[1] + D[2] + ... + D[n]) - (0 * D[1] + 1 * D[2] + ... + (n - 1)*D[n])
//所以上式可以变为∑(i = 1-n)A[i] = n * ∑(i = 1-n)D[i] - ∑(i = 1-n)(D[i] * (i - 1));
//所以维护两个树状数组：sum1[i] = D[i]，sum2[i] = D[i]*(i-1);

int n, m;
int a[50005] = { 0 };
int sum1[50005];    //(D[1] + D[2] + ... + D[n])
int sum2[50005];    //(1*D[1] + 2*D[2] + ... + n*D[n])

int lowbit(int x) {
	return x & (-x);
}

void updata(int i, int k) {
	int x = i;    //因为x不变，所以得先保存i值
	while (i <= n) {
		sum1[i] += k;
		sum2[i] += k * (x - 1);
		i += lowbit(i);
	}
}

int getsum(int i) {        //求前缀和
	int res = 0, x = i;
	while (i > 0) {
		res += x * sum1[i] - sum2[i];
		i -= lowbit(i);
	}
	return res;
}
int x, k,y;
int main() {
	cin >> n;
	for (int i = 1; i <= n; i++) {
		cin >> a[i];
		updata(i, a[i] - a[i - 1]);   //输入初值的时候，也相当于更新了值
	}

	//[x,y]区间内加上k
	cin >> x >> y >> k;
	updata(x, k);    //A[x] - A[x-1]增加k
	updata(y + 1, -k);        //A[y+1] - A[y]减少k

	//求[x,y]区间和
	int sum = getsum(y) - getsum(x - 1);

	return 0;
}




```