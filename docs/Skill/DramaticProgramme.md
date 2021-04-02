## 动态规划

### 1 连续子数组的最大和

> 输入一个整型数组，数组里有正数也有负数。数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。要求时间复杂度为 O(n).

> input:
> [1,-2,3,10,-4,7,2,-5]

> output:
> 18

```c++
int FindGreatestSumOfSubArray(vector<int> array) {
        int len = array.size();
        if(len == 0)
        {
            return 0;
        }
        int dp[len+1];
        dp[0] = array[0];
        int ans = dp[0];
        for(int i = 1; i < len; i++)
        {
            if(dp[i-1] > 0)
            {
                dp[i] = dp[i-1] + array[i];
            }
            else
            {
                dp[i] = array[i];
            }
            if(dp[i] > ans)
            {
                ans = dp[i];
            }
        }
        return ans;
    }
```

### 2.剪绳子

> 给你一根长度为n的绳子，请把绳子剪成整数长的m段（m、n都是整数，n>1并且m>1，m<=n），每段绳子的长度记为k[1],...,k[m]。请问k[1]x...xk[m]可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

> input:
> 8

> output:
> 18

```c++
class Solution {
public:
    int cutRope(int number) {
        int ans[10005];
        for(int i = 0; i <= 4; i++)
        {
            ans[i] = i;
        }
        for(int i = 5; i <= number; i++)
        {
            ans[i] = i;
            for(int j = 0;j < i; j++)
            {
                ans[i] = max(ans[i],ans[j] * ans[i-j]);
            }
        }
        return ans[number];
    }
};
```