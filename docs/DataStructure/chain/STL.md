## STL 常用

### 1.栈(stack)

```c++
#include <stack>  // 栈

stack<int> s;

// 常用操作：
s.empty()  // 如果栈为空返回true，否则返回false  
s.size()  // 返回栈中元素的个数  
s.pop()  // 删除栈顶元素但不返回其值  
s.top()  // 返回栈顶的元素，但不删除该元素  
s.push(X)  // 在栈顶压入新元素 ，参数X为要压入的元素

```

### 2.队列(queue)

```c++
#include <queue>  // 队列 

queue<int> q;

// 常用操作： 
q.empty()  // 如果队列为空返回true，否则返回false  
q.size()  // 返回队列中元素的个数  
q.pop()  // 删除队列首元素但不返回其值  
q.front()  // 返回队首元素的值，但不删除该元素  
q.push(X) // 在队尾压入新元素 ，X为要压入的元素
q.back() // 返回队列尾元素的值，但不删除该元素
```

### 3.容器(vector)

```c++
#include <vector>  // 容器

vector<int> v;
vector<int> vec;		//声明一个int型向量
vector<int> vec(5);		//声明一个初始大小为5的int向量
vector<int> vec(10, 1);	//声明一个初始大小为10且值都是1的向量
vector<int> vec(tmp);	//声明并用tmp向量初始化vec向量
vector<int> tmp(vec.begin(), vec.begin() + 3);	//用向量vec的第0个到第2个值初始化tmp
int arr[5] = {1, 2, 3, 4, 5};	
vector<int> vec(arr, arr + 5);		//将arr数组的元素用于初始化vec向量
//说明：当然不包括arr[4]元素，末尾指针都是指结束元素的下一个元素，
//这个主要是为了和vec.end()指针统一。
vector<int> vec(&arr[1], &arr[4]); //将arr[1]~arr[4]范围内的元素作为vec的初始值

// 常用操作：
v.empty()  // 判空 
v.size()  // 返回容器大小
v.push_back()  // 末尾添值
v.pop_back()  // 末尾删值
v.insert()  // 插入元素
v.erase()  // 删除元素
v.swap()  // 交换元素
v.clear()  // 清空元素
v.front()  // 首元素
v.back()  // 尾元素

auto it = lower_bound(v.begin(),v.end(),num); // 第一个>=num
auto it = upper_bound(v.begin(),v.end(),num); // 第一个>num
v.insert(i,num); // 在该位置插入
```

### 4.列表(list)

list 底层是一个带头节点的双向循环链表，任意位置插入和删除时间复杂度 O(1)

```c++
#include<iostream>
#include<list>
using namespace std;


void TestList1()
{
    //构造空的list
    list<int>L1;

    //构造10个值为1的list
    list<int>L2(10, 1);

    //构造用一段区间中的元素构造list
    int arr[] = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 0 };
    list<int> L3(arr, arr + sizeof(arr) / sizeof(arr[0]));

    //用L3取构造L4
    list<int>L4(L3);

    cout << "L1元素个数:" << L1.size() << endl;
    cout << "L2元素个数:" << L2.size() << endl;
    cout << "L3元素个数:" << L3.size() << endl;
    cout << "L4元素个数:" << L4.size() << endl;

    //遍历list1
    list<int>::iterator it1 = L1.begin();
    while (it1 != L1.end())
    {
        cout << *it1 << " ";
        ++it1;
    }
    cout << endl;

    //遍历list2
    list<int>::iterator it2 = L2.begin();
    while (it2 != L2.end())
    {
        cout << *it2 << " ";
        ++it2;
    }
    cout << endl;

    //遍历list3
    list<int>::iterator it3 = L3.begin();
    while (it3 != L3.end())
    {
        cout << *it3 << " ";
        ++it3;
    }
    cout << endl;

    //遍历list4
    list<int>::iterator it4 = L4.begin();
    while (it4 != L4.end())
    {
        cout << *it4 << " ";
        ++it4;
    }
    cout << endl;

    //逆向遍历list4
    list<int>::reverse_iterator it5 = L4.rbegin();
    while (it5 != L4.rend())
    {
        cout << *it5 << " ";
        ++it5;
    }
    cout << endl;
}
void TestList2()
{
    list<int>L;

    //尾插
    L.push_back(1);
    L.push_back(2);
    L.push_back(3);
    L.push_back(4);
    L.push_back(5);
    L.push_back(6);

    list<int>::iterator it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;

    //尾删
    L.pop_back();
    L.pop_back();
    it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;

    //头插
    L.push_front(0);
    it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;

    L.pop_front();
    it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;

    任意位置的插入
    //list<int>::iterator pos = find(L.begin(), L.end(), 2);
    //if (pos != L.end())
    //  pos = L.insert(pos, 9);
    //it = L.begin();
    //while (it != L.end());
    //{
    //  cout << *it << " ";
    //  ++it;
    //}
    //cout << endl;

    任意位置删除、
    //L.erase(pos);
    //it = L.begin();
    //while (it != L.end())
    //{
    //  cout << *it << " ";
    //  ++it;
    //}
    //cout << endl;

    //给list重新赋值
    int arr[] = { 1,2,3, 4, 5, 6, 7, 8, 9 };
    L.assign(arr, arr + sizeof(arr) / sizeof(arr[0]));
    it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;
    cout << "第一个元素" << L.front() << endl;
    cout << "第一个元素" << L.back() << endl;


}

void TestList3()
{
    int arr[] = { 1, 2, 3, 4, 5, 6, 7, 8, 9 };
    list<int>L(arr, arr + sizeof(arr) / sizeof(arr[0]));
    list<int>::iterator it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;

    L.remove(3); //按值删除
    it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;

    class Odd
    {
    public:
        bool operator()(int value)
        {
            return value & 0x01;
        };
    };

    L.remove_if(Odd());
    it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;

}

void TestList4()
{
    int arr[] = { 1, 2,  5, 3,1, 2, 3, 4, 5,7,6, 8, 9 };
    list<int>L(arr, arr + sizeof(arr) / sizeof(arr[0]));
    L.unique();

    list<int>::iterator it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl; 

    L.sort();
    it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;

    L.unique();
    it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;

    //class Three
    //{
    //public:
    //  bool operator()(int left, int right)
    //  {
    //      return 0 == (left + right) % 3;
    //  }
    //};
    //L.unique(Three());
    //it = L.begin();
    //while (it != L.end());
    //{
    //  cout << *it << " ";
    //  ++it;
    //}
    //cout << endl;
}

void TestList5()
{
    list<int>L;
    L.push_back(1);
    L.push_back(4);
    L.push_back(6);

    list<int>L2;
    L2.push_back(2);
    L2.push_back(3);
    L2.push_back(8);

    //将两个已序链表合并成为一个链表，合并后依然有序
    L.merge(L2);
    list<int>::iterator it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;

    //反转链表
    L.reverse();
    it = L.begin();
    while (it != L.end())
    {
        cout << *it << " ";
        ++it;
    }
    cout << endl;
}
```

### 5.集合(set)

```c++
#include <iostream>  
#include <set>  
  
using namespace std;  
  
int main()  
{  
    set<int> s;  
    s.insert(1);  
    s.insert(2);  
    s.insert(3);  
    s.insert(1);  
    cout<<"set 的 size 值为 ："<<s.size()<<endl;  
    cout<<"set 的 maxsize的值为 ："<<s.max_size()<<endl;  
    cout<<"set 中的第一个元素是 ："<<*s.begin()<<endl;  
    cout<<"set 中的最后一个元素是:"<<*s.end()<<endl;  
    s.clear();  
    if(s.empty())  
    {  
        cout<<"set 为空 ！！！"<<endl;  
    }  
    cout<<"set 的 size 值为 ："<<s.size()<<endl;  
    cout<<"set 的 maxsize的值为 ："<<s.max_size()<<endl;  
    return 0;  
}  
```

### 6.关联(map)

```c++
#include<map>
#include<string>

map<int, string> mapStudent;

mapStudent.insert(map<int, string>::value_type(1, "student_one"));
mapStudent.insert(map<int, string>::value_type(2, "student_two"));
mapStudent.insert(map<int, string>::value_type(3, "student_three"));

// or
mapStudent.insert(pair<int, string>(1, "studnet_one"));
mapStudent.insert(pair<int, string>(2, "student_two"));
mapStudent.insert(pair<int, string>(3, "student_three"));

// or
mapStudent[1] = "student_one";
mapStudent[2] = "student_two";
mapStudent[3] = "student_three";


map<int,string>::iterator i;
for (i = mapStudent.begin(); i != mapStudent.end(); i++)
		cout << i->first << ' ' << i->second << endl;

// 判断map中是否有key
if(mymap.count(iKey) == 1)
{

}

if(myMap.find(iKey) != myMap.end())
{

}
```