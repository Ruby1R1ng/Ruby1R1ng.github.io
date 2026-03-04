# Leetcode Hot100 

## 一、哈希表

### 1. 两数之和 (Two Sum)

#### 题目描述

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值 `target`** 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

#### 示例

```text
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9，返回 [0,1]。
```

#### 解题思路

使用哈希表（字典）存储已遍历的元素及其索引。对于每个元素，计算目标值与当前元素的差值，检查该差值是否已存在于哈希表中。如果存在，则找到了一对数；否则，将当前元素及其索引存入哈希表。

字典就是一种“键值对”的数据结构，例如：
```text
{
    2: 0,
    7: 1
}
```
这里的 num_map 用来记录：“某个数字出现过，并且它出现在哪个位置”
```python
for index, num in enumerate(nums):
```
其中index是这个位置的下标，num是这个位置的值。
```python
return [num_map[complement], index]
```
找到答案就返回两个下标。如果没找到，就把当前数字记下来
```python
num_map[num] = index
```
例如第一次遍历到 2 时：`num_map[2] = 0`，字典变成：`{2: 0}`

#### 复杂度分析

- 时间复杂度：`O(n)`，其中 `n` 是数组的长度。每个元素最多被访问一次。
- 空间复杂度：`O(n)`，用于存储哈希表中的元素。

#### Python 解答

```python
def twoSum(self, nums: List[int], target: int) -> List[int]:
    nums_map={}
    for index, num in enumerate(nums):
        complement = target - num
        if complement in nums_map:
            return [nums_map[complement], index]
        nums_map[num]=index
    return []
```
#### 菲菲の思考

为什么这里不用`defaultdict`，因为我们想看complement在不在字典中，所以需要判断key在不在这一步，而`defaultdict`如果你访问一个不存在的键，它会自动创建默认值为空，就达不到我们想要的效果。

### 49. 字母异位词分组(Group Anagrams)

#### 题目描述

给你一个字符串数组，请你将 字母异位词 组合在一起。可以按任意顺序返回结果列表。

字母异位词 是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。

#### 示例

```text
输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
输出: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

#### 解题思路

##### 方法一：使用哈希表

1.对于每个字符串，将其排序后的结果作为键

2.将具有相同键的字符串分组

3.返回所有分组

`defaultdict`可以理解成一种更方便的字典，普通字典如果某个键还不存在，直接用会报错。但 `defaultdict(list)` 会自动给你准备一个空列表。

如果某个键还没出现过：

```python
groups["aet"]
```
它不会报错，而是自动变成：
```python
groups["aet"] = []
```
这样你就可以直接追加：
```python
groups["aet"].append("eat")
```

`sorted(s)` 的结果不是字符串，而是一个“字符列表”。要先用 join，把它重新拼成字符串，才能方便当作字典的键来分组。

`groups.values()`不是普通列表，而是一个 dict_values 视图对象，所以`list(groups.values())`把它转换成真正的列表


##### 方法二：使用26个字母计数

时间复杂度主要出在排序上`O(k log k)`，如果改成统计每个字符串中出现的26个字母次数，就是O(k)

#### 复杂度分析

- 时间复杂度：`O(nk log k)`，其中 `n` 是字符串数组的长度，k 是字符串的最大长度。外层循环跑了 n 次，每次循环里 sorted(s) 排序的时间复杂度是：O(k log k)，join是`O(k)`，append是`O(1)`，小于O(k log k)

- 空间复杂度：`O(nk)`，用于存储哈希表和结果

#### Python 解答

方法一：

```python
def groupAnagrams(strs):
    from collections import defaultdict
    
    groups = defaultdict(list)
    
    for s in strs:
        key = ''.join(sorted(s))
        groups[key].append(s)
    
    return list(groups.values())
```

方法二：

```python
from collections import defaultdict

def groupAnagrams(strs):
    groups = defaultdict(list)
    
    for s in strs:
        count = [0] * 26   # 26个字母的计数器
        
        for ch in s:
            index = ord(ch) - ord('a')
            count[index] += 1
        
        key = tuple(count)   # 列表不能当字典键，所以转成元组
        groups[key].append(s)
    
    return list(groups.values())
```

`ord(ch)`把一个字符转换成它对应的数字编码。比如：`ord('a')`结果是97

### 128. 最长连续序列 (Longest Consecutive Sequence)

#### 题目描述

给定一个未排序的整数数组 nums，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。

请你设计并实现时间复杂度为 O(n) 的算法解决此问题。

#### 示例

```text
输入：nums = [100,4,200,1,3,2]
输出：4
解释：最长数字连续序列是 [1, 2, 3, 4]。它的长度为 4。
```

#### 解题思路

- 将所有数字存入哈希集合
  
- 对于每个数字，如果它是连续序列的起点（即 num-1 不在集合中），则从该数字开始计算连续序列的长度
  
- 更新最长连续序列的长度

把“列表改成哈希表来查”通常直接用 set() 就够了，set 本质上就是基于哈希实现的集合，查找平均时间复杂度是 O(1)

这里真正需要的是：快速判断一个数`在不在`集合里，不需要记录“键对应的值”

如果你直接在列表里判断`if x in nums`是线性查找，时间复杂度是 O(n)，换成 set 后，这些判断都变成平均 O(1)

#### 复杂度分析

- 时间复杂度：`O(n)`，其中 `n` 是数组的长度。每个元素最多被访问一次。
- 空间复杂度：`O(n)`，用于存储哈希表中的元素。

#### Python 解答

```python
def longestConsecutive(nums):
    if not nums:
        return 0
    
    num_set = set(nums)
    longest = 0
    
    for num in num_set:
        if num - 1 not in num_set:
            current_num = num
            current_length = 1
            
            while current_num + 1 in num_set:
                current_num += 1
                current_length += 1
            
            longest = max(longest, current_length)
    
    return longest
```

#### 菲菲の思考
我写的：
```python
    for num in groups:
        if num - 1 in groups:
            continue

        temp = 1
        while num + 1 in groups:
            temp += 1
            num += 1
```

## 二、双指针

### 283. 移动零 (Move Zeroes)

#### 题目描述

给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

注意 必须在不复制数组的情况下原地对数组进行操作。

#### 示例

```text
输入: nums = [0,1,0,3,12]
输出: [1,3,12,0,0]
```

#### 解题思路

使用双指针：

- 使用一个指针 left 指向当前应该放置非零元素的位置
- 遍历数组，遇到非零元素就将其放到 left 位置，然后 left++

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是数组的长度
- 空间复杂度：O(1)，只需要常数的额外空间

#### Python 解答

```python
def moveZeroes(nums):
    left = 0
    
    # 将所有非零元素移到前面
    for right in range(len(nums)):
        if nums[right] != 0:
            nums[left], nums[right] = nums[right], nums[left]
            left += 1
```
#### 菲菲の思考

右指针先走，去找非0值

### 11. 盛最多水的容器 (Container With Most Water)

#### 题目描述

给定一个长度为 n 的整数数组 height。有 n 条垂线，第 i 条线的两个端点是 (i, 0) 和 (i, height[i])。

找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

#### 示例

<img width="821" height="528" alt="image" src="https://github.com/user-attachments/assets/7596df30-a370-4be0-b8b4-e0f48a4bce08" />

#### 解题思路

1. 暴力遍历
```python
def maxArea(self, height: List[int]) -> int:
    max_area=0
    for index,num in enumerate(height):
        for j in range(index+1,len(height)):
            height_min=min(height[index],height[j])
            area = (j-index)*height_min
            max_area = max(max_area,area)
    return max_area
```
2. 使用双指针法：

- 初始化左右指针分别指向数组的两端
  
- 计算当前容器的容量：min(height[left], height[right]) * (right - left)
  
- 更新最大容量
  
- 移动较短的那一侧指针（因为移动较长的一侧不会增加容量）
  
- 重复直到左右指针相遇

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是数组的长度。双指针最多遍历整个数组一次
- 空间复杂度：O(1)，只需要常数的额外空间

#### Python 解答

```python
    def maxArea(self, height: List[int]) -> int:
        left=0
        right=len(height)-1
        max_area=0
        while left!=right:
            area=min(height[left],height[right])*(right-left)
            max_area = max(max_area,area)
            if height[left] >= height[right]:
                right-=1
            else:
                left+=1
        return max_area
```

### 15.三数之和 (3Sum)

#### 题目描述

给你一个整数数组 nums，判断是否存在三元组 [nums[i], nums[j], nums[k]] 满足 i != j、i != k 且 j != k，同时还满足 nums[i] + nums[j] + nums[k] == 0。请你返回所有和为 0 且不重复的三元组。

注意： 答案中不可以包含重复的三元组。

#### 示例

```text
输入：nums = [-1,0,1,2,-1,-4]
输出：[[-1,-1,2],[-1,0,1]]
解释：
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 。
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 。
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 。
不同的三元组是 [-1,0,1] 和 [-1,-1,2] 。
注意，输出的顺序和三元组的顺序并不重要。
```

#### 解题思路

- 首先对数组进行排序

- 固定第一个数，使用双指针在剩余部分寻找另外两个数

- 使用双指针法：左指针指向固定数的下一个位置，右指针指向数组末尾

- 根据三数之和与 0 的关系移动指针

- 注意跳过重复元素以避免重复解

#### 复杂度分析

- 时间复杂度：O(n²)，其中 n 是数组的长度。排序 O(n log n) + 双指针遍历 O(n²)
- 空间复杂度：O(1)，除了存储答案的空间外，只需要常数的额外空间

#### Python 解答

```python
def threeSum(nums):
    nums.sort()
    res = []
    n = len(nums)
    
    for i in range(n - 2):
        # 跳过重复元素
        if i > 0 and nums[i] == nums[i - 1]:
            continue
        
        left, right = i + 1, n - 1
        while left < right:
            total = nums[i] + nums[left] + nums[right]
            if total < 0:
                left += 1
            elif total > 0:
                right -= 1
            else:
                res.append([nums[i], nums[left], nums[right]])
                # 跳过重复元素
                while left < right and nums[left] == nums[left + 1]:
                    left += 1
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1
                left += 1
                right -= 1
    
    return res
```
#### 小菲の思考

i 是第一个数，left 是第二个数，right 是第三个数，所以必须有i <= n - 3

`i != j、i != k 且 j != k` 表示：同一个位置不能被用两次,而不是不能有相同的值

`nums[i] == nums[i - 1]` 判断当前元素是不是和前一个重复,一定要与前一个比较！ `[-1, 0, 1, 2, -1, -4]`排序后变成`[-4, -1, -1, 0, 1, 2]`

`while left < right and nums[left] == nums[left + 1]`表示当前已经找到一个解了，如果左指针后面还是同样的值，就一直跳过。

### 42. 接雨水 (Trapping Rain Water)

#### 题目描述

给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

#### 示例

<img width="729" height="272" alt="image" src="https://github.com/user-attachments/assets/5a99a511-dd6d-4c52-8c35-a17cb1b7474e" />

#### 解题思路

方法1：单调栈

使用单调递减栈，当遇到更高的柱子时，计算能接的雨水

栈（stack）就是一种后进先出的数据结构。你可以把它想成一摞盘子：新盘子只能放在最上面push，拿盘子也只能从最上面拿pop

在“接雨水”这题里，栈里放的不是高度本身，而是：柱子的下标（index）。单调递减栈的意思是：从栈底到栈顶，对应的高度是递减的（越来越小）

- 维护一个单调递减栈，栈里存的是下标

- 当遇到一个更高的柱子时，说明可能形成“凹槽”

- 弹出栈顶作为“底部”，再用当前柱子和新的栈顶作为左右边界，计算这部分能接的雨水

举例：
```text
高度:  4   2   0   3
位置:  0   1   2   3
```
扫描到位置 3（高度 3）时，栈里可能是：
```text
[0, 1, 2]
对应高度 [4, 2, 0]
```
现在 3 > 0，弹出 2：2（高度 0）是坑底，新栈顶 1（高度 2）是左边界，当前 3（高度 3）是右边界，形成一个坑：

```text
#
#     #
# #~~~#
# #~~~#
-----------
0 1 2 3
```
继续判断：因为当前高度 3 还大于新的栈顶高度 2

现在：

底部 = 1

左边界 = 0（高度 4）

右边界 = 3（高度 3）

```text
#     |
#~~~~~#
# #~~~#
# #~~~#
---------
0 1 2 3
```

方法2：双指针

- 使用左右指针，维护左右两边的最大高度
  
- 对于每个位置，能接的雨水 = min(left_max, right_max) - height[i]
  
- 移动较小的一边

#### 复杂度分析

- 时间复杂度：O(n)
- 空间复杂度：O(1)（双指针）或 O(n)（单调栈）

#### Python 解答

```python
# 方法一：单调递减栈
def trap(self, height: List[int]) -> int:
    stack = []   # 存下标，保持对应高度单调递减
    water = 0

    for i in range(len(height)):
        while stack and height[i] > height[stack[-1]]:
            bottom = stack.pop()   # 凹槽底部

            if not stack:
                break   # 左边没有边界了，无法接水

            left = stack[-1]       # 左边界
            width = i - left - 1   # 宽度
            h = min(height[left], height[i]) - height[bottom]  # 有效高度

            water += width * h

        stack.append(i)

    return water

# 方法二：双指针
def trap(height):
    if not height:
        return 0
    
    left, right = 0, len(height) - 1
    left_max, right_max = 0, 0
    water = 0
    
    while left < right:
        if height[left] < height[right]:
            if height[left] >= left_max:
                left_max = height[left]
            else:
                water += left_max - height[left]
            left += 1
        else:
            if height[right] >= right_max:
                right_max = height[right]
            else:
                water += right_max - height[right]
            right -= 1
    
    return water
```
#### 小菲の思考
在 Python 里，“栈”通常就是用列表来实现的。因为 Python 的 list 支持这些操作：

- 在末尾添加元素：append()

- 删除末尾元素：pop()

而栈正好就是：

- 从顶部放入元素

- 从顶部取出元素

如果我们约定列表的末尾就是栈顶,那它就完全可以模拟栈。注意，Python 的 list 没有叫 push() 的方法，“往末尾加元素”统一叫：append()

stack[-1] 确实是栈里最后压进去、当前位于栈顶的那个元素。但在这道题的这一刻，它恰好表示的是：被弹出的坑底左边，离它最近、还没被弹走的那根柱子，所以它是左边界。height[stack[-1]]取位置对应的高度

pop以后需要检查还有没有stack


## 报错类型总结

### TypeError: Type List cannot be instantiated; use list() instead
把 `List` 当成“真正的列表”去创建了，但 `List` 只是类型标注，不能拿来直接生成对象

`List`是拿来写类型提示的，比如：
```python
def f(nums: List[int]) -> List[str]:
    ...
```
这里的 `List[int]` 只是告诉你`nums` 应该是“整数列表”，返回值应该是“字符串列表”，不是真正创建列表的工具

### TypeError: 'builtin_function_or_method' object is not iterable  return list(groups.values)

`groups.values`是方法本身，还没有执行。加上括号后，才表示调用这个方法，真正把字典里的所有值取出来


## 参考的学习网址

https://yukinoshitasherry.github.io/Leetcode/
