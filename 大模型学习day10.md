# 📅 [2025.12.11] 学习笔记：[leetcode刷题以及transfomer中损失函数和反向传播的编写]
## 🎯 学习目标
- [x] 做了leetcode的轮转数组，除自身以外数组乘积，矩阵置0，螺旋矩阵
- [x] 在服务器云端部署了transformer
## 📝 核心知识点 (Key Takeaways)
### 1.轮转数组，其实就是三次排序，详细见代码
```python
from typing import List
class Solution:
    def rotate(self, nums: List[int], k: int) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        n = len(nums)
        # 0. 【边界处理】
        # 如果 k 大于数组长度，取余数。
        # 比如长度 5，轮转 6 次 等于 轮转 1 次。
        k = k % n
        
        # 如果 k 为 0，不需要轮转
        if k == 0:
            return

        # 定义一个内部辅助函数：翻转 nums 中从 start 到 end 的部分
        # 这是一个标准的双指针写法
        def reverse_part(start: int, end: int):
            while start < end:
                # 交换左右两边的值
                nums[start], nums[end] = nums[end], nums[start]
                # 指针向中间靠拢
                start += 1
                end -= 1

        # 1. 【整体翻转】
        # 将整个数组倒序
        # 变成 [7, 6, 5, 4, 3, 2, 1]
        reverse_part(0, n - 1)
        
        # 2. 【翻转前半部分】
        # 将前 k 个元素（0 到 k-1）翻转回来
        # 变成 [5, 6, 7, 4, 3, 2, 1]
        reverse_part(0, k - 1)
        
        # 3. 【翻转后半部分】
        # 将剩下的元素（k 到 n-1）翻转回来
        # 变成 [5, 6, 7, 1, 2, 3, 4]
        reverse_part(k, n - 1)

### 2.除自身以外数组乘积
其实就是前缀积，与后缀积
```python
from typing import List
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:
        n = len(nums)
        # 0. 【初始化结果数组】
        # 先把 answer 数组填满 0 (或者 1 也可以，反正会被覆盖)
        answer = [0] * n 
        # 1. 【第一轮：计算左侧乘积】
        # answer[i] 将表示：i 左边所有元素的乘积
        
        # 索引 0 的左边没有元素，所以乘积初始化为 1
        answer[0] = 1
        for i in range(1,n):
            # 当前位置的左侧积 = 上一个位置的左侧积 * 上一个位置的数值
            answer[i] = answer[i-1] * nums[i-1]
            # 此时 answer = [1, 1, 2, 6] (对应示例 [1, 2, 3, 4])
            
            # 2. 【第二轮：计算右侧乘积并直接得到最终结果】
            # R 是一个滚动变量，代表“当前位置右边所有元素的乘积”

        R = 1
        # 从最后一个元素倒着往前遍历 (n-1 -> 0)
        for i in reversed(range(n)):
            # 最终结果 = 左侧积 (已经在answer里) * 右侧积 (R)
            answer[i] = answer[i] * R 
            # 更新 R，把当前遍历过的 nums[i] 乘进去，给下一个位置用
            R = R * nums[i]
        return answer

### 3.矩阵置0
需要用到第一行第一列打上标记，并且最后在处理bool变量处理第一行第一列
```python
from typing import List
class Solution:
    def setZeroes(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        m = len(matrix)
        n = len(matrix[0])
        #得到了矩阵的行和列
        # 1. 【状态记录】
        # 用两个变量单独记录第一行和第一列是否原本包含 0
        row0_has_zero = False
        col0_has_zero = False
        # 检查第一列
        for i in range(m):
            if matrix[i][0] == 0:
                col0_has_zero = True
                break
        # 检查第一行
        for i in range(n):
            if matrix[0][i]==0:
                row0_has_zero = True
                break
        # 2. 【核心逻辑：遍历并标记】
        # 从 (1,1) 开始遍历内部矩阵，利用第一行和第一列做标记
        for i in range(1, m):
            for j in range(1, n):
                if matrix[i][j] == 0:
                    matrix[0][j] = 0#将列头打上标记，赋值为0
                    matrix[i][0] = 0#将行头打上标记，赋值为0
        # 3. 【置零】
        # 根据刚才做的标记，将内部矩阵置零
        # 注意：依然是从 (1,1) 开始，不动第一行第一列
        for i in range(1,m):
            for j in range(1,n):
                if matrix[i][0]==0 or matrix[0][j]==0:
                    matrix[i][j] = 0
        # 4. 【收尾】
        # 如果第一行原本有 0，就把第一行全变 0
        if row0_has_zero:
            for j in range(n):
                matrix[0][j] = 0
        # 如果第一列原本有 0，就把第一列全变 0
        if col0_has_zero:
            for i in range(m):
                matrix[i][0] = 0

                                    
### 4.螺旋矩阵
四个下标，并且及时更新边界条件就行
```python
from typing import List
class Solution:
    def spiralOrder(self, matrix: List[List[int]]) -> List[int]:
        # 0. 【边界处理】
        if not matrix:
            return []
        # 1. 【初始化边界变量】
        # top, bottom: 定义上、下边界的行索引
        # left, right: 定义左、右边界的列索引
        top = 0
        bottom = len(matrix) - 1
        left = 0 
        right = len(matrix[0]) - 1
        res = []
        # 2. 【核心逻辑：螺旋遍历】
        # 只要上下没交错、左右没交错，就继续转圈
        while top <= bottom and left <= right:
            # --- A. 向右移动 (Upper Wall) ---
            # 遍历当前的最上面一行：从 left 到 right
            for i in range(left, right+1):
                res.append(matrix[top][i])
            top += 1
            # --- B. 向下移动 (Right Wall) ---
            # 遍历当前的最右边一列：从 top 到 bottom
            for i in range(top, bottom+1):
                res.append(matrix[i][right])
            right -= 1
            # --- C. 向左移动 (Bottom Wall) ---
            # ⚠️ 关键检查：必须确保还有行可以遍历
            # 因为步骤 A 此时已经修改了 top，如果 matrix 只有一行，
            # 这里如果不检查，会重复遍历这一行
            if top <= bottom:
                # 注意 range 是左闭右开，且步长为 -1 (倒序)
                # 从 right 遍历到 left
                for i in range(right, left-1, -1):
                    res.append(matrix[bottom][i])
                bottom -= 1
            # --- D. 向上移动 (Left Wall) ---
            # ⚠️ 关键检查：必须确保还有列可以遍历
            # 防止单列矩阵重复遍历
            if left <= right:
                for i in range(bottom, top-1, -1):
                    res.append(matrix[i][left])
                left += 1
        return res


### 5.transformer
b部署到云端了，但是有问题，blue老是为0，还在排查
## TO ❀
今天有宝贝子陪着我学习！开心捏！这是痛苦日子里唯一有盼头的了qaq
