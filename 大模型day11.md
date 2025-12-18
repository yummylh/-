# 📅 [2025.12.18] 学习笔记：[leetcode刷题以及agent部署]
## 🎯 学习目标
- [x] 做了leetcode的旋转图像，搜索二维矩阵
- [x] 在服务器云端部署了正常跑起了transformer，成功部署了第一个agent
## 📝 核心知识点 (Key Takeaways)
### 1.旋转图像
先将元素沿对角线调换位置（完成矩阵转置），按照每一行逆排序即可得到旋转后的图像。
``` python
class Solution(object):
    def rotate(self, matrix):
        """
        :type matrix: List[List[int]]
        :rtype: None Do not return anything, modify matrix in-place instead.
        """
        n = len(matrix)
        # 1. 【第一步：转置矩阵 (Transpose)】
        # 也就是沿对角线交换元素：matrix[i][j] <-> matrix[j][i]
        for i in range(n):
            # ⚠️ 关键点：j 从 i 开始遍历
            # 为什么？因为如果 j 从 0 开始，会导致交换两遍（换过去又换回来），等于没变。
            # 我们只需要处理对角线右上方及其对角线上的元素即可。
            for j in range(i,n):
                matrix[i][j] , matrix[j][i] = matrix[j][i] , matrix[i][j]
                # 此时矩阵变成了：
        # [[1, 4, 7],
        #  [2, 5, 8],
        #  [3, 6, 9]]
        # 2. 【第二步：左右翻转 (Reverse)】
        # 遍历每一行，直接调用列表的 reverse() 方法
        for row in matrix:
            row.reverse()
```
### 2.搜索二维矩阵II
有点像树的算法，因为矩阵给定了一定的规律，所以我们只要按照一定的方向性去搜索即可，不用按行或者按列依次搜索
```python
class Solution(object):
    def searchMatrix(self, matrix, target):
        """
        :type matrix: List[List[int]]
        :type target: int
        :rtype: bool
        """
        if not matrix or not matrix[0]:
            return False
            # 1. 【初始化指针】
        # m: 行数, n: 列数
        m = len(matrix)
        n = len(matrix[0])
        # 选定“右上角”作为起始点
        # row: 当前行索引 (从 0 开始)
        # col: 当前列索引 (从最后一列开始)
        row = 0
        col = n-1
        # 2. 【核心逻辑：Z字形搜索】
        # 只要没有走出矩阵的边界（行没到底，列没到最左），就继续找
        while row < m and col >= 0:
            current_val = matrix[row][col]
            # --- 情况 A: 找到目标 ---
            if current_val == target:
                return True
                # --- 情况 B: 当前值太大 ---
            # 因为列是升序的，当前值已经是这一列最小的了
            # 如果它都比 target 大，那这一列下面的所有数肯定都比 target 大
            # 所以整个这一列都可以排除了 -> 向左移动
            elif current_val > target:
                col -= 1
            # --- 情况 C: 当前值太小 ---
            # 因为行是升序的，当前值已经是这一行最大的了（因为我们是从右往左走的）
            # 如果它都比 target 小，那这一行左边的所有数肯定都比 target 小
            # 所以整个这一行都可以排除了 -> 向下移动
            else:
                row += 1
        return False
```
### 3.chaos_agent
今天调用deepseek-chat基座模型搭建出了一个简易的agent，下一步就是根据rog LoRA对他进行微调了，希望进展一切顺利
# to ❀
md干项目好烦，好折磨人，我感觉我真的快鼠掉了。
