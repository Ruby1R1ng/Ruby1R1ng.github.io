---
title: "Leetcode Hot100"
---

# Leetcode Hot100 

## 涓€銆佸搱甯岃〃

### 1. 涓ゆ暟涔嬪拰 (Two Sum)

#### 棰樼洰鎻忚堪

缁欏畾涓€涓暣鏁版暟缁?`nums` 鍜屼竴涓暣鏁扮洰鏍囧€?`target`锛岃浣犲湪璇ユ暟缁勪腑鎵惧嚭 **鍜屼负鐩爣鍊?`target`** 鐨勯偅 **涓や釜** 鏁存暟锛屽苟杩斿洖瀹冧滑鐨勬暟缁勪笅鏍囥€?

浣犲彲浠ュ亣璁炬瘡绉嶈緭鍏ュ彧浼氬搴斾竴涓瓟妗堛€備絾鏄紝鏁扮粍涓悓涓€涓厓绱犲湪绛旀閲屼笉鑳介噸澶嶅嚭鐜般€?

浣犲彲浠ユ寜浠绘剰椤哄簭杩斿洖绛旀銆?

#### 绀轰緥

```text
杈撳叆锛歯ums = [2,7,11,15], target = 9
杈撳嚭锛歔0,1]
瑙ｉ噴锛氬洜涓?nums[0] + nums[1] == 9锛岃繑鍥?[0,1]銆?
```

#### 瑙ｉ鎬濊矾

浣跨敤鍝堝笇琛紙瀛楀吀锛夊瓨鍌ㄥ凡閬嶅巻鐨勫厓绱犲強鍏剁储寮曘€傚浜庢瘡涓厓绱狅紝璁＄畻鐩爣鍊间笌褰撳墠鍏冪礌鐨勫樊鍊硷紝妫€鏌ヨ宸€兼槸鍚﹀凡瀛樺湪浜庡搱甯岃〃涓€傚鏋滃瓨鍦紝鍒欐壘鍒颁簡涓€瀵规暟锛涘惁鍒欙紝灏嗗綋鍓嶅厓绱犲強鍏剁储寮曞瓨鍏ュ搱甯岃〃銆?

瀛楀吀灏辨槸涓€绉嶁€滈敭鍊煎鈥濈殑鏁版嵁缁撴瀯锛屼緥濡傦細
```text
{
    2: 0,
    7: 1
}
```
杩欓噷鐨?num_map 鐢ㄦ潵璁板綍锛氣€滄煇涓暟瀛楀嚭鐜拌繃锛屽苟涓斿畠鍑虹幇鍦ㄥ摢涓綅缃€?
```python
for index, num in enumerate(nums):
```
鍏朵腑index鏄繖涓綅缃殑涓嬫爣锛宯um鏄繖涓綅缃殑鍊笺€?
```python
return [num_map[complement], index]
```
鎵惧埌绛旀灏辫繑鍥炰袱涓笅鏍囥€傚鏋滄病鎵惧埌锛屽氨鎶婂綋鍓嶆暟瀛楄涓嬫潵
```python
num_map[num] = index
```
渚嬪绗竴娆￠亶鍘嗗埌 2 鏃讹細`num_map[2] = 0`锛屽瓧鍏稿彉鎴愶細`{2: 0}`

#### 澶嶆潅搴﹀垎鏋?

- 鏃堕棿澶嶆潅搴︼細`O(n)`锛屽叾涓?`n` 鏄暟缁勭殑闀垮害銆傛瘡涓厓绱犳渶澶氳璁块棶涓€娆°€?
- 绌洪棿澶嶆潅搴︼細`O(n)`锛岀敤浜庡瓨鍌ㄥ搱甯岃〃涓殑鍏冪礌銆?

#### Python 瑙ｇ瓟

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
#### 鑿茶彶銇€濊€?

涓轰粈涔堣繖閲屼笉鐢╜defaultdict`锛屽洜涓烘垜浠兂鐪媍omplement鍦ㄤ笉鍦ㄥ瓧鍏镐腑锛屾墍浠ラ渶瑕佸垽鏂璳ey鍦ㄤ笉鍦ㄨ繖涓€姝ワ紝鑰宍defaultdict`濡傛灉浣犺闂竴涓笉瀛樺湪鐨勯敭锛屽畠浼氳嚜鍔ㄥ垱寤洪粯璁ゅ€间负绌猴紝灏辫揪涓嶅埌鎴戜滑鎯宠鐨勬晥鏋溿€?

### 49. 瀛楁瘝寮備綅璇嶅垎缁?Group Anagrams)

#### 棰樼洰鎻忚堪

缁欎綘涓€涓瓧绗︿覆鏁扮粍锛岃浣犲皢 瀛楁瘝寮備綅璇?缁勫悎鍦ㄤ竴璧枫€傚彲浠ユ寜浠绘剰椤哄簭杩斿洖缁撴灉鍒楄〃銆?

瀛楁瘝寮備綅璇?鏄敱閲嶆柊鎺掑垪婧愬崟璇嶇殑瀛楁瘝寰楀埌鐨勪竴涓柊鍗曡瘝锛屾墍鏈夋簮鍗曡瘝涓殑瀛楁瘝閫氬父鎭板ソ鍙敤涓€娆°€?

#### 绀轰緥

```text
杈撳叆: strs = ["eat", "tea", "tan", "ate", "nat", "bat"]
杈撳嚭: [["bat"],["nat","tan"],["ate","eat","tea"]]
```

#### 瑙ｉ鎬濊矾

##### 鏂规硶涓€锛氫娇鐢ㄥ搱甯岃〃

1.瀵逛簬姣忎釜瀛楃涓诧紝灏嗗叾鎺掑簭鍚庣殑缁撴灉浣滀负閿?

2.灏嗗叿鏈夌浉鍚岄敭鐨勫瓧绗︿覆鍒嗙粍

3.杩斿洖鎵€鏈夊垎缁?

`defaultdict`鍙互鐞嗚В鎴愪竴绉嶆洿鏂逛究鐨勫瓧鍏革紝鏅€氬瓧鍏稿鏋滄煇涓敭杩樹笉瀛樺湪锛岀洿鎺ョ敤浼氭姤閿欍€備絾 `defaultdict(list)` 浼氳嚜鍔ㄧ粰浣犲噯澶囦竴涓┖鍒楄〃銆?

濡傛灉鏌愪釜閿繕娌″嚭鐜拌繃锛?

```python
groups["aet"]
```
瀹冧笉浼氭姤閿欙紝鑰屾槸鑷姩鍙樻垚锛?
```python
groups["aet"] = []
```
杩欐牱浣犲氨鍙互鐩存帴杩藉姞锛?
```python
groups["aet"].append("eat")
```

`sorted(s)` 鐨勭粨鏋滀笉鏄瓧绗︿覆锛岃€屾槸涓€涓€滃瓧绗﹀垪琛ㄢ€濄€傝鍏堢敤 join锛屾妸瀹冮噸鏂版嫾鎴愬瓧绗︿覆锛屾墠鑳芥柟渚垮綋浣滃瓧鍏哥殑閿潵鍒嗙粍銆?

`groups.values()`涓嶆槸鏅€氬垪琛紝鑰屾槸涓€涓?dict_values 瑙嗗浘瀵硅薄锛屾墍浠list(groups.values())`鎶婂畠杞崲鎴愮湡姝ｇ殑鍒楄〃


##### 鏂规硶浜岋細浣跨敤26涓瓧姣嶈鏁?

鏃堕棿澶嶆潅搴︿富瑕佸嚭鍦ㄦ帓搴忎笂`O(k log k)`锛屽鏋滄敼鎴愮粺璁℃瘡涓瓧绗︿覆涓嚭鐜扮殑26涓瓧姣嶆鏁帮紝灏辨槸O(k)

#### 澶嶆潅搴﹀垎鏋?

- 鏃堕棿澶嶆潅搴︼細`O(nk log k)`锛屽叾涓?`n` 鏄瓧绗︿覆鏁扮粍鐨勯暱搴︼紝k 鏄瓧绗︿覆鐨勬渶澶ч暱搴︺€傚灞傚惊鐜窇浜?n 娆★紝姣忔寰幆閲?sorted(s) 鎺掑簭鐨勬椂闂村鏉傚害鏄細O(k log k)锛宩oin鏄痐O(k)`锛宎ppend鏄痐O(1)`锛屽皬浜嶰(k log k)

- 绌洪棿澶嶆潅搴︼細`O(nk)`锛岀敤浜庡瓨鍌ㄥ搱甯岃〃鍜岀粨鏋?

#### Python 瑙ｇ瓟

鏂规硶涓€锛?

```python
def groupAnagrams(strs):
    from collections import defaultdict
    
    groups = defaultdict(list)
    
    for s in strs:
        key = ''.join(sorted(s))
        groups[key].append(s)
    
    return list(groups.values())
```

鏂规硶浜岋細

```python
from collections import defaultdict

def groupAnagrams(strs):
    groups = defaultdict(list)
    
    for s in strs:
        count = [0] * 26   # 26涓瓧姣嶇殑璁℃暟鍣?
        
        for ch in s:
            index = ord(ch) - ord('a')
            count[index] += 1
        
        key = tuple(count)   # 鍒楄〃涓嶈兘褰撳瓧鍏搁敭锛屾墍浠ヨ浆鎴愬厓缁?
        groups[key].append(s)
    
    return list(groups.values())
```

`ord(ch)`鎶婁竴涓瓧绗﹁浆鎹㈡垚瀹冨搴旂殑鏁板瓧缂栫爜銆傛瘮濡傦細`ord('a')`缁撴灉鏄?7

### 128. 鏈€闀胯繛缁簭鍒?(Longest Consecutive Sequence)

#### 棰樼洰鎻忚堪

缁欏畾涓€涓湭鎺掑簭鐨勬暣鏁版暟缁?nums锛屾壘鍑烘暟瀛楄繛缁殑鏈€闀垮簭鍒楋紙涓嶈姹傚簭鍒楀厓绱犲湪鍘熸暟缁勪腑杩炵画锛夌殑闀垮害銆?

璇蜂綘璁捐骞跺疄鐜版椂闂村鏉傚害涓?O(n) 鐨勭畻娉曡В鍐虫闂銆?

#### 绀轰緥

```text
杈撳叆锛歯ums = [100,4,200,1,3,2]
杈撳嚭锛?
瑙ｉ噴锛氭渶闀挎暟瀛楄繛缁簭鍒楁槸 [1, 2, 3, 4]銆傚畠鐨勯暱搴︿负 4銆?
```

#### 瑙ｉ鎬濊矾

- 灏嗘墍鏈夋暟瀛楀瓨鍏ュ搱甯岄泦鍚?
  
- 瀵逛簬姣忎釜鏁板瓧锛屽鏋滃畠鏄繛缁簭鍒楃殑璧风偣锛堝嵆 num-1 涓嶅湪闆嗗悎涓級锛屽垯浠庤鏁板瓧寮€濮嬭绠楄繛缁簭鍒楃殑闀垮害
  
- 鏇存柊鏈€闀胯繛缁簭鍒楃殑闀垮害

鎶娾€滃垪琛ㄦ敼鎴愬搱甯岃〃鏉ユ煡鈥濋€氬父鐩存帴鐢?set() 灏卞浜嗭紝set 鏈川涓婂氨鏄熀浜庡搱甯屽疄鐜扮殑闆嗗悎锛屾煡鎵惧钩鍧囨椂闂村鏉傚害鏄?O(1)

杩欓噷鐪熸闇€瑕佺殑鏄細蹇€熷垽鏂竴涓暟`鍦ㄤ笉鍦╜闆嗗悎閲岋紝涓嶉渶瑕佽褰曗€滈敭瀵瑰簲鐨勫€尖€?

濡傛灉浣犵洿鎺ュ湪鍒楄〃閲屽垽鏂璥if x in nums`鏄嚎鎬ф煡鎵撅紝鏃堕棿澶嶆潅搴︽槸 O(n)锛屾崲鎴?set 鍚庯紝杩欎簺鍒ゆ柇閮藉彉鎴愬钩鍧?O(1)

#### 澶嶆潅搴﹀垎鏋?

- 鏃堕棿澶嶆潅搴︼細`O(n)`锛屽叾涓?`n` 鏄暟缁勭殑闀垮害銆傛瘡涓厓绱犳渶澶氳璁块棶涓€娆°€?
- 绌洪棿澶嶆潅搴︼細`O(n)`锛岀敤浜庡瓨鍌ㄥ搱甯岃〃涓殑鍏冪礌銆?

#### Python 瑙ｇ瓟

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

#### 鑿茶彶銇€濊€?
鎴戝啓鐨勶細
```python
    for num in groups:
        if num - 1 in groups:
            continue

        temp = 1
        while num + 1 in groups:
            temp += 1
            num += 1
```

## 浜屻€佸弻鎸囬拡

### 283. 绉诲姩闆?(Move Zeroes)

#### 棰樼洰鎻忚堪

缁欏畾涓€涓暟缁?nums锛岀紪鍐欎竴涓嚱鏁板皢鎵€鏈?0 绉诲姩鍒版暟缁勭殑鏈熬锛屽悓鏃朵繚鎸侀潪闆跺厓绱犵殑鐩稿椤哄簭銆?

娉ㄦ剰 蹇呴』鍦ㄤ笉澶嶅埗鏁扮粍鐨勬儏鍐典笅鍘熷湴瀵规暟缁勮繘琛屾搷浣溿€?

#### 绀轰緥

```text
杈撳叆: nums = [0,1,0,3,12]
杈撳嚭: [1,3,12,0,0]
```

#### 瑙ｉ鎬濊矾

浣跨敤鍙屾寚閽堬細

- 浣跨敤涓€涓寚閽?left 鎸囧悜褰撳墠搴旇鏀剧疆闈為浂鍏冪礌鐨勪綅缃?
- 閬嶅巻鏁扮粍锛岄亣鍒伴潪闆跺厓绱犲氨灏嗗叾鏀惧埌 left 浣嶇疆锛岀劧鍚?left++

#### 澶嶆潅搴﹀垎鏋?

- 鏃堕棿澶嶆潅搴︼細O(n)锛屽叾涓?n 鏄暟缁勭殑闀垮害
- 绌洪棿澶嶆潅搴︼細O(1)锛屽彧闇€瑕佸父鏁扮殑棰濆绌洪棿

#### Python 瑙ｇ瓟

```python
def moveZeroes(nums):
    left = 0
    
    # 灏嗘墍鏈夐潪闆跺厓绱犵Щ鍒板墠闈?
    for right in range(len(nums)):
        if nums[right] != 0:
            nums[left], nums[right] = nums[right], nums[left]
            left += 1
```
#### 鑿茶彶銇€濊€?

鍙虫寚閽堝厛璧帮紝鍘绘壘闈?鍊?

### 11. 鐩涙渶澶氭按鐨勫鍣?(Container With Most Water)

#### 棰樼洰鎻忚堪

缁欏畾涓€涓暱搴︿负 n 鐨勬暣鏁版暟缁?height銆傛湁 n 鏉″瀭绾匡紝绗?i 鏉＄嚎鐨勪袱涓鐐规槸 (i, 0) 鍜?(i, height[i])銆?

鎵惧嚭鍏朵腑鐨勪袱鏉＄嚎锛屼娇寰楀畠浠笌 x 杞村叡鍚屾瀯鎴愮殑瀹瑰櫒鍙互瀹圭撼鏈€澶氱殑姘淬€?

杩斿洖瀹瑰櫒鍙互鍌ㄥ瓨鐨勬渶澶ф按閲忋€?

#### 绀轰緥

<img width="821" height="528" alt="image" src="https://github.com/user-attachments/assets/7596df30-a370-4be0-b8b4-e0f48a4bce08" />

#### 瑙ｉ鎬濊矾

1. 鏆村姏閬嶅巻
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
2. 浣跨敤鍙屾寚閽堟硶锛?

- 鍒濆鍖栧乏鍙虫寚閽堝垎鍒寚鍚戞暟缁勭殑涓ょ
  
- 璁＄畻褰撳墠瀹瑰櫒鐨勫閲忥細min(height[left], height[right]) * (right - left)
  
- 鏇存柊鏈€澶у閲?
  
- 绉诲姩杈冪煭鐨勯偅涓€渚ф寚閽堬紙鍥犱负绉诲姩杈冮暱鐨勪竴渚т笉浼氬鍔犲閲忥級
  
- 閲嶅鐩村埌宸﹀彸鎸囬拡鐩搁亣

#### 澶嶆潅搴﹀垎鏋?

- 鏃堕棿澶嶆潅搴︼細O(n)锛屽叾涓?n 鏄暟缁勭殑闀垮害銆傚弻鎸囬拡鏈€澶氶亶鍘嗘暣涓暟缁勪竴娆?
- 绌洪棿澶嶆潅搴︼細O(1)锛屽彧闇€瑕佸父鏁扮殑棰濆绌洪棿

#### Python 瑙ｇ瓟

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

### 15.涓夋暟涔嬪拰 (3Sum)

#### 棰樼洰鎻忚堪

缁欎綘涓€涓暣鏁版暟缁?nums锛屽垽鏂槸鍚﹀瓨鍦ㄤ笁鍏冪粍 [nums[i], nums[j], nums[k]] 婊¤冻 i != j銆乮 != k 涓?j != k锛屽悓鏃惰繕婊¤冻 nums[i] + nums[j] + nums[k] == 0銆傝浣犺繑鍥炴墍鏈夊拰涓?0 涓斾笉閲嶅鐨勪笁鍏冪粍銆?

娉ㄦ剰锛?绛旀涓笉鍙互鍖呭惈閲嶅鐨勪笁鍏冪粍銆?

#### 绀轰緥

```text
杈撳叆锛歯ums = [-1,0,1,2,-1,-4]
杈撳嚭锛歔[-1,-1,2],[-1,0,1]]
瑙ｉ噴锛?
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0 銆?
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0 銆?
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0 銆?
涓嶅悓鐨勪笁鍏冪粍鏄?[-1,0,1] 鍜?[-1,-1,2] 銆?
娉ㄦ剰锛岃緭鍑虹殑椤哄簭鍜屼笁鍏冪粍鐨勯『搴忓苟涓嶉噸瑕併€?
```

#### 瑙ｉ鎬濊矾

- 棣栧厛瀵规暟缁勮繘琛屾帓搴?

- 鍥哄畾绗竴涓暟锛屼娇鐢ㄥ弻鎸囬拡鍦ㄥ墿浣欓儴鍒嗗鎵惧彟澶栦袱涓暟

- 浣跨敤鍙屾寚閽堟硶锛氬乏鎸囬拡鎸囧悜鍥哄畾鏁扮殑涓嬩竴涓綅缃紝鍙虫寚閽堟寚鍚戞暟缁勬湯灏?

- 鏍规嵁涓夋暟涔嬪拰涓?0 鐨勫叧绯荤Щ鍔ㄦ寚閽?

- 娉ㄦ剰璺宠繃閲嶅鍏冪礌浠ラ伩鍏嶉噸澶嶈В

#### 澶嶆潅搴﹀垎鏋?

- 鏃堕棿澶嶆潅搴︼細O(n虏)锛屽叾涓?n 鏄暟缁勭殑闀垮害銆傛帓搴?O(n log n) + 鍙屾寚閽堥亶鍘?O(n虏)
- 绌洪棿澶嶆潅搴︼細O(1)锛岄櫎浜嗗瓨鍌ㄧ瓟妗堢殑绌洪棿澶栵紝鍙渶瑕佸父鏁扮殑棰濆绌洪棿

#### Python 瑙ｇ瓟

```python
def threeSum(nums):
    nums.sort()
    res = []
    n = len(nums)
    
    for i in range(n - 2):
        # 璺宠繃閲嶅鍏冪礌
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
                # 璺宠繃閲嶅鍏冪礌
                while left < right and nums[left] == nums[left + 1]:
                    left += 1
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1
                left += 1
                right -= 1
    
    return res
```
#### 灏忚彶銇€濊€?

i 鏄涓€涓暟锛宭eft 鏄浜屼釜鏁帮紝right 鏄涓変釜鏁帮紝鎵€浠ュ繀椤绘湁i <= n - 3

`i != j銆乮 != k 涓?j != k` 琛ㄧず锛氬悓涓€涓綅缃笉鑳借鐢ㄤ袱娆?鑰屼笉鏄笉鑳芥湁鐩稿悓鐨勫€?

`nums[i] == nums[i - 1]` 鍒ゆ柇褰撳墠鍏冪礌鏄笉鏄拰鍓嶄竴涓噸澶?涓€瀹氳涓庡墠涓€涓瘮杈冿紒 `[-1, 0, 1, 2, -1, -4]`鎺掑簭鍚庡彉鎴恅[-4, -1, -1, 0, 1, 2]`

`while left < right and nums[left] == nums[left + 1]`琛ㄧず褰撳墠宸茬粡鎵惧埌涓€涓В浜嗭紝濡傛灉宸︽寚閽堝悗闈㈣繕鏄悓鏍风殑鍊硷紝灏变竴鐩磋烦杩囥€?

### 42. 鎺ラ洦姘?(Trapping Rain Water)

#### 棰樼洰鎻忚堪

缁欏畾 n 涓潪璐熸暣鏁拌〃绀烘瘡涓搴︿负 1 鐨勬煴瀛愮殑楂樺害鍥撅紝璁＄畻鎸夋鎺掑垪鐨勬煴瀛愶紝涓嬮洦涔嬪悗鑳芥帴澶氬皯闆ㄦ按銆?

#### 绀轰緥

<img width="729" height="272" alt="image" src="https://github.com/user-attachments/assets/5a99a511-dd6d-4c52-8c35-a17cb1b7474e" />

#### 瑙ｉ鎬濊矾

鏂规硶1锛氬崟璋冩爤

浣跨敤鍗曡皟閫掑噺鏍堬紝褰撻亣鍒版洿楂樼殑鏌卞瓙鏃讹紝璁＄畻鑳芥帴鐨勯洦姘?

鏍堬紙stack锛夊氨鏄竴绉嶅悗杩涘厛鍑虹殑鏁版嵁缁撴瀯銆備綘鍙互鎶婂畠鎯虫垚涓€鎽炵洏瀛愶細鏂扮洏瀛愬彧鑳芥斁鍦ㄦ渶涓婇潰push锛屾嬁鐩樺瓙涔熷彧鑳戒粠鏈€涓婇潰鎷縫op

鍦ㄢ€滄帴闆ㄦ按鈥濊繖棰橀噷锛屾爤閲屾斁鐨勪笉鏄珮搴︽湰韬紝鑰屾槸锛氭煴瀛愮殑涓嬫爣锛坕ndex锛夈€傚崟璋冮€掑噺鏍堢殑鎰忔€濇槸锛氫粠鏍堝簳鍒版爤椤讹紝瀵瑰簲鐨勯珮搴︽槸閫掑噺鐨勶紙瓒婃潵瓒婂皬锛?

- 缁存姢涓€涓崟璋冮€掑噺鏍堬紝鏍堥噷瀛樼殑鏄笅鏍?

- 褰撻亣鍒颁竴涓洿楂樼殑鏌卞瓙鏃讹紝璇存槑鍙兘褰㈡垚鈥滃嚬妲解€?

- 寮瑰嚭鏍堥《浣滀负鈥滃簳閮ㄢ€濓紝鍐嶇敤褰撳墠鏌卞瓙鍜屾柊鐨勬爤椤朵綔涓哄乏鍙宠竟鐣岋紝璁＄畻杩欓儴鍒嗚兘鎺ョ殑闆ㄦ按

涓句緥锛?
```text
楂樺害:  4   2   0   3
浣嶇疆:  0   1   2   3
```
鎵弿鍒颁綅缃?3锛堥珮搴?3锛夋椂锛屾爤閲屽彲鑳芥槸锛?
```text
[0, 1, 2]
瀵瑰簲楂樺害 [4, 2, 0]
```
鐜板湪 3 > 0锛屽脊鍑?2锛?锛堥珮搴?0锛夋槸鍧戝簳锛屾柊鏍堥《 1锛堥珮搴?2锛夋槸宸﹁竟鐣岋紝褰撳墠 3锛堥珮搴?3锛夋槸鍙宠竟鐣岋紝褰㈡垚涓€涓潙锛?

```text
#
#     #
# #~~~#
# #~~~#
-----------
0 1 2 3
```
缁х画鍒ゆ柇锛氬洜涓哄綋鍓嶉珮搴?3 杩樺ぇ浜庢柊鐨勬爤椤堕珮搴?2

鐜板湪锛?

搴曢儴 = 1

宸﹁竟鐣?= 0锛堥珮搴?4锛?

鍙宠竟鐣?= 3锛堥珮搴?3锛?

```text
#     |
#~~~~~#
# #~~~#
# #~~~#
---------
0 1 2 3
```

鏂规硶2锛氬弻鎸囬拡

- 浣跨敤宸﹀彸鎸囬拡锛岀淮鎶ゅ乏鍙充袱杈圭殑鏈€澶ч珮搴?
  
- 瀵逛簬姣忎釜浣嶇疆锛岃兘鎺ョ殑闆ㄦ按 = min(left_max, right_max) - height[i]
  
- 绉诲姩杈冨皬鐨勪竴杈?

#### 澶嶆潅搴﹀垎鏋?

- 鏃堕棿澶嶆潅搴︼細O(n)
- 绌洪棿澶嶆潅搴︼細O(1)锛堝弻鎸囬拡锛夋垨 O(n)锛堝崟璋冩爤锛?

#### Python 瑙ｇ瓟

```python
# 鏂规硶涓€锛氬崟璋冮€掑噺鏍?
def trap(self, height: List[int]) -> int:
    stack = []   # 瀛樹笅鏍囷紝淇濇寔瀵瑰簲楂樺害鍗曡皟閫掑噺
    water = 0

    for i in range(len(height)):
        while stack and height[i] > height[stack[-1]]:
            bottom = stack.pop()   # 鍑规Ы搴曢儴

            if not stack:
                break   # 宸﹁竟娌℃湁杈圭晫浜嗭紝鏃犳硶鎺ユ按

            left = stack[-1]       # 宸﹁竟鐣?
            width = i - left - 1   # 瀹藉害
            h = min(height[left], height[i]) - height[bottom]  # 鏈夋晥楂樺害

            water += width * h

        stack.append(i)

    return water

# 鏂规硶浜岋細鍙屾寚閽?
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
#### 灏忚彶銇€濊€?
鍦?Python 閲岋紝鈥滄爤鈥濋€氬父灏辨槸鐢ㄥ垪琛ㄦ潵瀹炵幇鐨勩€傚洜涓?Python 鐨?list 鏀寔杩欎簺鎿嶄綔锛?

- 鍦ㄦ湯灏炬坊鍔犲厓绱狅細append()

- 鍒犻櫎鏈熬鍏冪礌锛歱op()

鑰屾爤姝ｅソ灏辨槸锛?

- 浠庨《閮ㄦ斁鍏ュ厓绱?

- 浠庨《閮ㄥ彇鍑哄厓绱?

濡傛灉鎴戜滑绾﹀畾鍒楄〃鐨勬湯灏惧氨鏄爤椤?閭ｅ畠灏卞畬鍏ㄥ彲浠ユā鎷熸爤銆傛敞鎰忥紝Python 鐨?list 娌℃湁鍙?push() 鐨勬柟娉曪紝鈥滃線鏈熬鍔犲厓绱犫€濈粺涓€鍙細append()

stack[-1] 纭疄鏄爤閲屾渶鍚庡帇杩涘幓銆佸綋鍓嶄綅浜庢爤椤剁殑閭ｄ釜鍏冪礌銆備絾鍦ㄨ繖閬撻鐨勮繖涓€鍒伙紝瀹冩伆濂借〃绀虹殑鏄細琚脊鍑虹殑鍧戝簳宸﹁竟锛岀瀹冩渶杩戙€佽繕娌¤寮硅蛋鐨勯偅鏍规煴瀛愶紝鎵€浠ュ畠鏄乏杈圭晫銆俬eight[stack[-1]]鍙栦綅缃搴旂殑楂樺害

pop浠ュ悗闇€瑕佹鏌ヨ繕鏈夋病鏈塻tack


## 鎶ラ敊绫诲瀷鎬荤粨

### TypeError: Type List cannot be instantiated; use list() instead
鎶?`List` 褰撴垚鈥滅湡姝ｇ殑鍒楄〃鈥濆幓鍒涘缓浜嗭紝浣?`List` 鍙槸绫诲瀷鏍囨敞锛屼笉鑳芥嬁鏉ョ洿鎺ョ敓鎴愬璞?

`List`鏄嬁鏉ュ啓绫诲瀷鎻愮ず鐨勶紝姣斿锛?
```python
def f(nums: List[int]) -> List[str]:
    ...
```
杩欓噷鐨?`List[int]` 鍙槸鍛婅瘔浣燻nums` 搴旇鏄€滄暣鏁板垪琛ㄢ€濓紝杩斿洖鍊煎簲璇ユ槸鈥滃瓧绗︿覆鍒楄〃鈥濓紝涓嶆槸鐪熸鍒涘缓鍒楄〃鐨勫伐鍏?

### TypeError: 'builtin_function_or_method' object is not iterable  return list(groups.values)

`groups.values`鏄柟娉曟湰韬紝杩樻病鏈夋墽琛屻€傚姞涓婃嫭鍙峰悗锛屾墠琛ㄧず璋冪敤杩欎釜鏂规硶锛岀湡姝ｆ妸瀛楀吀閲岀殑鎵€鏈夊€煎彇鍑烘潵


## 鍙傝€冪殑瀛︿範缃戝潃

https://yukinoshitasherry.github.io/Leetcode/


