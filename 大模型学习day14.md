# 📅 [2025.12.22] 学习笔记：[leetcode刷题以及RAG优化]
## 🎯 学习目标
- [x] 做了leetcode的四道题
- [x] 成功实现了本地化部署LLAMA
## 📝 核心知识点 (Key Takeaways)
### 1.两数相加
这道题的关键是已经逆序排好，那么相加时候就是竖式加法，满十后向后一位进位，则当前节点值是list1+list2+进位carry
``` python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution(object):
    def addTwoNumbers(self, l1, l2):
        """
        :type l1: Optional[ListNode]
        :type l2: Optional[ListNode]
        :rtype: Optional[ListNode]
        """
        # 1. 【初始化】
        # dummy: 结果链表的哨兵头
        # curr: 用来穿针引线的指针
        # carry: 进位
        dummy = ListNode(0)
        curr = dummy
        carry = 0
        # 2. 【核心循环】
        # 条件：只要 l1 没走完，或者 l2 没走完，或者 还有进位 (carry != 0)
        # 只要满足任意一个，就需要继续生成新节点
        while l1 or l2 or carry:
            # 获取当前位的值，如果链表已经空了，就用 0 代替
            val1 = l1.val if l1 else 0
            val2 = l2.val if l2 else 0
            # --- 数学计算 ---
            # 当前总和 = 两个数字 + 进位
            total = val1 + val2 + carry
            # 计算新节点的值（取个位）
            new_val = total % 10
            # 计算新的进位（取十位）
            carry = total // 10
            # --- 链表操作 ---
            # 创建新节点并接在后面
            curr.next = ListNode(new_val)
            curr = curr.next
            # --- 移动指针 ---
            if l1: l1 = l1.next
            if l2: l2 = l2.next
        return dummy.next    
```
### 2.删除链表的第N个节点
一看到有N固定长度，就要想到创捷双指针维持长度，那么这题的关键就是双指针我们要让 slow 停在“被删节点”的前一个位置所以我们让 fast 走到“最后一个节点”就停下来
``` python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution(object):
    def removeNthFromEnd(self, head, n):
        """
        :type head: Optional[ListNode]
        :type n: int
        :rtype: Optional[ListNode]
        """
        # 1. 【哨兵节点】
        # 防止删除的是头节点（例如：[1], 1），这样不用单独写 if
        dummy = ListNode(0,head)
        # 2. 【初始化指针】
        slow = dummy
        fast = dummy
        # 3. 【制造间隔】
        # 让 fast 先走 n 步
        # 此时 fast 和 slow 之间相差 n 个节点
        for _ in range(n):
            fast = fast.next
        # 4. 【同步移动】
        # 我们要让 slow 停在“被删节点”的前一个位置
        # 所以我们让 fast 走到“最后一个节点”就停下来
        while fast.next is not None:
            slow = slow.next
            fast = fast.next    
        # 此时：
        # fast 在最后一个节点 (5)
        # slow 在被删节点的前一个节点 (3)
        # slow.next 就是倒数第 n 个节点 (4)
        slow.next = slow.next.next
        return dummy.next
```
### 3.两两交换链表中的节点
本题只要遵循俩个原则，1个是提前储存好断链节点的信息，另一个是prev 永远停留在“待交换的那一对”的前一个位置
``` python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution(object):
    def swapPairs(self, head):
        """
        :type head: Optional[ListNode]
        :rtype: Optional[ListNode]
        """
        # 1. 【哨兵节点】
        # 因为头节点(1)会被换到后面去，新头节点会变成(2)
        # 用 dummy 可以统一操作，最后返回 dummy.next 即可
        dummy = ListNode(0,head)
        # prev 永远停留在“待交换的那一对”的前一个位置
        prev = dummy
        # 2. 【核心循环】
        # 只有当后面【至少还有两个节点】时，才能进行交换
        # prev.next 是第一个，prev.next.next 是第二个
        while prev.next and prev.next.next:
            # --- A. 锁定两个主角 ---
            node1 = prev.next
            node2 = prev.next.next
            # --- B. 执行交换 (三步走) ---
            # 1. 把 node1 的尾巴接到下一组去 (1 -> 3)
            # 这一步如果不先做，一旦修改了 node2，我们就找不到 3 了
            # 2. 把 node2 的尾巴指回 node1 (2 -> 1)
            # 3. 让前任指向 node2 (dummy -> 2)
            node1.next = node2.next
            node2.next = node1
            prev.next = node2
            # --- C. 移动指针 ---
            # 现在的顺序是 dummy -> 2 -> 1 -> 3...
            # 下一轮的“前任”应该是 1
            prev = node1
        return dummy.next    
```
### 4.随机链表的复制
这一题要用到hashmap存储老节点，我不是理解的很透彻，我还得在看一下
``` python
"""
# Definition for a Node.
class Node:
    def __init__(self, x, next=None, random=None):
        self.val = int(x)
        self.next = next
        self.random = random
"""

class Solution(object):
    def copyRandomList(self, head):
        """
        :type head: Node
        :rtype: Node
        """
        # 0. 【边界处理】
        if not head:
            return None
        # 1. 【造人阶段】
        # 创建一个哈希表，用来记录 {旧节点 : 新节点} 的映射
        # 技巧：预先放入 None 的映射，防止后续查表报错
        mapping = {None: None}
        curr = head
        #第一遍遍历
        while curr:
            # 创建一个新节点，值和旧节点一样
            # 但 next 和 random 先空着，等会再填
            new_node = Node(curr.val)
            # 存入档案：这个旧节点 curr 对应的新节点是 new_node
            mapping[curr] = new_node#其中，使用curr作为mapping的键，curr.val是新的节点对象
            curr = curr.next

        # 2. 【连线阶段】
        # 再次回到头部
        curr = head
        while curr:
            # 取出当前旧节点对应的“复印件”
            new_node = mapping[curr]
            # --- 核心逻辑 ---
            # 新节点的 next，应该指向 (旧节点 next) 对应的复印件
            new_node.next = mapping[curr.next]
            # 新节点的 random，应该指向 (旧节点 random) 对应的复印件
            new_node.random = mapping[curr.random]
            curr = curr.next
        return mapping[head]        
"""



"""
```

### 3.agent进展
今天加入本地化部署了agent模型，是Llama 3.1版本，与调API接口来比较，弊端还是挺多的，因为他的参数没有那么多，所以回答表现效果不是很好，正因为表现不好，才是RAG进行微调处理的时机。
![agent图片]()
# to ❀
我恨项目！！！！
