# 一、哈希表

## 1. 两数之和 (Two Sum)

### 题目描述

给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出 **和为目标值 `target`** 的那 **两个** 整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。

你可以按任意顺序返回答案。

### 示例

```text
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9，返回 [0,1]。
```

### 解题思路

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

### 复杂度分析

- 时间复杂度：`O(n)`，其中 `n` 是数组的长度。每个元素最多被访问一次。
- 空间复杂度：`O(n)`，用于存储哈希表中的元素。

### Python 解答

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
### 菲菲の思考

为什么这里不用`defaultdict`，因为我们想看complement在不在字典中，所以需要判断key在不在这一步，而`defaultdict`如果你访问一个不存在的键，它会自动创建默认值为空，就达不到我们想要的效果。

## 49. 字母异位词分组

### 题目描述

给你一个字符串数组，请你将 字母异位词 组合在一起。可以按任意顺序返回结果列表。

字母异位词 是由重新排列源单词的字母得到的一个新单词，所有源单词中的字母通常恰好只用一次。

### 示例

```text
输入: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
输出: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

### 解题思路

#### 方法一：使用哈希表

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

sorted(s) 的结果不是字符串，而是一个“字符列表”。要先用 join，把它重新拼成字符串，才能方便当作字典的键来分组。

#### 方法二：使用26个字母计数

时间复杂度主要出在排序上`O(k log k)`，如果改成统计每个字符串中出现的26个字母次数，就是O(k)

### 复杂度分析

- 时间复杂度：`O(nk log k)`，其中 `n` 是字符串数组的长度，k 是字符串的最大长度。外层循环跑了 n 次，每次循环里 sorted(s) 排序的时间复杂度是：O(k log k)，join是`O(k)`，append是`O(1)`，小于O(k log k)

- 空间复杂度：`O(nk)`，用于存储哈希表和结果

### Python 解答

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

# 报错类型总结

## TypeError: Type List cannot be instantiated; use list() instead
把 `List` 当成“真正的列表”去创建了，但 `List` 只是类型标注，不能拿来直接生成对象

`List`是拿来写类型提示的，比如：
```python
def f(nums: List[int]) -> List[str]:
    ...
```
这里的 `List[int]` 只是告诉你`nums` 应该是“整数列表”，返回值应该是“字符串列表”，不是真正创建列表的工具

## TypeError: 'builtin_function_or_method' object is not iterable  return list(groups.values)

`groups.values`是方法本身，还没有执行。加上括号后，才表示调用这个方法，真正把字典里的所有值取出来


# 参考的学习网址

https://yukinoshitasherry.github.io/Leetcode/
