## 排序算法

### 1 快速排序(不稳定)

```c++
// 随机生成start-end的任意整数
int RandomInRange(const int start,const int end)
{
  int rand_num = 0;
  rand_num = rand() % (end - start + 1) + start;
  return rand_num;
}

// 任意定位，小的放左，大的放右
int Partition(int data[],int length,int start,int end)
{
  if(data == NULL || length < 0 || start < 0 || end >= length)
  {
    printf("Error\n");
  }
  int index = RandomInRange(start,end); // 先锋针
  int small = start - 1;  // 定小针
  swap(data[index],data[end]);  // 在<algorithm>里
  for(index = start; index < end; index++)
  {
    if(data[index] < data[end])  // 两针不合做交换
    {
      small++;
      if(small != index)
      {
        swap(data[small],data[index]);
      }
    }
  }
  small++; // 最终定位
  swap(data[small],data[end]);
  return small;
}

void QuickSort(int data[], int length, int start, int end) // 传参 0,n-1
{
  if(start == end)
  {
    return;
  }
  int index = Partition(data,length,start,end);
  if(index > start)
  {
    QuickSort(data,length,start,index - 1);
  }
  if(index < end - 1)
  {
    QuickSort(data,length,index + 1,end);
  }
}
```

* 快排不是任何时候都是最优的算法，如果序列已经排好且每次都用最后一个数字作为标准，则时间复杂度达到 $O(n^2)$

其中 Partiton 函数可以保证在 O(n) 算法得到数组中第 k 大的数字

例如求一个数组中出现次数最多的数（保证该数出现超过数组的一半）（即排序后的中位数）

```c++
int MoreThanHalfNum_Solution(vector<int> numbers) {
        int len = numbers.size();
        int mid = len >> 1,start = 0,end = len - 1;
        int index = Partiton(numbers,len,0,len - 1);
        while(index != mid)
        {
            if(index < mid)
            {
                start = index + 1;
                index = Partiton(numbers, len, start, end);
            }
            else
            {
                end = index - 1;
                index = Partiton(numbers, len, start, end);
            }
        }
        int result = numbers[index];
        int cnt = 0;
        for(int i = 0; i < len; i++)
        {
            if(numbers[i] == result)
            {
                cnt++;
            }
        }
        if(cnt > (len>>1))
        {
            return result;
        }
        else
        {
            return 0;
        }
    }
```

### 2 计数排序

```c++
int* countSort(int* arr,int len)
{
	int max = arr[0];  // 记录数列的最大值
	int min = arr[0];  // 记录数列的最小值
	for(int i=0;i<len-1;i++)
	{
		if(arr[i]>max)
		{
			max = arr[i];
		}
		if(arr[i]<min)
		{
			min = arr[i];
		}
	}
	int l = max-min;//计算出数列最大最小值得差值
	int* couarr = new int[l+1];
	for(int i=0;i<len;i++)
	{
		couarr[arr[i]-min]++;//统计元素个数
	}

	int sum = 0;
	for(int i=0;i<l+1;i++)//统计数组做变形，后面的元素等于前面元素的和
	{
		sum += couarr[i];
		couarr[i]=sum;
	}
	int* sortarr = new int[len];
	for(int i=len-1;i>=0;i--)//倒序遍历原始数组，从统计数组中找到正确位置
	{
		sortarr[couarr[arr[i]-min]-1]=arr[i];
		couarr[arr[i]-min]--;
	}
	delete couarr;
	return sortarr;
}
```

计数排序适用于重复值多，范围小，可以空间换时间的场合

### 3 归并排序（稳定）

归并排序，是创建在归并操作上的一种有效的排序算法。算法是采用分治法（Divide and Conquer）的一个非常典型的应用，且各层分治递归可以同时进行。归并排序思路简单，速度仅次于快速排序，为稳定排序算法，一般用于对总体无序，但是各子项相对有序的数列。

迭代版
```c++
void merge_sort(int data[],int len)
{
  if(data == NULL || len <= 0)
  {
    printf("Error\n");
  }
  int *a = data;
  int *b = (int*)malloc(len * sizeof(int));
  int seg,start;
  for(seg = 1; seg < len; seg += seg)
  {
    for(start = 0; start < len; start += seg + seg)
    {
      int low = start, mid = min(start + seg,len),high = min(start + seg + seg,len);
      int start1 = low, end1 = mid - 1, start2 = mid,end2 = high - 1;
      // start2和end2要注意，strat2不要取min以在end2<start2可以退出循环
      int index = low;
      while(start1 <= end1 && start2 <= end2)
      {
        b[index++] = a[start1] <= a[start2] ? a[start1++] : a[start2++];
      }
      while(start1 <= end1)
      {
        b[index++] = a[start1++];
      }
      while(start2 <= end2)
      {
        b[index++] = a[start2++];
      }
    }
    int *temp = a;
    a = b;
    b = temp;
  }
  if(a != data)
  {
    for(int i = 0; i < len; i++)
    {
      b[i] = a[i];
    }
    b = a;
  }
  free(b);
}
```

递归版
```c++
void merge_sort_recursive(int data[],int ans[],int start,int end)
{
  if(data == NULL)
  {
    printf("Error\n");
  }
  if(start >= end)
  {
    return;
  }
  int start1 = start ,end1 = start + ((end - start) >> 1);
  int start2 = end1 + 1, end2 = end;
  merge_sort_recursive(data,ans,start1,end1);
  merge_sort_recursive(data,ans,start2,end2);
  int index = start;
  while(start1 <= end1 && start2 <= end2)
  {
    ans[index++] = data[start1] <= data[start2] ? data[start1++] : data[start2++];
  }
  while(start1 <= end1)
  {
    ans[index++] = data[start1++];
  }
  while(start2 <= end2)
  {
    ans[index++] = data[start2++];
  }
  for(int i = start; i <= end; i++)
  {
    data[i] = ans[i];
  }
}
```

归并排序核心是分治，而其三 while 也是常用的合并模板，如合并两个递增链表

```c++
ListNode* Merge(ListNode* pHead1, ListNode* pHead2) 
{
  ListNode *ans = NULL, *index = NULL, *p = pHead1, *q = pHead2;
  if(p == NULL)
    return q;
  if(q == NULL)
    return p;
  if(pHead1->val < pHead2->val)
  {
    ans = pHead1;
    p = p->next;
  }
  else
  {
    ans = pHead2;
    q = q->next;
  }
  index = ans;
  while(p && q)
  {
    if(p->val <= q->val)
    {
      index->next = p;
      p = p->next;
    }
    else
    {
      index->next = q;
      q = q->next;
    }
    index = index->next;
  }
  while(p)
  {
    index->next = p;
    p = p->next;
    index = index->next;
  }
  while(q)
  {
    index->next = q;
    q = q->next;
    index = index->next;
  }
  return ans;
 }
```

##### 3.1 逆序对

```c++
class Solution {
public:
    int p = 1000000007;
    int InversePairs(vector<int> data) {
        int cnt = 0, len = data.size();
        vector<int> ans(data);
        for(int seg = 1; seg < len; seg += seg)
        {
            for(int i = 0; i < len ; i += seg+seg)
            {
                int start1 = i, end1 = min(start1 + seg - 1,len - 1);
                int start2 = end1 + 1,end2 = min(start2 + seg - 1,len - 1);
                int index = end2;
                while(end1 >= start1 && end2 >= start2)
                {
                    if(data[end1] > data[end2])
                    {
                        ans[index--] = data[end1--];
                        cnt = (cnt +end2 - start2 + 1)%p;
                    }
                    else if(data[end1] <= data[end2])
                    {
                        ans[index--] = data[end2--];
                    }
                }
                while(end1 >= start1)
                {
                    ans[index--] = data[end1--];
                }
                while(end2 >= start2)
                {
                    ans[index--] = data[end2--];
                }
            }
            vector<int> tmp;
            tmp = ans;
            ans = data;
            data = tmp;
        }
        return cnt;
    }
};
```

使用循环的耗时比递归少

### 4. 插入排序（稳定）
时间复杂度：$O(n^2)$
```c++
void InsertionSort(int arr[], int len)
{
    int i, j;
    for (i = 1; i < len; i++)
    {
        int tmp = arr[i];
        for (j = i; j > 0 && arr[j - 1] > tmp; j--)
        {
            arr[j] = arr[j - 1];
        }
        arr[j] = tmp;
    }
}
```

### 5. 希尔排序（不稳定）
不同步长进行插入排序
时间复杂度：比 $O(n^2)$ 稍微好
```c++
int shellSort(int arr[], int n) 
{ 
    for (int gap = n/2; gap > 0; gap /= 2) 
    { 
        for (int i = gap; i < n; i += 1) 
        { 
            int temp = arr[i]; 
            int j;             
            for (j = i; j >= gap && arr[j - gap] > temp; j -= gap) 
                arr[j] = arr[j - gap]; 
            arr[j] = temp; 
        } 
    } 
    return 0; 
} 
```

### 6. 选择排序（不稳定）
时间复杂度：$O(n^2)$
```c++
void SelectionSort(int arr[], int len)
{
    for (int i = 0; i < len - 1; i++)
    {
        int index = i;
        for (int j = i ; j < len; j++)
        {
            if (arr[j] < arr[index])
            {
                index = j;
            }
        }
        swap(arr[i], arr[index]);
    }
}
```

### 7. 冒泡排序（稳定）
时间复杂度：$O(n^2)$
```c++
void BubbleSort(int arr[], int len)
{
    for (int i = 0; i < len; i++)
    {
        for (int j = 1; j < len - i; j++)
        {
            if (arr[j - 1] > arr[j])
            {
                swap(arr[j - 1], arr[j]);
            }
        }
    }
}
```

### 8. 桶排序（稳定）
时间复杂度O(N)，额外空间复杂度O(N)
```c++
void bucketSort(float arr[], int n) 
{ 
    // 1) Create n empty buckets 
    vector<float> b[n]; 
     
    // 2) Put array elements in different buckets 
    for (int i=0; i<n; i++) 
    { 
       int bi = n*arr[i]; // Index in bucket 
       b[bi].push_back(arr[i]); 
    } 
  
    // 3) Sort individual buckets 
    for (int i=0; i<n; i++) 
       sort(b[i].begin(), b[i].end()); 
  
    // 4) Concatenate all buckets into arr[] 
    int index = 0; 
    for (int i = 0; i < n; i++) 
        for (int j = 0; j < b[i].size(); j++) 
          arr[index++] = b[i][j]; 
}
```

### 9. 基数排序（稳定）
基数排序是一种非比较型整数排序算法，其原理是将整数按位数切割成不同的数字，然后按每个位数分别比较。排序过程是将所有待比较数值统一为同样的数位长度，数位较短的数前面补零，然后从最低位开始，依次进行一次排序。这样从最低位排序一直到最高位排序完成以后, 数列就变成一个有序序列。

```c++
/*
 * 获取数组a中最大值
 *
 * 参数说明：
 *     a -- 数组
 *     n -- 数组长度
 */
int get_max(int a[], int n)
{
    int i, max;

    max = a[0];
    for (i = 1; i < n; i++)
        if (a[i] > max)
            max = a[i];
    return max;
}

/*
 * 对数组按照"某个位数"进行排序(桶排序)
 *
 * 参数说明：
 *     a -- 数组
 *     n -- 数组长度
 *     exp -- 指数。对数组a按照该指数进行排序。
 *
 * 例如，对于数组a={50, 3, 542, 745, 2014, 154, 63, 616}；
 *    (01) 当exp=1表示按照"个位"对数组a进行排序
 *    (02) 当exp=10表示按照"十位"对数组a进行排序
 *    (03) 当exp=100表示按照"百位"对数组a进行排序
 *    ...
 */
void count_sort(int a[], int n, int exp)
{
    int output[n];             // 存储"被排序数据"的临时数组
    int i, buckets[10] = {0};

    // 将数据出现的次数存储在buckets[]中
    for (i = 0; i < n; i++)
        buckets[ (a[i]/exp)%10 ]++;

    // 更改buckets[i]。目的是让更改后的buckets[i]的值，是该数据在output[]中的位置。
    for (i = 1; i < 10; i++)
        buckets[i] += buckets[i - 1];

    // 将数据存储到临时数组output[]中
    for (i = n - 1; i >= 0; i--)
    {
        output[buckets[ (a[i]/exp)%10 ] - 1] = a[i];
        buckets[ (a[i]/exp)%10 ]--;
    }

    // 将排序好的数据赋值给a[]
    for (i = 0; i < n; i++)
        a[i] = output[i];
}

/*
 * 基数排序
 *
 * 参数说明：
 *     a -- 数组
 *     n -- 数组长度
 */
void radix_sort(int a[], int n)
{
    int exp;    // 指数。当对数组按各位进行排序时，exp=1；按十位进行排序时，exp=10；...
    int max = get_max(a, n);    // 数组a中的最大值

    // 从个位开始，对数组a按"指数"进行排序
    for (exp = 1; max/exp > 0; exp *= 10)
        count_sort(a, n, exp);
}
```

### 10. 堆排序（不稳定）
时间复杂度 $O(nlogn)$
```c++
void heapify(int arr[], int n, int i) 
{ 
    int largest = i; // 将最大元素设置为堆顶元素
    int l = 2*i + 1; // left = 2*i + 1 
    int r = 2*i + 2; // right = 2*i + 2 
  
    // 如果 left 比 root 大的话
    if (l < n && arr[l] > arr[largest]) 
        largest = l; 
  
    // I如果 right 比 root 大的话
    if (r < n && arr[r] > arr[largest]) 
        largest = r; 
  
    if (largest != i) 
    { 
        swap(arr[i], arr[largest]); 
  
        // 递归地定义子堆
        heapify(arr, n, largest); 
    } 
} 

void heapSort(int arr[], int n) 
{ 
    // 建立堆
    for (int i = n / 2 - 1; i >= 0; i--) 
        heapify(arr, n, i); 
  
    // 一个个从堆顶取出元素
    for (int i=n-1; i>=0; i--) 
    { 
        swap(arr[0], arr[i]);  
        heapify(arr, i, 0); 
    } 
} 
```




