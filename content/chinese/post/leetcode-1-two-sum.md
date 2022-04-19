



# Two Sum
## 概述
给定一个整数数组和一个目标值，在数组中找到两个数字，满足这两个数字之和等于目标值，返回这两个数字的索引。

此处我们假定这个数组中只有一个组合满足上述条件，此外，你不能使用同一个数字两次。

你可以以任意顺序返回组合的索引。

**示例 1:**

```markdown
输入: nums = [2,7,11,15], target = 9
输出: [0,1]
解释: Because nums[0] + nums[1] == 9, we return [0, 1].
```

**示例 2:**

```markdown
输入: nums = [3,2,4], target = 6
输出: [1,2]
```

**示例 3:**

```markdown
输入: nums = [3,3], target = 6
输出: [0,1]
```


**限制条件:**

* `2 <= nums.length <= 104`
* `-109 <= nums[i] <= 109`
* `-109 <= target <= 109`
* **Only one valid answer exists.**

## 解决方案
### 1 - 暴力破解
基本实现思路很简单，遍历数组两次，找到任意两个数字之和满足条件的即可。

需要注意的是，可能存在一个数字乘以2刚好满足条件，所以不能使用同一个数字两次。

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for(int x = 0;x < nums.length;x ++){
            for(int y = x + 1;y < nums.length;y ++){    //y从x+1开始
                if(nums[x] + nums[y] == target){
                    return new int[]{x,y};
                }
            }
        }
        
        return null;
    }
}
```

空间复杂度O(1)，时间复杂度`(n - 1) + (n - 2) + ... + 1 = n * (n + 1) / 2`，O(n * n)。

### 2 - 寻找补数
我们也可以转换一下思路，要找到两个数之和等于最终结果`target`，给定一个数字`m`，我们如果能在剩余的数字中找到一个`target - m`，那么我们其实也就找到了对应的组合。

我们可以使用`Map`来存储之前遍历过的结果，我们只需要把遍历过的结果存储下来就好，因为如果有这样一个组合满足条件，我们将这个组合中第一个遍历到的值存储在Map中，在遍历到第二个值的时候，我们就找到了这个组合。

```java
import java.util.HashMap;

class Solution {
    public int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> cache = new HashMap<>();

        for (int index = 0; index < nums.length; index++) {
            if(cache.containsKey(target - nums[index])) {
                return new int[]{cache.get(target - nums[index]), index};
            }

            cache.put(nums[index], index);
        }

        return null;
    }
}
```

空间复杂度O(n)，时间复杂度O(n)。可以看出我们其实是使用空间换了时间。