---
title: "Leetcode Hot100"
---

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

```text
  高度

2 |          █  <- right=3
1 |     █    █
0 |  █  █  █ █
    ------------
     0  1  2  3
     ^
   left=0
右边有墙，不会从右边漏掉
=> 左边这格能不能接水，只要看 left_max

2 |          █  <- right=3
1 |     █    █
0 |  █  █  █ █
    ------------
     0  1  2  3
        ^
      left=1

1 < 2，所以还是处理左边。

2 |          █  <- right=3
1 |     █    █
0 |  █  █  █ █
    ------------
     0  1  2  3
           ^
         left=2
0 < 2
因此，这一格的水量就可以直接由左边最高墙决定：
water += left_max - height[left]
       = 1 - 0
       = 1
```

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
def trap(self, height: List[int]) -> int:
    if not height:
        return 0

    left, right = 0, len(height) - 1
    left_max, right_max = 0, len(height) - 1   # 存下标
    water = 0

    while left < right:
        if height[left] < height[right]:
            if height[left] >= height[left_max]: 
                left_max = left
            else:
                water += height[left_max] - height[left] # 最大挡板高度 - 当前高度
            left += 1
        else:
            if height[right] >= height[right_max]:
                right_max = right
            else:
                water += height[right_max] - height[right]
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

## 三、滑动窗口

### 3. 无重复字符的最长子串 (Longest Substring Without Repeating Characters)

#### 题目描述

给定一个字符串 s ，请你找出其中不含有重复字符的 最长 子串 的长度。

#### 示例

```text
输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

#### 解题思路

使用滑动窗口（双指针）+ 哈希集合：

- 使用两个指针表示滑动窗口的左右边界
- 使用哈希集合记录窗口内的字符 
- 右指针不断向右移动，如果遇到重复字符，移动左指针直到窗口内无重复字符
- 在移动过程中更新最大长度

#### 复杂度分析

- 时间复杂度：O(n)，其中 n 是字符串的长度。每个字符最多被访问两次（左指针和右指针各一次）
- 空间复杂度：O(min(n, m))，其中 m 是字符集的大小。哈希集合最多存储 min(n, m) 个字符

#### Python 解答

```python
def lengthOfLongestSubstring(s):
    char_set = set()
    left = 0
    max_length = 0
    
    for right in range(len(s)):
        # 如果遇到重复字符，移动左指针
        while s[right] in char_set:
            char_set.remove(s[left])
            left += 1
        
        char_set.add(s[right])
        max_length = max(max_length, right - left + 1)
    
    return max_length
```
#### 小菲の思考

不需要值的时候，直接用set，set添加元素不是append，而是add，删除不是pop而是remove

```python
while s[right] in char_set:
    char_set.remove(s[left])
    left += 1
```
意思是：

如果当前要加入的字符 s[right] 已经在窗口里了，说明出现重复。

所以要不断移动左边界，把左边的字符一个个移出去，直到这个重复字符不在窗口里为止。

### 438．找到字符串中所有字母异位词（Find All Anagrams in a String）

#### 题目描述

给定两个字符串 s 和 p，找到 s 中所有 p 的 异位词 的子串，返回这些子串的起始索引。不考虑答案输出的顺序。

#### 示例

```text
输入: s = "cbaebabacd", p = "abc"
输出: [0,6]
解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的异位词。
```

#### 解题思路

方法一：

从 s 的每一个可能起点出发，截取一个长度等于 p 的子串，判断这个子串是不是 p 的字母异位词。

方法二：

用一个长度固定为 len(p) 的窗口在 s 上滑动，实时维护窗口内字符频次；只要窗口频次和 p 的频次完全相同，这个窗口就是一个字母异位词。

#### 复杂度分析

方法一：
- 时间复杂度：O(nm)，其中 n 是字符串 s 的长度, m 是子串 p 长度, 外层一个循环，内层统计切片
- 空间复杂度：O(m)，切片的长度

方法二：
- 时间复杂度：O(n)
- 空间复杂度：O(1)，只用了固定长度 26 的数组

#### Python 解答

方法一：
```python
from collections import Counter

def findAnagrams(self, s: str, p: str) -> List[int]:
    res = []
    p_count = Counter(p)
    length = len(p)
    
    for i in range(len(s) - length + 1):
        if Counter(s[i:i + length]) == p_count:
            res.append(i)
    
    return res
```

方法二：
```python
def findAnagrams(self, s: str, p: str) -> List[int]:
    if len(p) > len(s):
        return []
    
    res = []
    p_count = [0] * 26
    window_count = [0] * 26
    
    for ch in p:
        p_count[ord(ch) - ord('a')] += 1
    
    left = 0
    for right in range(len(s)):
        window_count[ord(s[right]) - ord('a')] += 1
        
        if right - left + 1 > len(p):
            window_count[ord(s[left]) - ord('a')] -= 1
            left += 1
        
        if right - left + 1 == len(p):
            if window_count == p_count:
                res.append(left)
    
    return res
```
#### 小菲の思考

方法一：

`p_count = Counter(p)`统计 p 中每个字符出现的次数。`p = "abc"`，那么`Counter(p) = {'a': 1, 'b': 1, 'c': 1}`

`for i in range(len(s) - length + 1)`中的+1是因为取不到len(s) - length

比如：

s = "cbaebabacd"，长度 10

p = "abc"，长度 3

那么起点 i 最多只能到 7=10-3，但是range取不到7，所以必须+1

字符串切片要用 冒号 :，不是逗号 ,  s[i:i + length]

方法二：

每次循环，都把 s[right] 这个新字符加入窗口。
```python
for right in range(len(s)):
    window_count[ord(s[right]) - ord('a')] += 1
```
窗口长度超过 len(p) 要缩小,如果超过了 len(p)，说明窗口太大了，就要把左边的字符移出去：` window_count[ord(s[left]) - ord('a')] -= 1`

暴力法是对每个起点 i：截取一个长度为 m 的子串，重新统计这个子串的字符频次，再和 p 比较

也就是说，每移动一次窗口，都要把这 m 个字符重新数一遍。所以暴力法每次要花 O(m)，总共大约做 n 次，变成：O(nm)

滑动窗口法不是每次重算整个窗口，而是：右边进来一个字符：给它计数 +1，左边出去一个字符：给它计数 -1，也就是只更新两个字符的变化，而不是重建整个窗口统计。

## 四、子串

### 560．和为 K 的子数组（Subarray Sum Equals K）

#### 题目描述

给你一个整数数组 nums 和一个整数 k ，请你统计并返回 该数组中和为 k 的子数组的个数 。子数组是数组中元素的连续非空序列。

#### 示例

```text
输入：nums = [1,1,1], k = 2
输出：2
```

#### 解题思路

用哈希表记录“某个前缀和出现了多少次”

一边遍历数组，一边累加当前前缀和

每到一个位置，就查看 prefix_sum - k 是否在哈希表中

如果在，说明有对应数量的子数组和为 k

然后再把当前前缀和加入哈希表

#### 复杂度分析

- 时间复杂度：O(n)，数组长度为 n，只遍历数组一次，哈希表的查询和插入平均都是 O(1)
- 空间复杂度：O(n)，最坏情况下，每个前缀和都不同, 哈希表最多存 n+1 个前缀和

#### Python 解答

```python
def subarraySum(self, nums: List[int], k: int) -> int:
    count = 0
    prefix_sum = 0
    hashmap = {0: 1}
    
    for num in nums:
        prefix_sum += num
        if prefix_sum - k in hashmap:
            count += hashmap[prefix_sum - k]
        hashmap[prefix_sum] = hashmap.get(prefix_sum, 0) + 1
    
    return count
```
#### 小菲の思考

我一开始写的暴力解法逻辑是：从每个 left 出发，一直往右加，直到 prefix >= k 就停，但如果后面还有负数，继续往右走，可能又会重新等于 k，你却提前停了。
```python
def subarraySum(self, nums: List[int], k: int) -> int:
    number=0
    for left in range(len(nums)):
        prefix=0
        right = left
        while prefix < k and right<len(nums) :
            prefix+=nums[right]
            right +=1
            if prefix == k:
                number+=1
    return number
```

`hashmap = {0: 1}`用来记录：某个前缀和出现了多少次，`{前缀和 : 出现次数}`

```python
nums = [1,2,3]
k = 3
```
第一步`prefix_sum = 1`，我们查`prefix_sum - k = 1 - 3 = -2`，hashmap里没有。更新：`{0:1, 1:1}`

第二步`prefix_sum = 3`，我们查`prefix_sum - k = 3 - 3 = 0`，hashmap里有`0:1`，说明有 1个子数组和为 k。这个子数组是：`[1,2]`

如果hashmap = {}，没有`{0:1}`，程序认为 没有子数组和为3。很多前缀和题都要写这一句：`hashmap = {0:1}`

`count += hashmap[prefix_sum - k]` 表示的是：前面有多少个前缀和等于 prefix_sum - k，就有多少个子数组以当前位置结尾、且和为 k。

字典的 get 方法：`dict.get(key, default)`，从字典 hashmap 里取 key 对应的值，如果没有这个键，就返回 0。

### 239．滑动窗口最大值（Sliding Window Maximum）

#### 题目描述

给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。

返回 滑动窗口中的最大值 。

#### 示例

```text
输入：nums = [1,3,-1,-3,5,3,6,7], k = 3
输出：[3,3,5,5,6,7]
解释：
滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

#### 解题思路

单调队列（双端队列）：希望队列里始终保存“当前窗口内可能成为最大值的元素下标”，并且让这些下标对应的值保持 从大到小单调递减。

队头永远是当前窗口最大值的下标

每次窗口右移时：

先把不在窗口内的下标从队头删掉

再把所有比当前元素小的下标从队尾删掉

把当前下标加入队尾

当窗口形成后，队头对应的值就是最大值

以：

```python
nums = [1,3,-1,-3,5,3,6,7], k = 3
```
为例。

i = 0, nums[i] = 1，队列空，直接加入 0
  
队列下标: [0]， 对应值: [1]

i = 1, nums[i] = 3，当前值 3 比队尾下标 0 对应值 1 大，说明 1 不可能再成为最大值，删掉。加入 1

队列下标: [1]，对应值: [3]

i = 2, nums[i] = -1，-1 小于队尾值 3，直接加入。

队列下标: [1,2]，对应值: [3,-1]，此时窗口形成 [0,2]，最大值就是队头对应的 3

i = 3, nums[i] = -3先检查队头是否过期。队头下标 1 还在窗口 [1,3] 内，不删。-3 小于队尾值 -1，直接加入。

队列下标: [1,2,3]，对应值: [3,-1,-3]，最大值还是 3

i = 4, nums[i] = 5先删过期元素：队头 1 已不在窗口 [2,4] 中，删掉。然后从队尾开始删比 5 小的：

删 3（值 -3）

删 2（值 -1）

加入 4

队列下标: [4]，对应值: [5]，最大值是 5

#### 复杂度分析

- 时间复杂度：O(n)，虽然有两个 while，但每个元素最多：进队一次，出队一次，所以总操作次数是线性的。
- 空间复杂度：O(k)，队列里最多保存当前窗口内的下标

#### Python 解答

```python
from collections import deque
def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
    q = deque()   # 存下标
    res = []

    for i in range(len(nums)):
        # 1. 删除已经滑出窗口的下标
        while q and q[0] < i - k + 1:
            q.popleft()

        # 2. 维持单调递减队列
        while q and nums[q[-1]] < nums[i]:
            q.pop()

        # 3. 当前下标入队
        q.append(i)

        # 4. 当窗口形成后，记录最大值
        if i >= k - 1:
            res.append(nums[q[0]])

    return res
```
#### 小菲の思考

双指针通常适合处理这类问题：区间和、区间长度、满足某种单调性质的区间，因为左右指针移动时，区间信息可以比较容易更新。但是“区间最大值”不一样，删除左边一个数时可能删掉的是最大值

deque() 是 Python 里的 双端队列。两端都可以高效地插入和删除的队列。

普通队列一般是：一端进一端出

但 deque 可以：左边插入、左边删除、右边插入、右边删除，所以叫“双端队列”。

常用操作：

右边加入：`q.append(3)`，左边加入：`q.appendleft(3)`，右边删除：`q.pop()`，右边删除：`q.popleft()`

因为滑动窗口最大值这题需要：队尾不断删掉较小元素，队头删掉已经过期的元素，队尾加入新元素，也就是同时要操作两端。所以 deque 非常适合。

### 76．最小覆盖子串（Minimum Window Substring）

#### 题目描述

给定两个字符串 s 和 t，长度分别是 m 和 n，返回 s 中的最短窗口子串，使得该子串包含 t 中的每一个字符（包括重复字符）。如果没有这样的子串，返回空字符串 ""。

#### 示例

```text
输入：s = "ADOBECODEBANC", t = "ABC"
输出："BANC"
解释：最小覆盖子串 "BANC" 包含来自字符串 t 的 'A'、'B' 和 'C'。
```

#### 解题思路

用两个指针 left 和 right 表示一个窗口 [left, right]，不断移动右指针扩大窗口，直到窗口已经包含了 t 所有需要的字符；然后再移动左指针缩小窗口，尽量把窗口缩到最短。

#### 复杂度分析

- 时间复杂度：时间复杂度：O(m + n), 其中m = len(s), n = len(t)统计 t 需要 O(n)，右指针 right 最多走 m 次，左指针 left 也最多走 m 次，虽然有 while，但左右指针总共都只会各走一遍，所以总复杂度是：
- 空间复杂度：O(k)，其中 k 是字符种类数。我们用了两个哈希表：need、window，它们存储的是字符计数

#### Python 解答

```python
from collections import defaultdict

class Solution:
    def minWindow(self, s: str, t: str) -> str:
        if len(t) > len(s):
            return ""

        need = defaultdict(int)
        window = defaultdict(int)

        # 统计 t 中每个字符需要的次数
        for ch in t:
            need[ch] += 1

        left = 0
        valid = 0

        start = 0          # 记录最短子串起点
        min_len = float('inf')   # 记录最短子串长度

        # right 表示将要加入窗口的字符位置
        for right in range(len(s)):
            c = s[right]

            # 扩大窗口：加入右边字符
            if c in need:
                window[c] += 1
                if window[c] == need[c]:
                    valid += 1

            # 当窗口已经覆盖 t 时，开始收缩左边界
            while valid == len(need):
                # 更新最短子串
                if right - left + 1 < min_len:
                    start = left
                    min_len = right - left + 1

                d = s[left]
                left += 1

                # 左边字符离开窗口
                if d in need:
                    if window[d] == need[d]:
                        valid -= 1
                    window[d] -= 1

        return "" if min_len == float('inf') else s[start:start + min_len]
```

小菲自己的版本：

```python
class Solution:
    def minWindow(self, s: str, t: str) -> str:
        from collections import Counter
        window = Counter()
        need = Counter(t)
        left = 0
        start = 0
        min_len = float("inf")

        for right in range(len(s)):
            ch = s[right]
            if ch in need:
                window[ch] += 1

            while window >= need:
                if right - left + 1 < min_len:
                    min_len = right - left + 1
                    start = left

                d = s[left]
                left += 1
                if d in need:
                    window[d] -= 1

        return "" if min_len == float("inf") else s[start:start + min_len]
```
#### 小菲の思考

我采用的是counter直接计数每个字符出现的个数，只有counter可以比较 window >= need，意思是对 need 中每个字符，都有 `window[ch] >= need[ch]`，defaultdict不行

## 五、普通数组

### 53. 最大子数组和（Maximum Subarray）

#### 题目描述：

给你一个整数数组 `nums`，请你找出一个具有最大和的**连续子数组**（子数组最少包含一个元素），返回其最大和。

**子数组** 是数组中的一个连续部分。

#### 示例：

```text
输入：nums = [-2,1,-3,4,-1,2,1,-5,4]
输出：6
解释：连续子数组 [4,-1,2,1] 的和最大，为 6。

#### 解题思路：

<img width="1284" height="1015" alt="image" src="https://github.com/user-attachments/assets/f3d6bc91-e5aa-4d6b-aa8d-02138d4c2269" />

每次多一个元素的时候，其实只需要计算以当前这个元素结尾的子数组和的最大值就可以了，也就是每多一个元素，就可以把计算分为两部分，将以这个元素结尾的最大子数组和（之前的全局最大子数组和+该数字本身/该数字本身）和 与 之前的全局最大子数组和比较。

<img width="842" height="369" alt="image" src="https://github.com/user-attachments/assets/6c7397ac-d450-476e-9b0a-35998e943501" />

定义dp[i]为以当前元素结尾的最大子数组和

动态规划（Kadane 算法）：

1. 定义 `dp[i]` 为以第 i 个元素结尾的最大子数组和
2. 状态转移方程：`dp[i] = max(nums[i], dp[i-1] + nums[i])`
3. 可以优化空间复杂度，只使用一个变量

#### 复杂度分析：

- 时间复杂度：O(n)，其中 n 是数组的长度
- 空间复杂度：O(1)，只需要常数的额外空间（优化后）

#### Python 解答：

```python
def maxSubArray(nums):
    max_sum = current_sum = nums[0]
    
    for i in range(1, len(nums)):
        current_sum = max(nums[i], current_sum + nums[i])
        max_sum = max(max_sum, current_sum)
    
    return max_sum
```
#### 小菲の思考

这道题还不能用滑动窗口算法，因为数组中的数字可以是负数。

滑动窗口算法无非就是双指针形成的窗口扫描整个数组/子串，但关键是，你得清楚地知道什么时候应该移动右侧指针来扩大窗口，什么时候移动左侧指针来减小窗口。

而对于这道题目，当窗口扩大的时候可能遇到负数，窗口中的值也就可能增加也可能减少，这种情况下不知道什么时机去收缩左侧窗口，也就无法求出「最大子数组和」。

### 56. 合并区间 (Merge Intervals)

#### 题目描述：

以数组 `intervals` 表示若干个区间的集合，其中单个区间为 `intervals[i] = [starti, endi]`。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。

#### 示例：

```
输入：intervals = [[1,3],[2,6],[8,10],[15,18]]
输出：[[1,6],[8,10],[15,18]]
解释：区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6]。
```

#### 解题思路：

排序 + 贪心：

1. 按区间的起始位置排序
2. 遍历区间，如果当前区间与结果中最后一个区间重叠，则合并
3. 否则，将当前区间加入结果

#### 复杂度分析：

- 时间复杂度：O(n log n)，排序的时间复杂度
- 空间复杂度：O(1)，不考虑结果存储的空间

#### Python 解答：

```python
class Solution:
    def merge(self, intervals: list[list[int]]) -> list[list[int]]:
        # 先按区间左端点排序
        intervals.sort(key=lambda x: x[0])

        # 结果数组，先放入第一个区间
        res = [intervals[0]]

        # 遍历后面的区间
        for start, end in intervals[1:]:
            last_end = res[-1][1]

            # 如果重叠，就合并，合并后的右端点要取更大的那个。
            if start <= last_end:
                res[-1][1] = max(last_end, end)
            else:
                # 不重叠，直接加入结果
                res.append([start, end])

        return res
```

#### 小菲の思考

怎么判断两个区间是否重叠

假设现在有两个区间：[a, b]  [c, d]，如果第二个区间的起点 c <= b，说明它和前一个区间有重叠。

intervals 是列表，里面的每个元素也都是一个小列表。sort() 把列表本身直接排序。

lambda x: x[0] 是一个匿名函数，把它理解成：
```python
def f(x):
    return x[0]
```

所以intervals.sort(key=lambda x: x[0])就是让区间按起点从小到大排列

result[-1][1]表示最后一个区间的右端点

### 189. 轮转数组

#### 题目描述：

给定一个整数数组 nums，将数组中的元素向右轮转 k 个位置，其中 k 是非负数。

#### 示例：

```
输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右轮转 1 步: [7,1,2,3,4,5,6]
向右轮转 2 步: [6,7,1,2,3,4,5]
向右轮转 3 步: [5,6,7,1,2,3,4]
```

#### 解题思路：

排序 + 贪心：

1. 按区间的起始位置排序
2. 遍历区间，如果当前区间与结果中最后一个区间重叠，则合并
3. 否则，将当前区间加入结果

#### 复杂度分析：

- 时间复杂度：O(n log n)，排序的时间复杂度
- 空间复杂度：O(1)，不考虑结果存储的空间

#### Python 解答：

```python
class Solution:
    def merge(self, intervals: list[list[int]]) -> list[list[int]]:
        # 先按区间左端点排序
        intervals.sort(key=lambda x: x[0])

        # 结果数组，先放入第一个区间
        res = [intervals[0]]

        # 遍历后面的区间
        for start, end in intervals[1:]:
            last_end = res[-1][1]

            # 如果重叠，就合并，合并后的右端点要取更大的那个。
            if start <= last_end:
                res[-1][1] = max(last_end, end)
            else:
                # 不重叠，直接加入结果
                res.append([start, end])

        return res
```

#### 小菲の思考

deque() 是 Python 里的 双端队列。两端都可以高效地插入和删除的队列。普通队列一般是：一端进一端出。但 deque 可以：左边插入、左边删除、右边插入、右边删除，所以叫“双端队列”。

常用操作：右边加入：`q.append(3)`，左边加入：`q.appendleft(3)`，右边删除：`q.pop()`，右边删除：`q.popleft()`

set添加元素不是append，而是add，删除不是pop而是remove

list 支持这些操作：在末尾添加元素：append()，删除末尾元素：pop()


## 报错类型总结

#### TypeError: Type List cannot be instantiated; use list() instead
把 `List` 当成“真正的列表”去创建了，但 `List` 只是类型标注，不能拿来直接生成对象

`List`是拿来写类型提示的，比如：
```python
def f(nums: List[int]) -> List[str]:
    ...
```
这里的 `List[int]` 只是告诉你`nums` 应该是“整数列表”，返回值应该是“字符串列表”，不是真正创建列表的工具

#### TypeError: 'builtin_function_or_method' object is not iterable  return list(groups.values)

`groups.values`是方法本身，还没有执行。加上括号后，才表示调用这个方法，真正把字典里的所有值取出来


## 参考的学习网址

https://yukinoshitasherry.github.io/Leetcode/

<img width="1568" height="1376" alt="image" src="https://github.com/user-attachments/assets/0c49d4ac-ca79-46ef-9316-07a7051134ff" />

