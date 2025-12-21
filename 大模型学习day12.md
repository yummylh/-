# 📅 [2025.12.21] 学习笔记：[leetcode刷题以及agent部署]
## 🎯 学习目标
- [x] 做了leetcode的旋转图像，搜索二维矩阵
- [x] 成功实现了agent的工具增加与注册工具以及UI编写，提示词工程
## 📝 核心知识点 (Key Takeaways)
### 1.相交链表
一个非常有趣的解法，你走过的路我也走过，设相交点后链表为C，链表一相交点前长度lenA，链表二相交点前长度lenB，且假设lenA<lenB,则有链表A指针lenA+C+lenB，链表B指针，lenB+C+lenA相等
``` python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def getIntersectionNode(self, headA, headB):
        """
        :type head1, head1: ListNode
        :rtype: ListNode
        """
        # 0. 边界处理
        # 如果任何一个链表是空的，那肯定不可能相交
        if not headA or not headB:
            return None
        # 1. 初始化两个指针
        pA = headA
        PB = headB
        # 2. 核心循环：只要两个指针没相遇，就一直走
        # 如果相交，会在交点相遇 (pA == pB == 交点)
        # 如果不相交，会在最后同时变成 None (pA == pB == None) 退出循环
        while pA != PB:
            # --- 指针 A 的逻辑 ---
            # 如果 pA 走到头了 (None)，就跳到 headB 继续走
            # 否则，继续往下一个节点走
            if pA is None:
                pA = headB
            else:
                pA = pA.next
        # --- 指针 B 的逻辑 ---
            # 如果 pB 走到头了 (None)，就跳到 headA 继续走
            # 否则，继续往下一个节点走
            if PB is None:
                PB = headA
            else:
                PB = PB.next
        return pA
```
### 2.反转链表
链表与数组不同，是由当前val值与指向下一个节点的node组成，我们不知道链表长度到底是多少，所以这题关键是，让后面一个人的“手”牵前一个人的“手”
``` python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution(object):
    def reverseList(self, head):
        """
        :type head: Optional[ListNode]
        :rtype: Optional[ListNode]
        """
        # 1. 初始化两个指针
        # prev: 指向“已经反转好的链表”的头，初始为 None
        # curr: 指向“当前正在处理”的节点，初始为 head
        prev = None
        curr = head
        # 2. 遍历链表
        while curr is not None:
            # A. 【备份】
            # 因为我们要修改 curr.next，改了之后就找不到原来的下一个节点了
            # 所以必须先暂时存起来
            next_Node = curr.next
            # B. 【反转】
            # 核心动作：将当前节点的指针指向前一个节点
            curr.next = prev
            # C. 【推进】
            # 两个指针整体向前移动一步 
            prev = curr  # prev 追上 curr
            curr = next_Node # curr 追上刚才备份的 next_node
        return prev
```
### 3.回文链表
快慢指针+只要理解后面的链表反转后与前面链表相同就表示这个链表是回文链表即可
``` python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution(object):
    def isPalindrome(self, head):
        """
        :type head: Optional[ListNode]
        :rtype: bool
        """
        # 0. 边界处理
        if not head or not head.next:
            return True
        # 1. 【找中点】：快慢指针法
        # slow 最终会指向“后半部分的起始点”
        slow = head 
        fast = head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
        # 2. 【反转后半部分】
        # 这里直接复用上一题“反转链表”的逻辑
        curr = slow
        prev = None
        while curr:
            next_node = curr.next
            curr.next = prev
            prev = curr
            curr = next_node
            # 3. 【前后对比】
        # left 指针从原链表头出发
        # right 指针从后半段的头（prev）出发
        left = head
        right = prev    
        while right:# 只需要判断 right 没走完即可（因为前半段可能比后半段长一个节点
            if left.val != right.val:
                return False
            left = left.next
            right = right.next    
        return True
```
### 4.环形链表
经典快慢指针（龟兔赛跑），如果这个链表是环形链表，那么兔子一定会套乌龟的圈，反之兔子永远不可能套圈
``` python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def hasCycle(self, head):
        """
        :type head: ListNode
        :rtype: bool
        """
        # 0. 【边界处理】
        # 如果链表为空，或者只有一个节点（肯定没环），直接返回 False
        if not head or not head.next:
            return False
        # 1. 【初始化指针】
        # slow 每次走 1 步
        # fast 每次走 2 步
        slow = head
        fast = head
        # 2. 【核心逻辑：龟兔赛跑】
        # 只要兔子还能跑（没到尽头），就一直跑
        # 必须同时检查 fast 和 fast.next，防止空指针报错
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            # 3. 【判断相遇】
            # 如果内存地址一样，说明追上了，肯定有环
            if fast == slow:
                return True
        return False        
```
### 5.agent部署
搭积木真好玩，其实就是tool pormpt UI
AGENT图片如下所示：
![agent]()
