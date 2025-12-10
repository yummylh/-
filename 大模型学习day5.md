# 📅 [2025.12.10] 学习笔记：[leetcode刷题以及transfomer中tokenizer的编写]
## 🎯 学习目标
- [x] 做了leetcode的三数之和以及盛最多水的容器
- [x] 实现了transformer架构中的分词器
## 📝 核心知识点 (Key Takeaways)
### 1. [leetcode：盛最多的水]
核心思想是双指针，一个从nums数组头一个从nums数组尾相向而行，二者遇到后即遍历完整个数组。
* 值得注意的是，在实现过程中需要将较高的height留下，移动较小的height部分，即可找到能盛最多雨水的俩个元素。
![思考过程图]()
