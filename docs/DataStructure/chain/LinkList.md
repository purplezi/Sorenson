## 链表常用思路

### 1 快慢针

#### 1.1 倒数第 k 个节点

```c++
ListNode* FindKthToTail(ListNode* pHead, int k) 
{
    if(pHead == NULL || k <= 0)
    {
        return NULL;
    }
    ListNode *p = pHead;
    for(int i = 0; i < k - 1; i++)
    {
        if(p->next)
        {
            p = p->next;
        }
        else
        {
            return NULL;
        }
    }
    ListNode *q = pHead;
    while(p->next)
    {
        p = p->next;
        q = q->next;
    }
    return q;
}
```

### 2 反转链表

#### 2.1 顺序实现
```c++
ListNode *ReverseList(ListNode *phead)
{
    ListNode *pReverseHead = NULL;
    ListNode *pNode = pHead;
    ListNode *pPre = NULL;
    while(pNode)
    {
        ListNode *pNext = pNode->next;
        if(pNext == NULL)
        {
            pReverseHead = pNode;
        }
        pNode->next = pPrev;
        pPrev = pNode;
        pNode = pNext
    }
    return pReverseHead;
}
```

#### 2.2 递归实现
```c++
ListNode* ReverseList(ListNode* pHead) {
        if(pHead == NULL)
        {
            return NULL;
        }
        if(pHead->next == NULL)
        {
            return pHead;        
        }
        ListNode *p = ReverseList(pHead->next);
        pHead->next->next = pHead;
        pHead->next = NULL;
        return p;
    }
```

### 3 去重

#### 3.1 保留重复节点

```py
class Solution:
    def deleteDuplicates(self, head: ListNode) -> ListNode:
        ans = head
        while head != None:
            if head.next != None and head.next.val == head.val:
                head.next = head.next.next
            else:
                head = head.next
        return ans

```

#### 3.2 不保留重复节点

==要义：== 开辟一个新的头节点既可以大大减少工作量！！！
`当边界处理情况过多时，考虑能否通过增加节点来保证代码通用`
```c++
/*
struct ListNode {
    int val;
    struct ListNode *next;
    ListNode(int x) :
        val(x), next(NULL) {
    }
};
*/
class Solution {
public:
    ListNode* deleteDuplication(ListNode* pHead) {
        ListNode *tmphead = (ListNode*)malloc(sizeof(ListNode));
        ListNode *pre = tmphead,*p = NULL,*q = NULL;
        pre->next = pHead;
        if(pre->next)
        {
            p = pre->next;
        }
        else
        {
            return pre->next;
        }
        if(p->next)
        {
            q = p->next;
        }
        else
        {
            return pre->next;
        }
        while(q)
        {
            if(q->val == p->val)
            {
              while(q && q->val == p->val)
              {
                q = q->next;
              }
                pre ->next = q;
                p = q;
                if(q)
                    q = q->next;  
            }
            else
            {
                q = q->next;
                p = p->next;
                pre = pre->next;
            }
        }
        return tmphead->next;
    }
};
```