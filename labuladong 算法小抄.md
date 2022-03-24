# 双指针 2021-08-18 17:35✅


## 快慢指针 


### 141.环形链表<sup>2.5k</sup>

[我是链接](https://leetcode-cn.com/problems/linked-list-cycle)


```javascript
var hasCycle = function(head) {
  // 设置快(2)慢(1)指针，若快慢指针相遇，则为环形
  let slow = head
  let fast = head
  while (fast && fast.next) {
    fast = fast.next.next
    slow = slow.next
    if (slow === fast) {
      return true
    }
  }
  return false
};
```


### 142.环形链表II <sup>1.6k</sup>

[我是链接](https://leetcode-cn.com/problems/linked-list-cycle-ii/)


```javascript
var detectCycle = function(head) {
  let slow = head
  let fast = head
  let isLoop = false
  while (fast && fast.next) {
    fast = fast.next.next
    slow = slow.next
    if (slow === fast) {
      isLoop = true
      // 找到时直接跳出循环
      break
    }
  }
  // 确认是环形，找出pos
  if (isLoop) {
    slow = head
    while (slow !== fast) {
      slow = slow.next
      fast = fast.next
    }
  }
  return isLoop ? slow : null
};
```


### 876. 链表的中间结点 <sup>1.5k</sup>

[我是链接](https://leetcode-cn.com/problems/middle-of-the-linked-list/)


```javascript
// 快指针(2) 慢指针(1)，快指针到达尾部时，慢指针刚好在中点
var middleNode = function(head) {
  let slow = fast = head
  while (fast && fast.next) {
    fast = fast.next.next
    slow = slow.next
  }
  return slow
};
```


### 19.删除链表的倒数第N个节点 <sup>3.5k</sup>

[我是链接](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)


```javascript
var removeNthFromEnd = function(head, n) {
  let dummyNode = new ListNode(-1)
  dummyNode.next = head
  let slow = fast = dummyNode
  while (n--) {
    fast = fast.next
  }
  while (fast && fast.next) {
    fast = fast.next
    slow = slow.next
  }
  slow.next = slow.next.next
  return dummyNode.next
};
```


## 左右指针


### 704.二分查找 

[我是链接](https://leetcode-cn.com/problems/binary-search/)

**探寻边界**时找到target的下标后**不要直接返回mid，而是通过缩减区间**，去探求左｜右边界


```javascript
/** 基础二分法 **/
/** 
 *  初始化 right 为 nums.length - 1 
 *  搜索区间 [left, right] => while (left <= right)
 *  决定 left = mid + 1 , right = mid - 1
 * **/
 const binnay_search = (nums, target) => {
  let left = 0;
  let right = nums.length - 1
  while (left <= right) {
    let mid = Math.floor((left + right) / 2)
    if (nums[mid] === target) {
      return target
    } else if (nums[mid] < target) {
      left = mid + 1
    } else if (nums[mid] > target) {
      right = mid - 1
    }
  }
  return -1
}

const leftSearch = (nums, target) => {
  const n = nums.length
  let left = 0
  let right = n - 1
  while (left <= right) {
    const mid = Math.floor((left + right) / 2)
    if (nums[mid] === target) {
      right = mid - 1
    }
    if (nums[mid] < target) {
      left = mid + 1
    }
    if (nums[mid] > target) {
      right = mid - 1
    }
  }
  if (left > n - 1 || nums[left] !== target) {
    return - 1
  }
  return left
}

const rightSearch = (nums, target) => {
  const n = nums.length
  let left = 0
  let right = n - 1
  while (left <= right) {
    const mid = Math.floor((left + right) / 2)
    if (nums[mid] === target) {
      left = mid + 1
    }
    if (nums[mid] < target) {
      left = mid + 1
    }
    if (nums[mid] > target) {
      right = mid - 1
    }
  }
  if (right < 0 || nums[right] !== target) {
    return - 1
  }
  return right
}
// 二分查找有个缺点，就是不能找到两侧区间例如参数为`[1, 2, 2, 2, 5], 2`时，只能找到下标为2的2
console.log(leftSearch([1,2,2,2,3], 2))
console.log(rightSearch([1,2,2,2,3], 2))
```

### 875.爱吃香蕉的珂珂<sup>0.3k</sup>

[我是链接](https://leetcode-cn.com/problems/koko-eating-bananas/)

```js
var minEatingSpeed = function(piles, h) {
  let left = 1
  let right = Math.max(...piles)
  const canFinished = (piles, H, speed) => {
    let time = 0
    for (let item of piles) {
      time += Math.ceil(item / speed)
    }
    return time <= H
  }
  while (left <= right) {
    let mid = Math.floor((left + right) / 2)
    if (canFinished(piles, h, mid)) {
      right = mid - 1
    } else {
      left = mid + 1
    }
  }
  return left
};
```

### 1011.在 D 天内送达包裹的能力<sup>0.5k</sup>

[我是链接](https://leetcode-cn.com/problems/capacity-to-ship-packages-within-d-days)

```js
var shipWithinDays = function(weights, days) {
  let left = Math.max(...weights) // 最小载重必须能载一个包裹，故取最大重量包裹
  let right = weights.reduce((a, b) => a + b) // 最大载重就是一天内运完所有包裹
  const canFinish = (weights, DAY, cap) => {
    let current = 0
    let day = 1
    for (let w of weights) {
      current += w
      // 超过每天的限制, 这个货就放在下一天运
      if (current > cap) {
        current = w
        day ++
      }
    }
    return day <= DAY
  }
  while (left <= right) {
    let mid = Math.floor((left + right) / 2)
    if (canFinish(weights, days, mid)) {
      right = mid - 1
    } else {
      left = mid + 1
    }
  }
  return left
};
```



## 滑动窗口


### 76. 最小覆盖子串 <sup>1.2k</sup>

[我是链接](https://leetcode-cn.com/problems/minimum-window-substring)


```javascript
var minWindow = function(s, t) {
  let need = {}
  let slideWindow = {}
  let valid = 0
  for (let i = 0; i < t.length; i ++) {
    need[t[i]] = need[t[i]] ? need[t[i]] + 1 : 1
  }
  let left = 0
  let right = 0
  let start = 0
  let minLen = Infinity
  while (right < s.length) {
    const c = s[right ++]
    slideWindow[c] = slideWindow[c] ? slideWindow[c] + 1 : 1
    if (slideWindow[c] === need[c]) {
      valid ++
    }
    // 窗口缩减条件
    while (valid === Object.keys(need).length) {
      // 处理结果
      if (right - left < minLen) {
        start = left
        minLen = right - left
      }
      // 窗口移动
      const d = s[left ++]
      if (need[d]) {
        if (slideWindow[d] === need[d]) {
          valid --
        }
        slideWindow[d] --
      }
    }
  }
  return minLen === Infinity ? '' : s.substr(start, minLen)
};
```


### 567.字符串的排列 <sup>0.9k</sup>

[我是链接](https://leetcode-cn.com/problems/permutation-in-string)


```javascript
var checkInclusion = function(s1, s2) {
  let need = {}
  let slideWindow = {}
  for (let i = 0; i < s1.length; i ++) {
    need[s1[i]] = need[s1[i]] ? need[s1[i]] + 1: 1
  }
  let left = 0
  let right = 0
  let valid = 0
  let res = false
  // 右滑动
  while (right < s2.length) {
    const c = s2[right ++]
    slideWindow[c] = slideWindow[c] ? slideWindow[c] + 1 : 1
    if (slideWindow[c] === need[c]) {
      valid ++
    }
    // 窗口缩减条件
    while (right - left >= s1.length) {
      // 结果返回
      if (valid === Object.keys(need).length) {
        res = true
        break
      } else {
        // 左滑动
        const d = s2[left ++]
        if (need[d]) {
          if (need[d] === slideWindow[d]) {
            valid --
          }
          slideWindow[d] --
        }
      }
    }
  }
  return res
};
```


### 3.无重复字符的最长子串 <sup>7k</sup>

[我是链接](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters)


```javascript
var lengthOfLongestSubstring = function(s) {
  let slideWindow = {}
  let left = 0
  let right = 0
  let res = 0
  while (right < s.length) {
    const c = s[right ++]
    slideWindow[c] = slideWindow[c] ? slideWindow[c] + 1 : 1
    // 缩减条件
    while (slideWindow[c] > 1) {
      const d = s[left ++]
      slideWindow[d] --
    }
    res = Math.max(res, right - left)
  }
  return res
};
```


# 动态规划


## 子序列问题


### 300. 最长递增子序列 

[我是链接](https://leetcode-cn.com/problems/longest-increasing-subsequence)


```javascript
/**
 * dp(i)表示：以 nums[i] 结尾的最长递增子序列长度
 * nums[i] > nums[j]：dp(i) = max(1 + dp(j), dp(i))
 */
var lengthOfLIS = function(nums) {
  const n = nums.length
  let dp = new Array(n).fill(1)
  let res = 1
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < i; j++) {
      if (nums[i] > nums[j]) {
        dp[i] = Math.max(1 + dp[j], dp[i])
        res = Math.max(res, dp[i])
      }
    }
  }
  return res
};
```


### 354.俄罗斯套娃信封问题 

[我是链接](https://leetcode-cn.com/problems/russian-doll-envelopes)


```javascript
var maxEnvelopes = function(envelopes) {
  // 先按宽度| 高度去升序排序另一维度，相同宽度的高度降序排序
  // [[5,4],[6,4],[6,7],[2,3]] => [[2,3], [5,4], [6, 7], [6, 4]]
  // 另一维度就是 [3, 4, 7, 4] 即转变成最长增长子序列问题
  const nums = envelopes.sort((a, b) => {
    if (a[0] === b [0]) {
      return b[1] - a[1]
    } else {
      return a[0] - b[0]
    }
  }).map(i => i[1])
  // 最长递增子序列
  var lengthOfLIS = function(nums) {
    const n = nums.length
    let dp = new Array(n).fill(1)
    let res = 1
    for (let i = 1; i < n; i ++) {
      for (let j = 0; j < i; j ++) {
        if (nums[j] < nums[i]) {
          dp[i] = Math.max(dp[i], dp[j] + 1)
          res = Math.max(res, dp[i])
        }
      }
    }
    return res
  };
  return lengthOfLIS(nums)
};
```


### 53.最大子序和 

[我是链接](https://leetcode-cn.com/problems/maximum-subarray)


```javascript
/**
 * dp(i)表示：以 nums[i] 结尾的最大子数组和
 * dp(i) = max(nums(i), dp(i - 1) + nums(i))
 */
var maxSubArray = function(nums) {
  let dp = [nums[0]]
  let res = nums[0]
  for (let i = 1; i < nums.length; i++) {
    // 要么自身，要么与前面的子数组合并
    dp[i] = Math.max(nums[i], dp[i-1] + nums[i])
    res = Math.max(res, dp[i])
  }
  return res
};
```


### 1143.最长公共子序列 

[我是链接](https://leetcode-cn.com/problems/longest-common-subsequence/)

![image.png](https://pic.leetcode-cn.com/1617411822-KhEKGw-image.png)


```javascript
/**
 * base_case: dp[...][0]= dp[0][...] = 0， 任意一个子串为空串，lcs 为 0
 * dp[i][j] 表示串s1[...i-1] 和 s2[...j-1]的最长公共子序列
 * i 从 1 开始，串的下表要取 i - 1
 * s1[i-1] === s2[j-1]: dp[i][j] = dp[i-1][j-1] + 1，当前字符为lcs中的一个
 * s1[i-1] !== s2[j-1]:dp[i][j] = max(dp[i-1][j], dp[i][j-1])
 */
var longestCommonSubsequence = function(text1, text2) {
  const m = text1.length
  const n = text2.length
  let dp = new Array(m + 1)
  for (let i = 0; i < m + 1; i++) {
    dp[i] = new Array(n + 1).fill(0)
  }
  for (let i = 1; i < m + 1; i ++) {
    for (let j = 1; j < n + 1; j ++) {
      if (text1[i - 1] === text2[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1] + 1
      } else {
        dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1])
      }
    }
  }
  return dp[m][n]
};
```

### 516.最长回文子序列 

[我是链接](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

```js
/**
 * base_case: i === j 时 dp[i][j] = 1
 * dp[i][j]表示s[i...j] 的最长回文子序列
 * s[i] === s[j]时 dp[i][j] = dp[i+1][j-1] + 2,头尾2个字符算上啊
 * dp[i][j] = max(dp[i+1][j], dp[i][j-1])
 */
var longestPalindromeSubseq = function(s) {
  const n = s.length
  let dp = new Array(n + 1)
  for (let i = 0; i < n + 1; i ++) {
    dp[i] = new Array(n + 1).fill(0)
    dp[i][i] = 1
  }
  for (let i = n - 2; i >= 0; i --) {
    for (let j = i + 1; j < n; j ++) {
      if (s[i] === s[j]) {
        dp[i][j] = dp[i + 1][j - 1] + 2
      } else {
        dp[i][j] = Math.max(dp[i + 1][j], dp[i][j - 1])
      }
    }
  }
  return dp[0][n - 1]
};
```

###  72.编辑距离 

[我是链接](https://leetcode-cn.com/problems/edit-distance/)

```js
/**
 * base_case: dp[0][...j] = ...j,dp[i][0]=...i,表示其中一个为空串时，变成另一个的最小编辑距离为非空串的长度
 * dp[i][j] 表示：s1[...i]变成s2[...j]的最小编辑距离
 * s1[i - 1] === s2[j -1]: dp[i][j] = dp[i-1][j-1]
 * 不相等时： dp[i][j] = min(删除, 插入, 替换)
 * 删除：dp[i][j - 1] + 1
 * 插入：dp[i -1][j] + 1
 * 替换：dp[i-1][j-1] + 1
 */
var minDistance = function(word1, word2) {
   const m = word1.length
   const n = word2.length
   let dp = new Array(m + 1)
   for (let i = 0; i < m + 1; i++) {
     dp[i] = new Array(n + 1).fill(0)
   }
   // base_case
  for (let i = 0; i < m + 1; i++) {
    dp[i][0] = i
  }
  for (let j = 0; j < n + 1; j++) {
    dp[0][j] = j
  }
  // 开始操作
  for (let i = 1; i < m + 1; i++) {
    for (let j = 1; j < n + 1; j++) {
      if (word1[i-1] === word2[j-1]) {
        dp[i][j] = dp[i-1][j-1]
      } else {
        dp[i][j] = Math.min(dp[i-1][j], dp[i][j - 1], dp[i - 1][j - 1]) + 1
      }
    }
  }
  return dp[m][n]
};
```



### 1312.让字符串成为回文串的最少插入次数 

[ 我是链接](https://leetcode-cn.com/problems/minimum-insertion-steps-to-make-a-string-palindrome)

```js
/**
 * base_case: i === j 时，相同字符即为回文不需要插入，值为0
 * dp[i][j]表示：字符串s[i...j]变成回文串的最小插入操作
 * s[i] === s[j]：两端相同不需要操作，dp[i][j] = dp[i+1][j-1]
 * s[i] !== s[j]: min(dp[i+1][j], dp[i][j-1]) + 1
 */
var minInsertions = function(s) {
  const n = s.length
  let dp = new Array(n)
  for (let i = 0; i < dp.length; i++) {
    dp[i] = new Array(n).fill(0)
  }
  for (let i = n - 2; i >= 0; i--) {
    for (let j = i + 1; j < n; j++) {
      if (s[i] === s[j]) {
        dp[i][j] = dp[i+1][j-1]
      } else {
        dp[i][j] = Math.min(dp[i+1][j], dp[i][j-1]) + 1
      }
    }
  }
  return dp[0][n - 1]
};
```

### 651.四键键盘 

[我是链接](https://leetcode-cn.com/problems/4-keys-keyboard/)

```js
/**
 * base_case: dp[0] = 0
 * dp[i] 表示：操作i次后A的最大数量,return dp[N]
 * dp[i]有两个选择
 * 1. 最后一次按A，dp[i] = dp[i -1] + 1
 * 2. 最后一次按的C-V粘贴，dp[i] = dp[j - 2] * 粘贴次数， j < i,复制粘贴要2个操作
 * dp[i] = max(dp[i-1] + 1, dp[j] * (i-j+1))
 */
var maxA = function(n) {
 let dp = [0]
 for (let i = 1; i < n + 1; i++) {
   dp[i] = dp[i-1] + 1
   for (let j = 2; j < i; j++) {
     dp[i] = Math.max(dp[i], dp[j-2] * (i-j+1)) // C-A + C-V
   }
 }
 return dp[n]
};
```

## 背包系列问题

### 经典01背包问题  

```js
const knapsack = (N, W, wt, val) => {
  // base_case: 二维dp 为 0 
  // dp[i][w] 表示： 前 i 个物品放入 w 容量的背包 最大价值为 dp[i][w]
  // 对于第 i 个物品，可以选择装包或者不装包
  // 1. 若第 i 物品 超过剩余背包容量，一定不能装包： dp[i][w] = dp[i - 1][w]
  // 2. 第 i 物品 可以放入包内，此时可以选择装包 ｜ 不装包：  dp[i][w] = max(dp[i - 1][w], dp[i - 1][w - wt[i - 1]] + val[i - 1])
  let dp = new Array(N+1).fill(0).map(() => new Array(W + 1).fill(0))
  for (let i = 1; i < N + 1; i++) {
    for (let w = 1; w < W + 1; w++) {
      if (w - wt[i - 1] < 0) {
        dp[i][w] = dp[i - 1][w]
      } else {
        dp[i][w] = Math.max(dp[i - 1][w], dp[i - 1][w - wt[i - 1]] + val[i - 1])
      }
    }
  }
  return dp[N][W]
}

knapsack(5, 10, [1,2,3,4,5], [5,4,3,2,1]) // 14
```

### 416.分割等和子集 

[我是链接](https://leetcode-cn.com/problems/partition-equal-subset-sum/)

```js
/**
 * dp[i][w] 表示： 前 i 个物品是否能刚好装满w容量
 * base_case_1: dp[0][...] = false，没有物品没法装，false
 * base_case_2: dp[...][0] = true，没有容量无需装，false
 * 分割成两个等和，也就是只要前i个能刚好装满 sum/2 就行
 * 两个操作：
 * 1. 装： dp[i][w] = dp[i-1][w - wt[i-1]]
 * 2. 不装或者没法装: dp[i][w] = dp[i-1][w]
 */
/**
 * @param {number[]} nums
 * @return {boolean}
 */
var canPartition = function(nums) {
  let sum = nums.reduce((a, b) => a + b)
  if (sum % 2 !== 0) return false
  sum /= 2
  const n = nums.length
  let dp = new Array(n + 1)
  for (let i = 0; i < n + 1; i ++) {
    dp[i] = new Array(sum + 1).fill(false)
    dp[i][0] = true
  }
  for (let i = 1; i < n + 1; i ++) {
    for (let w = 1; w < sum + 1; w ++) {
      if (nums[i - 1] > w) {
        dp[i][w] = dp[i -1][w]
      } else {
        dp[i][w] = dp[i -1][w] || dp[i -1][w - nums[i -1]]
      }
    }
  }
  return dp[n][sum]
};

```

### 494.目标和 

[我是链接](https://leetcode-cn.com/problems/target-sum/)

```js
/**
 * 把整数组分成正数集合A 和 负数集合 B，那么有
 * A - B = target
 * A = B + target
 * A + A = A + B + target = S + target
 * 所以A = (S+target) / 2
 * 即转化为 等和子集背包问题
 */
var findTargetSumWays = function(nums, target) {
  const S = nums.reduce((a, b) => a + b)
  if ((S + target) % 2 !== 0) return 0
  let sum = (target + S) / 2 // 必须整数
  const n = nums.length
  let dp = new Array(n + 1)
  for (let i = 0; i < n + 1; i ++) {
    dp[i] = new Array(sum + 1).fill(0)
    dp[i][0] = 1
  }
  for (let i = 1; i < n + 1; i ++) {
    // 存在 0 ，所以 w 仍旧有 0 的情况存在
    for (let w = 0; w < sum + 1; w ++) {
      if (nums[i - 1] > w) {
        // 装不下
        dp[i][w] = dp[i - 1][w]
      } else {
        // 不装 + 装下小的
        dp[i][w] = dp[i - 1][w] + dp[i - 1][w - nums[i - 1]]
      }
    }
  }
  return dp[n][sum]
};
```

### 322.零钱兑换  

[我是链接](https://leetcode-cn.com/problems/coin-change/)

```js
/**
 * dp[i] 表示凑成金额 i 所需要的最少金币数
 * base_case: dp[0] = 0
 * dp[i] = min(dp[i], dp[i - coin] + 1)
 */
var coinChange = function(coins, amount) {
  let dp = new Array(amount + 1).fill(Infinity)
  dp[0] = 0
  for (let i = 1; i < amount + 1; i ++) {
    for (let coin of coins) {
      if (coin > i) {
        continue
      } else {
        dp[i] = Math.min(dp[i], dp[i - coin] + 1)
      }
    }
  }
  return dp[amount] === Infinity ? -1 : dp[amount]
};
```

### 518.零钱兑换II  

[我是链接](https://leetcode-cn.com/problems/coin-change-2/)

```js
/**
 * dp[i][w] 表示前 i 个硬币组成金额 w 的种数
 * base_case: dp[i][0] = 1, dp[0][w] = 0
 * choices:
 * 1. 金额 < 硬币面值: w < coin => dp[i][w] = dp[i - 1][w]
 * 2. 金额 >= 硬币面值： dp[i][w] = dp[i - 1][w] + dp[i][w - coions[i - 1]]
 */
var change = function(amount, coins) {
  const n = coins.length
  let dp = new Array(n + 1)
  for (let i = 0; i < n + 1; i ++) {
    dp[i] = new Array(amount + 1).fill(0)
    dp[i][0] = 1
  }
  for (let i = 1; i < n + 1; i ++) {
    for (let w = 1; w < amount + 1; w ++) {
      if (w < coins[i - 1]) {
        dp[i][w] = dp[i - 1][w]
      } else {
        dp[i][w] = dp[i - 1][w] + dp[i][w - coins[i - 1]]
      }
    }
  }
  return dp[n][amount]
};
```

### 198.打家劫舍 

[我是链接](https://leetcode-cn.com/problems/house-robber/)

```js
/**
 * base_case: dp[0] = nums[0], dp[1] =  max(nums[0], nums[1])
 * dp[i]表示：前i个房屋所能拿到的最多的钱
 * 两个选择：
 * 1. 不偷： dp[i] = dp[i-1]
 * 2. 偷: dp[i] = dp[i-2] + nums[i]
 */
 var rob = function(nums) {
  if (nums.length === 0) {
    return 0
  }
  let dp = [nums[0], Math.max(nums[0], nums[1])]
  for (let i = 2; i < nums.length; i++) {
    dp[i] = Math.max(dp[i-2] + nums[i], dp[i-1])
  }
  return dp[nums.length - 1]
};
```

### 213.打家劫舍II 

[我是链接](https://leetcode-cn.com/problems/house-robber-ii/)

```js
/**
 * 偷: dp[i - 2] + nums[i]
 * 不偷: dp[i-1]
 * 因为是环形所以只能：只偷第一家 ｜ 只偷最后一家
 */
var rob = function(nums) {
  if (nums.length === 1) {
    return nums[0]
  }
  // 打家劫舍实现代码
  const robRange = nums => {
    if (nums.length === 0) {
      return 0
    }
    let dp = [nums[0], Math.max(nums[0], nums[1])]
    for (let i = 2; i < nums.length; i++) {
      dp[i] = Math.max(dp[i-2] + nums[i], dp[i-1])
    }
    return dp[nums.length - 1]
  }
  // 不偷第一家
  const tail = robRange(nums.slice(1))
  // 不偷最后一家
  const head = robRange(nums.slice(0, nums.length -1))
  return Math.max(tail, head)
};
```

### 337. 打家劫舍 III 

[我是链接](https://leetcode-cn.com/problems/house-robber-iii/)

```js
var rob = function(root) {
   // root 是对象的话， 采用{}不能作键值
  let memo = new Map() // 用哈希表存储树的计算值，避免重复计算
  const myRob = root => {
    if (root === null) {
      return 0
    }
    if (memo.get(root)) {
      return memo.get(root)
    }
    // 1. 根节点 加 左右子树的子树
    let hasRoot = root.val
    if (root.left) {
      hasRoot += myRob(root.left.left) + myRob(root.left.right)
    }
    if (root.right) {
      hasRoot += myRob(root.right.left) + myRob(root.right.right)
    }
    // 2. 左右子树
    const noRoot = myRob(root.left) + myRob(root.right)
    const res = Math.max(hasRoot, noRoot)
    memo.set(root, res)
    return res
  }
  return myRob(root)
};
```

## 其他

### 10.正则表达式匹配 

[我是链接](https://leetcode-cn.com/problems/regular-expression-matching/)

```js
/**
 * @param {string} s
 * @param {string} p
 * @return {boolean}
 */
var isMatch = function(s, p) {
  const m = s.length
  const n = p.length
  const dp = (s, i, p, j) => {
    // 边界处理
    if (j === n) {
      // j 到底， 看 i 是否也走到底, eg. s = 'aaaa', p = 'a*'
      return i === m
    }
    if (i === m) {
      // 匹配空串， 一定是字符和 * 成对出现，eg. s = '', p = 'a*c*b*'
      if ((n - j) % 2 === 1) {
        return false
      }
      // 后一位 是 * 可以进行匹配
      while (j + 1 < n) {
        if (p[j + 1] !== '*') {
          return false
        }
        j = j + 2
      }
      return true
    }
    // 当前位匹配
    if (s[i] === p[j] || p[j] === '.') {
      // 后一位是 * 匹配 1 次 或者 多次
      if (j < n && p[j + 1] === '*') {
        return dp(s, i, p, j + 2) || dp(s, i + 1, p, j) // j + 2 跳过 *
      }
      // 当前位匹配 1 次
      return dp(s, i + 1, p, j + 1)
    } else {
      // 后一位是 * 仍可以进行匹配 1 次
      if (j < n && p[j + 1] === '*') {
        return dp(s, i, p, j + 2) // j + 2 跳过 *
      }
      return false
    }
    
  }
  return dp(s, 0, p, 0)
};
```

### 887.高楼扔鸡蛋 

[我是链接](https://leetcode-cn.com/problems/super-egg-drop/)

```js
/**dcm
 * dp[i][j] 表示：拥有 i 个鸡蛋 和最多允许的扔鸡蛋次数 j  所能确认的最高层数 f
 * 其中 1 <= i <= k, 0 <= f <= n
 * 摔碎： 鸡蛋数 - 1， 扔鸡蛋次数 - 1
 * 未摔碎： 鸡蛋数不变，扔鸡蛋次数 - 1
 * 总楼层数 = 当前楼 + 楼上层数(未碎) + 楼下层数（摔碎）
 * dp[i][j] = 1 + dp[i][j - 1] + dp[i - 1][j - 1]
 * base_case: dp[0][j] = 0, dp[i][0] = 0
 */
var superEggDrop = function(k, n) {
  let dp = new Array(k + 1).fill(0).map(_ => Array(n + 1).fill(0))
  let m = 0
  while (dp[k][m] < n) {
    m++
    for (let i = 1; i < k + 1; i++) {
      dp[i][m] = 1 + dp[i][m - 1] + dp[i - 1][m - 1]
    }
  }
  return m
};
```

### 312. 戳气球 

[我是链接](https://leetcode-cn.com/problems/burst-balloons/)

```js
/**
 * dp[i][j]表示：戳破i 和 j 间不包括(i, j)所有气球所能得到的分数
 * points: 加入两个虚拟气球，即戳破 0 和 n + 1 间的所有气球🎈所能得到的最高分数
 * base_case: i === j 时，中间没有气球没得戳，值为 0
 * k 表示i和j间最后一个被戳破的气球下标
 * dp[i][k]：k左边已经被戳破的气球分值
 * dp[k][j]：k右边已经被戳破的气球分值
 * 则有 dp[i][j] = dp[i][k] + point[i] * point[k] * point[j] + dp[k][j]
 */
var maxCoins = function(nums) {
  const point = [1, ...nums, 1]
  const n = point.length
  const dp = new Array(n).fill(0).map(_ => Array(n).fill(0))
  for (let i = n - 2; i >= 0; i --) {
    for (let j = i + 1; j < n; j ++) {
      for (let k = i + 1; k < j; k ++) {
        const count = dp[i][k] + point[k] * point[i] * point[j] + dp[k][j]
        dp[i][j] = Math.max(dp[i][j], count)
      }
    }
  }
  return dp[0][n - 1]
};
```

# 手写数据结构 2021-08-24 18:14 ✅

### 155.最小栈<sup>1.5k</sup>

[我是链接](https://leetcode-cn.com/problems/min-stack/)

```js
// 思路: 用两个栈，一个min栈用来存每次新增时当前栈最小值
class MinStack {
  constructor () {
    this.stack = []
    this.minStack = []
  }
  top () {
    return this.stack[this.stack.length - 1]
  }
  push (x) {
    this.stack.push(x)
    const min = Math.min(...this.stack)
    this.minStack.push(min)
  }
  pop () {
    this.stack.pop()
    this.minStack.pop()
  }
  getMin () {
    return this.minStack[this.minStack.length - 1]
  }
}
```

### 146.LRU 缓存 <sup>1.3k</sup>

[我是链接](https://leetcode-cn.com/problems/lru-cache/)

```js
class ListNode {
  // 双向链表节点，包括前驱后继
  constructor (key, value) {
    this.key = key
    this.value = value
    this.prev = null
    this.next = null
  }
}

class LRUCache {
  constructor (capacity) {
    this.capacity = capacity
    this.count = 0
    this.hash = {}
    // 哑头尾节点，方便对头尾节点进行操作
    this.dummyHead = new ListNode()
    this.dummyTail = new ListNode()
    this.dummyHead.next = this.dummyTail
    this.dummyTail.prev = this.dummyHead
  }
  // 读取
  get (key) {
    const node = this.hash[key]
    if (node) {
      // 节点存在，删除节点并将节点添加至头部
      this.removeFromList(node)
      this.addToHead(node)
      return node.value
    }
    return -1
  }
  // 写入
  put (key, value) {
    let node = this.hash[key]
    if (node) {
      // 节点已存在：更新value，移至头部
      node.value = value
      this.removeFromList(node)
      this.addToHead(node)
    } else {
      // 节点不存在：新建节点，【更新哈希表】
      // 1. 容量已满：移除尾部，添加节点至头部
      // 2. 容量未满：添加至头部，节点数 count + 1
      node = new ListNode(key, value)
      if (this.count === this.capacity) {
        const tail = this.dummyTail.prev
        this.removeFromList(tail)
        delete this.hash[tail.key]
        this.count--
      }
      this.hash[key] = node
      this.addToHead(node)
      this.count++
    }
  }
  // 删除节点
  removeFromList (node) {
    //  1 <=> 2(node) <=> 3 变成 1 <=> 3
    node.prev.next = node.next
    node.next.prev = node.prev
  }
  // 添加节点至头部
  addToHead (node) {
    // dummy <=> 2 变成 dummy <=> 1(node) <=> 2
    node.prev = this.dummyHead
    node.next = this.dummyHead.next
    this.dummyHead.next.prev = node
    this.dummyHead.next = node
  }
}
```

### 460.LFU缓存

[我是链接](https://leetcode-cn.com/problems/lfu-cache/)

```js
class LFUCache {
  constructor (capacity) {
    this.capacity = capacity
    this.KVMap = new Map() // key => value
    this.KFMap = new Map() // key => freq
    this.FKMap = new Map() // freq => Set(key1, key2)，这里用 Set 存储 key 可以方便增删去重
    this.minFreq = 0
  }
  addFreq (key) {
    // 获取现有次数 和 次数相应的 key 集合
    const freq = this.KFMap.get(key)
    const keySet = this.FKMap.get(freq)
    // 更新最小 freq
    if (this.minFreq === freq && keySet.size === 1) {
      this.minFreq += 1
    }
    // 当前次数集合去除原始key
    keySet.delete(key)
    // 更新 KF 和 FKSet
    this.KFMap.set(key, freq + 1)
    let newKeySet = this.FKMap.get(freq + 1)
    if (!newKeySet) {
      newKeySet = new Set()
      this.FKMap.set(freq + 1, newKeySet)
    }
    newKeySet.add(key)
  }
  get (key) {
    if (this.KVMap.has(key)) {
      // 增加次数
      this.addFreq(key)
      return this.KVMap.get(key)
    }
    return -1
  }
  put (key, value) {
    if (this.capacity === 0) return
    if (this.KVMap.has(key)) {
      this.KVMap.set(key, value)
      this.addFreq(key)
    } else {
      // 容量已满
      if (this.KVMap.size === this.capacity) {
        // 找到最小次数对应的最老 key
        const minKeySet = this.FKMap.get(this.minFreq) // Set{key1, key2}, 取出 key1
        const minKey = minKeySet.keys().next().value
        // 删除映射中的 key
        this.KVMap.delete(minKey)
        this.KFMap.delete(minKey)
        minKeySet.delete(minKey)
      }
      // 新增一个 key 及其映射
      this.KVMap.set(key, value)
      this.KFMap.set(key, 1)
      let oneSet = this.FKMap.get(1)
      if (!oneSet) {
        oneSet = new Set()
        this.FKMap.set(1, oneSet)
      }
      oneSet.add(key)
      this.minFreq = 1
    }
  }
}
```

# 单调栈｜队列

```js
// 模板
let res = [] // 存放答案
let stack = []
for (let i = n - 1; i >= 0; i --) {
  let top = stack[stack.length - 1]
  while (stack.length && top <= nums[i]) {
    stack.pop()
  }
  let restTop = stack[stack.length - 1]
  res[i] = stack.length ? restTop : -1
  stack.push(nums[i])
}
```

### 739.每日温度 

[我是链接](https://leetcode-cn.com/problems/daily-temperatures/)

```js
var dailyTemperatures = function(temperatures) {
  const n = temperatures.length
  let res = []
  let deque = []
  for (let i = n - 1; i >= 0; i --) {
    while (deque.length && temperatures[deque[deque.length - 1]] <= temperatures[i]) {
      deque.pop()
    }
    res[i] = deque.length ? deque[deque.length - 1] - i : 0
    deque.push(i)
  }
  return res
};
```

### 496.下一个更大的元素I 

[我是链接](https://leetcode-cn.com/problems/next-greater-element-i/)

```js
var nextGreaterElement = function(nums1, nums2) {
  const n = nums2.length
  let s = []
  let hash = {} //存放答案，作为 nums1 的映射
  let res = []
  for (let i = n - 1; i >= 0; i --) {
    while (s.length && s[s.length - 1] <= nums2[i]) {
      s.pop()
    }
    hash[nums2[i]] = s.length ? s[s.length - 1] : -1
    s.push(nums2[i])
  }
  for (let i = 0; i < nums1.length; i ++) {
    res[i] = hash[nums1[i]] || -1
  }
  return res
};
```

### 503.下一个更大的元素II  

[我是链接](https://leetcode-cn.com/problems/next-greater-element-ii/)

```js
// 这里数组是环形的，可以将原数组接在最后一个元素后面
var nextGreaterElements = function(nums) {
  let arr = [...nums, ...nums]
  const n = arr.length
  let res = []
  let s = []
  for (let i = n - 1; i >= 0; i --) {
    while (s.length && s[s.length - 1] <= arr[i]) {
      s.pop()
    }
    res[i] = s.length ? s[s.length - 1] : -1
    s.push(arr[i])
  }
  return res.slice(0, n / 2)
};
```

#### 处理环形数组

```js
// let arr = [1, 2, 3]
// const n = arr.length
// for (let i = 2 * n - 1; i >= 0; i --) {
// 	console.log(i%n, arr[i%n])
// }
var nextGreaterElements = function(nums) {
  const n = nums.length
  let res = []
  let s = []
  for (let i = 2 * n - 1; i >= 0; i --) {
    while (s.length && s[s.length - 1] <= nums[i % n]) {
      s.pop()
    }
    res[i % n] = s.length ? s[s.length - 1] : -1
    s.push(nums[i % n])
  }
  return res
};
```

### 239.滑动窗口最大的值 

[我是链接](https://leetcode-cn.com/problems/sliding-window-maximum/)

```js
var maxSlidingWindow = function(nums, k) {
  const n = nums.length
  let deque = []
  let res = []
  for (let i = 0; i < n; i ++) {
    // 合法性检测,超出窗口删除
    if (i - deque[0] > k - 1) {
      deque.shift()
    }
    // 保持单调递减，将小于新加入元素的从队列弹出
    while (deque.length && nums[deque[deque.length - 1]] <= nums[i]) {
      deque.pop()
    }
    deque.push(i)
    if (i >= k - 1) {
      res.push(nums[deque[0]])
    }
  }
  return res
};
```

# 树

## 手把手二叉树

### 226. 翻转二叉树

[我是链接](https://leetcode-cn.com/problems/invert-binary-tree/)

```javascript
var invertTree = function(root) {
  // 交换当前节点的左右节点，剩下的递归就行
  if (root === null) return root
  const temp = root.left
  root.left = root.right 
  root.right = temp
  
  invertTree(root.left)
  invertTree(root.right)
  return root
};
```

### 116. 填充每个节点的下一个右侧节点指针

[我是链接](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

```javascript
var connect = function(root) {
  if (root === null) return root
  const connectTwoNodes = (nodeL, nodeR) => {
    // 连接两个节点
    if (nodeL === null || nodeR === null) {
      return null
    }
    nodeL.next = nodeR
    // 左节点 -> 右节点
    connectTwoNodes(nodeL.left, nodeL.right)
    connectTwoNodes(nodeR.left, nodeR.right)
    // 左子树的右节点 跨区链接 右子树的左节点
    connectTwoNodes(nodeL.right, nodeR.left)
  }
  connectTwoNodes(root.left, root.right)
  return root
};
```

### 114.二叉树展开为链表

[我是链接](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

```javascript
var flatten = function(root) {
  if (root === null) return
  // 后序遍历
  flatten(root.left)
  flatten(root.right)
  // 将左子树作为右子树，再将原先的右子树接在当前右子树底部
  let temp = root.right
  root.right = root.left
  root.left = null
  let p = root
  while (p.right) {
    p = p.right
  }
  p.right = temp
};
```

### 654.最大二叉树

[我是链接](https://leetcode-cn.com/problems/maximum-binary-tree/)

```javascript
var constructMaximumBinaryTree = function(nums) {
  // 找出最大值，切割左右子数组作为左右子树
  const buildTree = (nums, start, end) => {
    if (start > end) return null
    let maxIndex = -1
    let maxNum = -Infinity
    for (let i = start; i <= end; i ++) {
      if (nums[i] > maxNum) {
        maxNum = nums[i]
        maxIndex = i
      }
    }
    let root = new TreeNode(maxNum)
    root.left = buildTree(nums, start, maxIndex - 1)
    root.right = buildTree(nums, maxIndex + 1, end)
    return root
  }
  return buildTree(nums, 0, nums.length - 1)
};
```

### 105. 从前序与中序遍历序列构造二叉树 

[我是链接](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal)

```js
/**
 * 核心： 定位根节点位置，划分左右子树，再递归
 * 前序遍历：[中 | 左 | 右] , 中序遍历：[左 | 中 | 右]
 * 用一个哈希表记录中序遍历数组的节点下标,前序遍历的第一个即是根节点
 * 拿到中序遍历中根节点的下标为 mid, 记中序左部分长度为 leftLen = mid - inStart
 * 前序遍历: [
 * prestart |  preStart + 1 ... prestart + leftLen | prestart + leftLen + 1...*  preEnd
 * ]
 * 中序数组： [inStart...mid - inStart | mid |  mid + 1 ... inEnd]
 */
var buildTree = function(preorder, inorder) {
  const iMap = new Map()
  inorder.map((item, index) => {
    iMap.set(item, index)
  })
  const helper = (pStart, pEnd, iStart, iEnd) => {
    if (pStart > pEnd) return null
    const root = new TreeNode(preorder[pStart])
    const mid = iMap.get(preorder[pStart])
    const inLeftLen = mid - iStart
    root.left = helper(pStart + 1, pStart + inLeftLen, iStart, mid - 1)
    root.right = helper(pStart + inLeftLen + 1, pEnd, mid + 1, iEnd)
    return root
  }
  return helper(0, preorder.length - 1, 0, inorder.length - 1)
};
```

## 手把手二叉搜索树

### 100.相同的树  

[我是链接](https://leetcode-cn.com/problems/same-tree/)

```js
var isSameTree = function(p, q) {
  if (p === null && q === null) {
    return true
  }
  if (p === null || q === null) {
    return false
  }
  if (p.val !== q.val) {
    return false
  }
  return isSameTree(p.left, q.left) && isSameTree(p.right, q.right)
};
```

### 98.验证二叉搜索树 

[我是链接](https://leetcode-cn.com/problems/validate-binary-search-tree/)

```js
/**
 * 根节点的值是所有其左子树的最大值，所有右子树的最小值
 */
var isValidBST = function(root) {
  const isValid = (root, minNode, maxNode) => {
    if (!root) {
      return true
    }
    if (minNode && minNode.val >= root.val) {
      return false
    }
    if (maxNode && maxNode.val <= root.val) {
      return false
    }
    return isValid(root.left, minNode, root) && isValid(root.right, root, maxNode)
  }
  return isValid(root, null, null)
};
```

### 701. 二叉搜索树中的插入操作 

[我是链接](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

```js
var insertIntoBST = function(root, val) {
  // 在空位插入新节点
  if (root === null) {
    root = new TreeNode(val)
  }
  // 左右节点进行插入
  if (root.val < val) {
    root.right = insertIntoBST(root.right, val)
  }
  if (root.val > val) {
    root.left = insertIntoBST(root.left, val)
  }
  return root
};
```

### 450. 删除二叉搜索树中的节点 

[我是链接](https://leetcode-cn.com/problems/delete-node-in-a-bst)

```js
var deleteNode = function(root, key) {
  if (root === null) return null
  const getMinNode = node => {
    while (node.left) {
      node = node.left
    }
    return node
  }
  if (root.val === key) {
    // 执行删除操作
    // 没有左右子树 直接删除返回剩余节点即可
    if (!root.left) return root.right
    if (!root.right) return root.left
    // 拥有左右子树，找出左子树的最大值或者右子树的最小值替换被删除的节点
    const minNode = getMinNode(root.right)
    root.val = minNode.val
    root.right = deleteNode(root.right, minNode.val)
  }
  if (root.val < key) {
    root.right = deleteNode(root.right, key)
  }
  if (root.val > key) {
    root.left = deleteNode(root.left, key)
  }
  return root
};
```

### 96.不同的二叉搜索树

[我是链接](https://leetcode-cn.com/problems/unique-binary-search-trees/)

```javascript
var numTrees = function(n) {
  // 用备忘录减少时间
  let memo = new Array(n + 1).fill(0).map(_ => Array(n + 1).fill(0))
  let res = 0
  // 计算[start, n]间的节点
  const countNodes = (start, end) => {
    if (start > end) return 1
    if (memo[start][end]) {
      return memo[start][end]
    }
    for (let i = start; i <= end; i ++) {
      const leftNodes = countNodes(start, i - 1)
      const rightNodes = countNodes(i + 1, end)
      res += leftNodes * rightNodes
    }
    memo[start][end] = res
    return res
  }
  return countNodes(1, n)
};
```

### 95.不同的二叉搜索树 II

[我是链接](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

```javascript
var generateTrees = function(n) {
  if (n === 0) return []
  const build = (start, end) => {
    let res = []
    if (start > end) {
      res.push(null)
      return res
    }
    for (let i = start; i <= end; i ++) {
      const leftTree = build(start, i - 1)
      const rightTree = build(i + 1, end)
      for (let left of leftTree) {
        for (let right of rightTree) {
          const root  = new TreeNode(i)
          root.left = left
          root.right = right
          res.push(root)
        }
      }
    }
    return res
  }
  return build(1, n)
};
```

### 222.完全二叉树的节点数 

[我是链接](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

```js
var countNodes = function(root) {
  // 计算左右子树高度
  let tempL = root
  let tempR = root
  let leftHeight = 0
  let rightHeight = 0
  while (tempL) {
    tempL = tempL.left
    leftHeight ++
  }
  while (tempR) {
    tempR = tempR.right
    rightHeight ++
  }
  // 高度相同 返回 2^n - 1
  if (leftHeight === rightHeight) {
    return Math.pow(2, leftHeight) - 1
  }
  // 高度不同： 当前节点 + 左子树节点数 + 右子树节点数
  return 1 + countNodes(root.left) + countNodes(root.right)
};
```

### 144.二叉树的前序遍历 

[我是链接](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)


```javascript
// 递归
var preorderTraversal = function(root) {
  let res = []
  const pre = (root) => {
    if (!root) {
      return
    }
    res.push(root.val)
    pre(root.left)
    pre(root.right)
  }
  pre(root)
  return res
};
```


![](https://pic.leetcode-cn.com/1600934720-bMXWmu-%E4%BA%8C%E5%8F%89%E6%A0%91%E5%89%8D%E5%BA%8F%E9%81%8D%E5%8E%86%EF%BC%88%E8%BF%AD%E4%BB%A3%E6%B3%95%EF%BC%89.gif#id=XDMij&originalType=binary&status=done&style=none#id=Ise9K&originalType=binary&status=done&style=none)


```javascript
var preorderTraversal = function(root) {
  // 前序： 中左右 => 入栈顺序：右左中
  if (!root) {
    return []
  }
  let stack = [root]
  let res = []
  while (stack.length) {
    const node = stack.pop()
    node.right && stack.push(node.right)
    node.left && stack.push(node.left)
    res.push(node.val)
  }
  return res
};
```

### 94.二叉树的中序遍历 

[我是链接](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)


```javascript
// 递归
var inorderTraversal = function(root) {
  let res = []
  const inorder = (root) => {
    if (!root) {
      return 
    }
    inorder(root.left)
    res.push(root.val)
    inorder(root.right)
  }
  inorder(root)
  return res
};
```


![](https://pic.leetcode-cn.com/1600934697-oafdTT-%E4%BA%8C%E5%8F%89%E6%A0%91%E4%B8%AD%E5%BA%8F%E9%81%8D%E5%8E%86%EF%BC%88%E8%BF%AD%E4%BB%A3%E6%B3%95%EF%BC%89.gif#id=WIO0w&originalType=binary&status=done&style=none#id=pbFoq&originalType=binary&status=done&style=none)


```javascript
// 迭代
var inorderTraversal = function(root) {
  if (!root) {
    return []
  }
  let stack = []
  let res = []
  while (root || stack.length) {
    // 左
    if (root) {
      stack.push(root)
      root = root.left
    } else {
      root = stack.pop()
    	// 中
    	res.push(root.val)
    	// 右
    	root = root.right
    }
  }
  return res
};
```

###  230.二叉搜索树中第K小的元素

[我是链接](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/)

```javascript
var kthSmallest = function(root, k) {
  // 对 root 中序遍历，并用一个count遍历记录便利次数，次数到达 k 返回结果
  let count = 0
  let res = null
  const midTravel = (root, k) => {
    if (!root) return null
    midTravel(root.left, k)
    count ++
    if (count === k) {
      res = root.val
    }
    midTravel(root.right, k)
  }
  midTravel(root, k)
  return res
};
```

### 145.二叉树的后序遍历 

[我是链接](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)


```javascript
// 递归
var postorderTraversal = function(root) {
  let res = []
  const post = (root) => {
    if (!root) {
      return []
    }
    post(root.left)
    post(root.right)
    res.push(root.val)
  }
  post(root)
  return res
};
```


```javascript
var postorderTraversal = function(root) {
  // 后序遍历： 左右中
  // 可以借鉴前序遍历再对结果进行反转： 中右左 => 左右中
  // 需要注意的是前序遍历是：中左右不是中右左
  if (root === null) return []
  let stack = [root]
  let res = []
  while (stack.length) {
    const node = stack.pop()
    node.left && stack.push(node.left)
    node.right && stack.push(node.right)
    res.push(node.val)
  }
  for (let i = 0, j = res.length - 1; i <= j ; i ++, j --) {
    [res[i], res[j]] = [res[j], res[i]]
  }
  return res
};
```

### 102.二叉树的层序遍历  

[我是链接](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)


```javascript
// 用一个队列（先进先出）存储每个节点的左右子树
var levelOrder = function(root) {
  if (root === null) return []
  let queue = [root]
  let res = []
  while (queue.length) {
    const n = queue.length
    let arr = []
    for (let i = 0; i < n; i ++) {
      const node = queue.shift()
      arr.push(node.val)
      node.left && queue.push(node.left)
      node.right && queue.push(node.right)
    }
    res.push(arr)
  }
  return res
};
```

### 111.二叉树最小高度

[我是链接](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)


```javascript
// 层序遍历找到没有子树的节点，其深度即为最小深度
var minDepth = function(root) {
  if (root === null) return 0
  let depth = 1
  let queue = [root]
  while (queue.length) {
    const n = queue.length 
    for (let i = 0; i < n; i ++) {
      const node = queue.shift()
      if (!node.left && !node.right) {
        return depth
      }
      node.left && queue.push(node.left)
      node.right && queue.push(node.right)
    }
    depth ++
  }
  return depth
};
```

### 236.二叉树的最近公共祖先 

[我是链接](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

````js
var lowestCommonAncestor = function(root, p, q) {
  if (root === null) return root
  if (root === p || root === q) {
    return root
  }
  const commonLeft = lowestCommonAncestor(root.left, p, q)
  const commonRight = lowestCommonAncestor(root.right, p, q)
  // p 和 q 都存在，返回 root
  if (commonLeft && commonRight) {
    return root
  }
  return commonLeft || commonRight || null
};
````

### 124. 二叉树中的最大路径和 

[我是链接](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum)

```js
var maxPathSum = function(root) {
  let res = -Infinity
 const countSum = root => {
  if (root === null) return 0
  const left = Math.max(0, countSum(root.left))
  const right = Math.max(0, countSum(root.right))
  // 最大路径和 = 当前节点 + 左子树路径和 + 右子树和
  res = Math.max(res, root.val + left + right)
  // 路径走法： 当前节点 + 左右子树最大值
  return Math.max(left, right) + root.val
 }
 countSum(root)
 return res
};
```

# 链表

### 234.回文链表

[我是链接](https://leetcode-cn.com/problems/palindrome-linked-list/)

- 哈希表
- flag 标记已访问的节点
- 以下代码是**快慢指针**，无需开拓空间

```js
var isPalindrome = function(head) {
  // 快慢指针获取中间节点
  let fast = head
  let slow = head
  while (fast && fast.next) {
    fast = fast.next.next
    slow = slow.next
  }
  // 翻转链表
  const reverse = (head) => {
    let current = null
    let newHead = head
    while (head && head.next) {
      current = head.next
      head.next = current.next
      current.next = newHead
      newHead = current
    }
    return newHead
  }
  slow = reverse(slow)
  // 从头、以及反转后的节点开始一次遍历比较
  let p1 = head
  let p2 = slow
  while (p1 && p2) {
    if (p1.val !== p2.val) {
      return false
    }
    p1 = p1.next
    p2 = p2.next
  }
  return true
};
```

### 206.反转链表

[我是链接](https://leetcode-cn.com/problems/reverse-linked-list/)

```js
// 递归
var reverseList = function(head) {
  // 链表节点只有一个，返回它自身
  if (head === null || head.next === null) return head
  // reverseList 函数的定义是以head节点为起点反转链表
  // head(1)-> reverseList(2 -> 3 -> 4 -> null)
  // 最终：head -> 2 <- 3 <- 4(last)
  const last = reverseList(head.next)
  head.next.next = head
  head.next = null
  return last
};
```

迭代

![img](https://pic.leetcode-cn.com/1624782858-oyJziv-008eGmZEly1gnrf1oboupg30gy0c44qp.gif)

```js
var reverseList = function(head) {
  let pre = null
  let current = head
  while (current) {
    let temp = current.next
    current.next = pre
    pre = current
    current = temp
  }
  return pre
};
```

### 92.反转链表II

[我是链接](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

递归

```js
var reverseBetween = function(head, left, right) {
  // 函数定义： 反转链表的前 n 个节点
  let successor = null
  const reverseN = (head, n) => {
    if (n === 1) {
      successor = head.next
      return head
    }
    const last = reverseN(head.next, n - 1)
    head.next.next = head
    head.next = successor
    return last
  }
  // left = 1 时相当于反转前 right个
  if (left == 1) {
    return reverseN(head, right)
  }
  head.next = reverseBetween(head.next, left - 1, right - 1)
  return head
};
```

迭代

```js
var reverseBetween = function(head, left, right) {
  const dummyNode = new ListNode(-1)
  dummyNode.next = head
  let pre = dummyNode
  for (let i = 0; i < left - 1; i++) {
    pre = pre.next
  }
  let cur = pre.next
  for (let j = 0; j < right - left; j++) {
    let next = cur.next;
    cur.next = next.next;
    next.next = pre.next;
    pre.next = next;
  }
  return dummyNode.next
};
```

### 25.k 个一组反转链表

[我是链接](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

```js
var reverseKGroup = function(head, k) {
  let start = head
  let end = head
  for (let i = 0; i < k; i ++) {
    if (end === null) {
      return head
    }
    end = end.next
  }
  // 翻转 start 到 end 间的节点
  const reverse = (start, end) => {
    let pre = null, cur = start, nxt = start
    while (cur !== end) {
      nxt = cur.next
      cur.next = pre
      pre = cur
      cur = nxt
    }
    return pre
  }
  const last = reverse(start, end)
  start.next = reverseKGroup(end, k)
  return last
};
```

# 回溯算法（DFS）

## 回溯模版

```js
res = []
function backTrack (path, list) {
  if (满足条件) {
    res.push(list)
    return
  }
  for (选择 in list) {
    // 前序遍历：做选择
    backtrack(路径, 选择列表)
    // 后序遍历：撤销选择
  }
}
```

## 46. 全排列

[我是链接](https://leetcode-cn.com/problems/permutations/)

```js
var permute = function(nums) {
  let res = []
  const backTrack = (nums, track = []) => {
    if (track.length === nums.length) {
      res.push(track)
    }
    for (let item of nums) {
      if (!track.includes(item)) {
        track.push(item)
        backTrack(nums, [...track]) // 下面的pop会影响引用，故不能直接用 track 数组
        track.pop()
      }
    }
  }
  backTrack(nums, [])
  return res
};
```

## 78.  子集

[我是链接](https://leetcode-cn.com/problems/subsets/)

```js
// 用一个 start 排除 start 索引之前的数字
var subsets = function(nums) {
  let res = []
  const backTrack = (nums, track = [], startIndex = 0) => {
    res.push(track)
    for (let i = startIndex; i < nums.length; i ++) {
      track.push(nums[i])
      backTrack(nums, [...track], i + 1)
      track.pop()
    }
  }
  backTrack(nums, [], 0)
  return res
};
```

## 77.组合

[我是链接](https://leetcode-cn.com/problems/combinations/)

```js
var combine = function(n, k) {
  let res = []
  const backTrack = (n, k, track = [], startIndex = 1) => {
    if (track.length === k) {
      res.push(track)
    }
    for (let i = startIndex; i <= n; i ++) {
      track.push(i)
      backTrack(n, k, [...track], i + 1)
      track.pop()
    }
  }
  backTrack(n, k, [], 1)
  return res
};
```

## 51.N 皇后

[我是链接](https://leetcode-cn.com/problems/n-queens/)

```js
/**
 * 这里生成棋盘时不直接创建['....', '....']形式的棋盘是因为JS中不能直接对字符串的某位进行替换
 * 当然也可以直接创建, board[row][col] = 'Q' 需要通过字符串的substring 和 replace方法实现 较为繁琐
 */
var solveNQueens = function(n) {
  // 生成棋盘 n x n
  const emptyBoard = new Array(n).fill(0).map(_ => Array(n).fill('.'))
  // 是否能放置皇后
  const isValid = (board, row, col) => {
    for (let i = 0; i < n; i ++) {
      if (board[i][col] === 'Q') return false
    }
    for (let i = row - 1, j = col + 1; i >= 0 && j < n; i --, j ++) {
      if (board[i][j] === 'Q') return false
    }
    for (let i = row - 1, j = col - 1; i >= 0 && j >= 0; i --, j --) {
      if (board[i][j] === 'Q') return false
    }
    return true
  }
  // 按行回溯
  const backTrack = (board, row = 0) => {
    // 遍历到最后一行，皇后已摆放完毕，棋盘以【字符串】形式存入结果
    if (row === n) {
      const validBoard = board.map(i => i.join(''))
      res.push(validBoard)
      return
    }
    // 回溯模板
    for (let col = 0; col < n; col ++) {
      if (isValid(board, row, col)) {
        board[row][col] = 'Q'
        backTrack(board, row + 1)
        board[row][col] = '.'
      }
    }
  }
  let res = []
  backTrack(emptyBoard, 0)
  return res
};
```

## 37.解数独

[我是链接](https://leetcode-cn.com/problems/sudoku-solver/)

```js
var solveSudoku = function(board) {
  const isValid = (board, row, col, n) => {
    for (let i = 0; i < 9; i ++) {
      const R = Math.floor(row / 3) * 3 + Math.floor(i / 3)
      const C = Math.floor(col / 3) * 3 + Math.floor(i % 3)
      // 行是否重复
      if (board[row][i] === n) {
        return false
      }
      // 列是否重复
      if (board[i][col] === n) {
        return false
      }
      // 判断 3 x 3 小块是否存在重复
      if (board[R][C] === n) {
          return false
      }
    }
    return true
  }
  const backTrack = (board, row, col) => {
    // 到列尾，换下一行
    if (col === 9) {
      return backTrack(board, row + 1, 0)
    }
    // 到达最后一行，返回结果
    if (row === 9) {
      return true
    }
    // 跳过预设数字
    if (board[row][col] !== '.') {
      return backTrack(board, row, col + 1)
    }
    // 回溯模板
    for (let i = 1; i <= 9; i ++) {
      const ch = i.toString()
      if (isValid(board, row, col, ch)) {
        board[row][col] = ch
        if (backTrack(board, row, col + 1)) {
          return true
        }
        board[row][col] = '.'
      }
    }
    return false
  }
  if (backTrack(board, 0, 0)) {
    return board
  }
};
```

## 22. 括号生成

[我是链接](https://leetcode-cn.com/problems/generate-parentheses/)

```js
var generateParenthesis = function(n) {
  let res = []
  const backTrack = (left = n, right = n, track = []) => {
    // 一定会先消耗左括号，所以 left <= right
    if (right < left) return
    if (left < 0 || right < 0) return
    // 左右括号用完 返回结果
    if (left === 0 && right === 0) {
      res.push(track.join(''))
      return
    }
    // 选择：消耗左右括号数
    for (let choice of ['(', ')']) {
      track.push(choice)
      choice === '(' && backTrack(left - 1, right, [...track])
      choice === ')' && backTrack(left, right - 1, [...track])
      track.pop()
    }
  }
  backTrack(n, n, [])
  return res
};
```

# 面试高频

### 5.最长回文子串

[我是链接](https://leetcode-cn.com/problems/longest-palindromic-substring/)

```js
var longestPalindrome = function(s) {
  if (s.length < 2) return s
  let res = ''
  const palindrome = (start, end) => {
    while (start >= 0 && end < s.length && s[start] === s[end]) {
      start --
      end ++
    }
    const newLen = end - (start + 1)
    if (newLen > res.length) {
      res = s.slice(start + 1, end)
    }
  }
  for (let i = 0; i < s.length; i ++) {
    // 奇数中心扩散
    palindrome(i, i)
    // 偶数中心扩散
    palindrome(i, i + 1)
  }
  return res
};
```

### 204.素数 

[我是链接](https://leetcode-cn.com/problems/count-primes/)

```js
// 质数的倍数一定不是质数，从2开始标记质数的倍数，每找到一个质数，count 加一
var countPrimes = function(n) {
  let primes = new Array(n).fill(true)
  // 0 和 1 不是质数
  let count = 0
  for (let i = 2; i < n; i++) {
    if (primes[i]) {
      count ++
      for (let j = i * i; j < n; j += i) {
        primes[j] = false
      }
    }
  }
  return count
};
```

### 42.接雨水 

[我是链接](https://leetcode-cn.com/problems/trapping-rain-water/)

```js
/**
 * 对于位置 i 能装下多少水？ => min(左侧峰值, 右侧峰值) -  当前高度height[i]
 */
var trap = function(height) {
  let left = 0
  let right = height.length  - 1
  let maxLeft = height[left]
  let maxRight = height[right]
  let res = 0
  while (left <= right) {
    maxLeft = Math.max(height[left], maxLeft)
    maxRight = Math.max(height[right], maxRight)
    if (maxLeft < maxRight) {
      res += maxLeft - height[left ++]
    } else {
      res += maxRight - height[right --]
    }
  }
  return res
};
```

### 26.删除有序数组中的重复项  

[我是链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

```js
var removeDuplicates = function(nums) {
  let j = 0
  for (let i = 1; i < nums.length; i++) {
    if (nums[i] !== nums[j]) {
      nums[++j] = nums[i]
    }
  }
  return j + 1
};
```

### 55.跳跃游戏 

[我是链接](https://leetcode-cn.com/problems/jump-game/)

```js
/**
 * 思路：判断s[n - 1]前面的下标 i 加上s[i]能否到达 n - 1
 */
var canJump = function(nums) {
  const n = nums.length
  let maxJump = 0
  // i 只需要遍历到倒数第二个就行了，题目是到达最后一个
  for (let i = 0; i < n - 1; i++) {
    maxJump = Math.max(maxJump, i + nums[i])
    if (maxJump <= i) {
      // 跳完之后还在原地
      return false
    }
  }
  return maxJump >= n - 1
};
```

### 45.跳跃游戏II 

[我是链接](https://leetcode-cn.com/problems/jump-game-ii/)

```js
//  跳一次的最远跳跃距离 = 当前位置 + 可跳跃的最大数
var jump = function(nums) {
  const n = nums.length
  let step = 0
  let maxJump = 0
  let end = 0
  for (let i = 0; i < n - 1; i ++) {
    maxJump = Math.max(maxJump, i + nums[i])
    if (i === end) {
      end = maxJump
      step ++
    }
  }
  return step
};
```

动态规划

```js
/**
 * dp[i]表示：跳跃到i位置所需要的最小步数
 * base_case: dp[0] = 0, 
 * dp[i...n - 1] = n | Infinity
 * 选择： 跳： dp[i + j] = dp[i] + 1// 从dp[i]跳到 dp[i + j] 需要 1 步
 */
var jump = function(nums) {
  const n = nums.length
  let dp = new Array(n).fill(n)
  dp[0] = 0
  for (let i = 0; i < n - 1; i ++) {
    for (let j = 1; j <= nums[i]; j ++) {
      if (i + j >= n) {
        return dp[n - 1]
      }
      dp[i + j] = Math.min(dp[i + j], dp[i] + 1)
    }
  }
  return dp[n - 1]
};
```

### 969.煎饼排序

[我是链接](https://leetcode-cn.com/problems/pancake-sorting/)

```js
// 垃圾题，有个思路就行了，答案只要在范围内【10 * arr.length】怎么翻转都可以通过
// 找到前 n 个 最大值的下标 maxIndex
// 翻转前 maxIndex 个 再翻转前 n 个
var pancakeSort = function(arr) {
  const reverse = (start, end) => {
    while (start <= end) {
      [arr[start], arr[end]] = [arr[end], arr[start]]
      start ++
      end --
    }
  }
  let res = []
  const sort = (arr, n) => {
    if (n === 1) return
    let maxItem = -Infinity
    let maxIndex = 0
    for (let i = 0; i < n; i ++) {
      if (maxItem < arr[i]) {
        maxItem = arr[i]
        maxIndex = i
      }
    }
    reverse(0, maxIndex)
    res.push(maxIndex + 1)
    reverse(0, n - 1)
    res.push(n)
    sort(arr, n - 1)
  }
  sort(arr, arr.length)
  return res
};
```

###  560.和为k的子数组

> 前缀和数组 方便用于区间

[我是链接](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

![](https://pic.leetcode-cn.com/1610773274-TkbOFU-file_1610773273675)

哈希表

![微信截图_20210115194113](https://pic.leetcode-cn.com/1610773274-xuuVxS-file_1610773273681)

```js
var subarraySum = function(nums, k) {
  const n = nums.length
  let preSum = 0
  let hash = {} // 记录前缀和出现次数
  // base_case:前缀和为 0 的出现次数为最少为 1
  hash[0] = 1
  let res = 0
  // 构造前缀和
  for (let i = 0; i < n; i ++) {
    preSum += nums[i]
    if (hash[preSum - k]) {
      res += hash[preSum - k]
    }
    hash[preSum] = hash[preSum] ? hash[preSum] + 1 : 1
  }
  return res
};
```

### 224.计算器

[我是链接](https://leetcode-cn.com/problems/basic-calculator/)

```js
// 对op的处理有严格顺序要求：数值位计算 + 左括号 + 符号处理 + 右括号
var calculate = function(s) {
  let index = 0 // 用来记录遍历点
  const helper = s => {
    let stack = []
    let num = 0
    let sign = '+'
    const isNum = op => /\d/.test(op)
    while (index < s.length) {
      const op = s[index++]
      // 1.数值位计算
      if (isNum(op)) {
        num = 10 * num + Number(op)
      }
      // 2.左括号，数值位递归计算
      if (op === '(') {
        num = helper(s)
      }
      // 3.符号处理
      if (!isNum(op) && op !== ' ' || index === s.length) {
        switch (sign) {
          case '+': stack.push(num); break;
          case '-': stack.push(-num); break;
          case '*': stack.push(stack.pop() * num); break;
          case '/': stack.push(stack.pop() / num | 0); break;
        }
        sign = op
        num = 0
      }
      // 4.右括号
      if (op === ')') {
        break
      }
    }
		return stack.reduce((a, b) => a + b)
  }
  return helper(s)
};
```

### 20.有效的括号  

[我是链接](https://leetcode-cn.com/problems/valid-parentheses/)

```js
/**
 * 讲左括号存入栈，遇到右括号时与栈顶匹配，不匹配直接返回 false
 * 匹配则出栈，栈最终为空则完全匹配，返回 true
 */
var isValid = function(s) {
  const hash = {
    ']': '[',
    '}': '{',
    ')': '('
  }
  let stack = []
  for (let i = 0; i < s.length; i ++) {
    if (['(', '{', '['].includes(s[i])) {
      stack.push(s[i])
    } else {
      if (stack.length && stack[stack.length - 1] === hash[s[i]]) {
        stack.pop()
      } else {
        return false
      }
    }
  }
  return stack.length === 0
};
```



## 一行代码

### 292.Nim 游戏 

[我是链接](https://leetcode-cn.com/problems/nim-game/)

```js
var canWinNim = function(n) {
 // 1 + 3 === 4
 // 假如我想赢，那轮到我拿的时候必须剩下 1～3 颗石子
 // 也就是在上一步，对手在有4颗石子的情况，无论怎么拿都会留下 1～3 颗石子
 // 也就是只要把对方控制在4的倍数内，对方必输
 return n % 4 !== 0
};
```

### 877.石子游戏 

[我是链接](https://leetcode-cn.com/problems/stone-game/)

```js
var stoneGame = function(piles) {
  // [2, 1, 9, 5] 按照索引[1, 2, 3, 4] 可以分为奇数堆和偶数堆，且奇偶堆石子总数不一致，第一个拿的人总能控制自己拿奇数堆或者偶数堆，只要拿石子数更多的堆即可。
  return true
};
```

### 319.灯泡开关 

[我是链接](https://leetcode-cn.com/problems/bulb-switcher/)

```js
var bulbSwitch = function(n) {
  // 灯要亮就得按奇数次，假设 6 盏灯，那第六盏灯能被按的 6 = 1 * 6 = 2 * 3，因为因子成对出现，所以被按的次数是偶数 4 次，最终暗，但 4 = 1 * 4 = 2 * 2，由于因子2 重复，所以最后是奇数次 3次，也就是 能开平方的最终会亮。
  return Math.floor(Math.sqrt(n))
};
```
