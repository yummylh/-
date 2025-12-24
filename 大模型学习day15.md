# 📅 [2025.12.24] 学习笔记：[leetcode刷题以及数据构建]
## 🎯 学习目标
- [x] 做了leetcode的排序链表
- [x] 干了一天的苦活，这就是洗数据构建数据的痛
## 📝 核心知识点 (Key Takeaways)
### 1.链表排序
分治思想，将链表从中间断开，然后分别对左右俩边断开的链表进行排序，左右排完序后，在进行链表合并，链表合并操作与昨天相同，那么这道题的做法也就是中间断表left排序，right排序，最后在合并排序
``` python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def sortList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        # 0. 【递归终止条件】
        # 如果链表为空，或者只有一个节点，那它已经是有序的了
        if not head or not head.next:
            return head
        # 1. 【找中点 & 切断】 (Cut)
        # 使用快慢指针找中点
        # ⚠️ 技巧：fast 从 head.next 开始，是为了防止只有 2 个节点时死循环
        # 比如 [4, 2]，fast在next，slow在4。这样 slow.next=None 才能把 4 和 2 分开
        slow = head
        fast = head.next
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
        # mid 是后半段的开头
        mid = slow.next
        # ⚠️ 关键操作：把链表从中间切断！
        slow.next = None
        # 2. 【递归排序】 (Sort)
        # 分别去排左半边和右半边
        left_sorted = self.sortList(head)
        right_sorted = self.sortList(mid)
        # 3. 【合并】 (Merge)
        # 将两个有序链表合并（复用 LeetCode 21 的逻辑）
        return self.merge(left_sorted, right_sorted)
        # --- 辅助函数：合并两个有序链表 ---
    def merge(self, l1:ListNode, l2:ListNode) -> ListNode:
        dummy = ListNode(0)
        curr = dummy
        while l1 and l2:
            if l1.val < l2.val:
                curr.next = l1
                l1 = l1.next
            else:
                curr.next = l2
                l2 = l2.next
            curr = curr.next
            
        if l1: curr.next = l1
        if l2: curr.next = l2
        
        return dummy.next    
        ```

### 3.agent进展
我只能说，本来下午很早就能完成的东西，结果电脑死了，execl没保存，自己构建的数据集，评价指标全没了，又重头来过
![数据图]()
# to ❀
虽然晚上很冷！但是一起吃好吃的开心！再坚持明天一天！后天就能粗去玩啦！
