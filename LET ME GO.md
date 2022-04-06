# LET ME GO

# 链表

## Day1: 单链表的六大解题思路

### 21. 合并两个有序链表

[我是链接](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

生成一个哑节点`current`，比较`list1 `和 `list2` 指针，将`较小的值的指针`拼接在current后，current指针前进，最后`将剩余的指针全拼在current后`。

利用哑节点可以避免处理空指针的情况，减少不必要的麻烦。

```javascript
var mergeTwoLists = function(list1, list2) {
  // 生成哑节点
  let dummyNode = new ListNode(-1)
  let current = dummyNode
  while (list1 && list2) {
  	// 将较小的值的指针拼接在current后
    if (list1.val < list2.val) {
      current.next = list1
      list1 = list1.next
    } else {
      current.next = list2
      list2 = list2.next
    }
    // 指针继续前进
    current = current.next
  }
  // 将剩余的指针全拼在current后
  current.next = list1 || list2
  return dummyNode.next
};
```

### 23.合并k个链表

[我是链接](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

#### 最小堆

由于js没有堆的数据结构，所以`将链表所有节点存入一个数组`中，再将数组`排序`，再从数组`重新生成一个完整链表`

>  优先队列 `pq` 中的元素个数最多是 `k`，所以一次 `poll` 或者 `add` 方法的时间复杂度是 `O(logk)`；所有的链表节点都会被加入和弹出 `pq`，
>
>  **所以算法整体的时间复杂度是 `O(Nlogk)`，其中 `k` 是链表的条数，`N` 是这些链表的节点总数**。

```js
var mergeKLists = function(lists) {
  let arr = []
  // 将所有【链表节点】存入数组，不能对lists使用flat的数组方法
  lists.map(i => {
    let node = i
    while (node) {
      arr.push(node.val)
      node = node.next
    }
  })
  // 排序
  arr.sort((a, b) => a - b)
  let dummyNode = new ListNode(-1)
  let current = dummyNode
  // 重新生成链表
  arr.map(num => {
    current.next = new ListNode(num)
    current = current.next
  })
  return dummyNode.next
};
```

#### 归并合并排序链表

输入k个排序链表，将其拆分成`前k/2个链表`和后`k/2个链表`，这前k/2个链表和后k/2个链表`分别合并成两个排序的链表`，再将两个排序的链表合并，所有链表就都合并了。

> - 下面代码中递归调用栈的深度为O(logn)，所以空间复杂度为**O(logn)**
> - 因为使用的是归并排序的思路，所以它的时间复杂度为**O(nlogn)**

```javascript
var mergeKLists = function(lists) {
  if (!lists.length) {
    return null
  }
  /* 合并两个链表为升序链表 */
  var mergeTwoLists = function(list1, list2) {
    // 生成哑节点
    let dummyNode = new ListNode(-1)
    let current = dummyNode
    while (list1 && list2) {
      // 将较小的值的指针拼接在current后
      if (list1.val < list2.val) {
        current.next = list1
        list1 = list1.next
      } else {
        current.next = list2
        list2 = list2.next
      }
      // 指针继续前进
      current = current.next
    }
    // 将剩余的指针全拼在current后
    current.next = list1 || list2
    return dummyNode.next
  };
  const mergeLists = (lists, start = 0, end = lists.length) => {
    // 仅有一个节点时直接返回 lists， 无需拆分
    if (start + 1 === end) {
      return lists[start]
    }
    // 拆分成两个链表，分别进行排序
    const mid = Math.floor((start + end)/2) // 也可写作 const mid = (start + end) >> 1
    const l1 = mergeLists(lists, start, mid)
    const l2 = mergeLists(lists, mid, end)
    return mergeTwoLists(l1, l2)
  }
  // 前闭后开区间
  return mergeLists(lists, 0, lists.length)
};
```



### 19.删除链表的倒数第 N 个结点

[我是链接](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

生成哑结点，让`快指针先走N步`，快指针到达时，慢指针到达倒数N + 1结点处，再删除

```js
var removeNthFromEnd = function(head, n) {
  let dummyNode = new ListNode(-1)
  dummyNode.next = head
  let fast = dummyNode
  let slow = dummyNode
  // 快指针先走 N 步
  while (n --) {
    fast = fast.next
  }
  // 快指针到达终点，慢指针到达倒数N + 1处
  while (fast && fast.next) {
    fast = fast.next
    slow = slow.next
  }
  // 删除
  slow.next = slow.next.next
  return dummyNode.next
};
```

### 876. 链表的中间结点

[我是链接](https://leetcode-cn.com/problems/middle-of-the-linked-list/)

设置快慢指针，让快指针速度是慢指针的2倍，快指针到达时，慢指针刚好在链表中点。

```javascript
var middleNode = function(head) {
  let slow = fast = head
  while (fast && fast.next) {
    fast = fast.next.next
    slow = slow.next
  }
  return slow
};
```

### 环形链表II - 环位置

[我是链接](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

设置快(2)慢(1)指针，若`快慢指针相遇，则为环形`

可以看到，当快慢指针相遇时，让其中任一个指针指向头节点，然后让它俩以`相同速度`前进，再次相遇时所在的节点位置就是`环开始的位置`

```js
var detectCycle = function(head) {
  let slow = head
  let fast = head
  let isLoop = false
  while (fast && fast.next) {
    fast = fast.next.next
    slow = slow.next
    if (slow === fast) {
      // 快慢指针相遇，则为环形
      isLoop = true
      // 跳出循环
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

### 160. 相交链表

[我是链接](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

我们可以让 `p1` 遍历完链表 `A` 之后开始遍历链表 `B`，

让 `p2` 遍历完链表 `B` 之后开始遍历链表 `A`，

这样相当于「逻辑上」两条链表接在了一起。

如果这样进行拼接，就可以让 `p1` 和 `p2` 同时进入公共部分，也就是同时到达相交节点

```js
var getIntersectionNode = function(headA, headB) {
  let p1 = headA
  let p2 = headB
  while (p1 != p2) {
    // 遍历完后遍历另一支链表
    p1 = p1 ? p1.next : headB
    p2 = p2 ? p2.next : headA
  }
  return p1
};
```

## Day2: 递归反转链表

### 206. 反转整个链表

[我是链接](https://leetcode-cn.com/problems/reverse-linked-list/)

#### 递归

将递归函数定义为：输入一个结点head，`将以【head为起点】的链表反转，返回反转后的头节点`.

递归函数需要base case，当`链表仅有一个节点时，反转后也是自己`，直接返回即可

```js
var reverseList = function(head) {
  // 链表仅有一个节点，直接返回
  if (head === null || head.next === null) {
    return head
  }
  // 将head.next为起点的链表反转，返回反转后的头结点
  const last = reverseList(head.next)
  head.next.next = head
  head.next = null
  return last
};
```

### 迭代

![img](https://pic.leetcode-cn.com/1624782858-oyJziv-008eGmZEly1gnrf1oboupg30gy0c44qp.gif)

```js
var reverseList = function(head) {
  let pre = null
  let current = head
  while (current) {
    // 临时变量存储未反转的链表起点
    const temp = current.next
    current.next = pre
    pre = current
    current = temp
  }
  return pre
};
```

### 反转链表的前n个节点

递归函数定义： 反转`以head为起点的前n个节点`链表，返回反转后的头节点

basecase:

```js
/**
* A -> B -> C -> D
* A <- B C -> D
* A -> C
*/
let rest = null
var reverseN = function(head, n) {
  if (n === 1) {
    // 返回当前节点
    // 并记录 n + 1 的节点位置（未反转部分的链表起点）
    rest = head.next
    return head
  }
  // 反转 head.next 为起点的 n - 1 个节点
	const last = reverseN(head.next, n - 1)
  head.next.next = head
  // 反转后的节点和未反转的节点连接起来
  head.next = rest
  return last
};
```



### 92.反转链表的一部分节点

[我是链接](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

basecase：当 left 为 1 时，相当于**反转前N个节点**。

反转head起始的[left，right]区间就相当于反转 head.next 起始的[left - 1, right - 1]区间

```js
var reverseBetween = function(head, left, right) {
  if (left === 1) {
    return revereN(head, right)
  }
  // 以 head.next 为下标 1，那就是 反转head.next为起点的 left - 1, right - 1 区间
  head.next = reverseBetween(head.next, left - 1, right - 1)
  return head
};

// 反转前 N 个链表，相当于 left = 1 开始
let rest = null
const revereN = (head, n) => {
  if (n === 1) {
    rest = head.next
    return head
  }
  const last = revereN(head.next, n - 1)
  head.next.next = head
  head.next = rest
  return last
}
```



### 25. K个一组反转链表

[我是链接](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

记录下每k个一组的起始节点和结束节点，不足 k 个的直接返回head。

将反转`起始节点和结束节点的链表`并拼接到`剩余未反转的链表`上

```js
var reverseKGroup = function(head, k) {
  // 用start 和 end 记录每k个需要反转的起始节点、结束起点
  let start = end = head
  // 不足 k 个即end 到达了 null，直接返回 head
  for (let i = 0; i < k; i ++) {
    if (end === null) {
      return head
    }
    // 得到第 k 个节点 end
    end = end.next
  }
  // 反转 start 到 end 间的链表
  const last = reverse(start, end)
  start.next = reverseKGroup(end, k)
  return last
};

// 前面的反转整个链表：其实就是反转 head 到 null 间的链表
// 迭代
const reverse = (start, end = null) => {
  let pre = null
  let current = start
  while (current !== end) {
    const temp = current.next
    current.next = pre
    pre = current
    current = temp
  }
  return pre
}
```

### 234.回文链表

[我是链接](https://leetcode-cn.com/problems/palindrome-linked-list/)

找出中点，将中点后的节点反转，记录为右指针，比较左指针（起始）和右指针的节点值，不同则返回false

```js
//  反转链表
 const reverse = (head) => {
   if (head === null || head.next == null) {
     return head
   }
   const last = reverse(head.next)
   head.next.next = head
   head.next = null
   return last
 }
var isPalindrome = function(head) {
  // 找出中点
  let fast = slow = head
  while (fast && fast.next) {
    fast = fast.next.next
    slow = slow.next
  }
  // 注意奇数链表，中点多走一步
  if (fast !== null) {
    slow = slow.next
  }
  let left = head
  let right = reverse(slow)
  while (right) {
    if (left.val != right.val) {
      return false
    }
    left = left.next
    right = right.next
  }
  return true
};
```
# 数组

## 双指针秒杀7道数组题

### 26.删除有序数组中的重复项

[我是链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

设置快慢指针，比较快慢指针，**符合条件时慢指针才移动**

```js
var removeDuplicates = function(nums) {
  let slow = 0
  let fast = 1
  while (fast < nums.length) {
    if (nums[fast] != nums[slow]) {
      nums[++slow] = nums[fast]
    }
    fast ++
  }
  // 返回长度 下标加 1
  return slow + 1
};
```

### 83.删除排序链表中的重复元素

[我是链接](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

```js
var deleteDuplicates = function(head) {
  if (head === null) return head
  let slow = head
  let fast = head
  while (fast !== null) {
    if (fast.val != slow.val) {
      slow.next = fast
      slow = slow.next
    }
    fast = fast.next
  }
  slow.next = null
  return head
};
```

### 27.移除元素

[我是链接](https://leetcode-cn.com/problems/remove-element/)

```js
var removeElement = function(nums, val) {
  let slow = 0
  let fast = 0
  while (fast < nums.length) {
    if (nums[fast] !== val) {
      nums[slow ++] = nums[fast] // slow ++
    }
    fast ++
  }
  return slow
};
```

### 283.移动零

[我是链接](https://leetcode-cn.com/problems/move-zeroes/)

```js
var moveZeroes = function(nums) {
  let slow = 0
  let fast = 0
  while (fast < nums.length) {
    if (nums[fast] !== 0) {
      nums[slow ++] = nums[fast]
    }
    fast ++
  }
  while (slow < nums.length) {
    nums[slow ++] = 0
  }
  return nums
};
```

### 167. 两数之和 II - 输入有序数组

[我是链接](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/)

```js
var twoSum = function(numbers, target) {
  let l = 0
  let r = numbers.length - 1
  while (l < r) {
    const sum = numbers[l] + numbers[r]
    if (sum === target) {
      return [l + 1, r + 1]
    }
    if (sum < target) {
      l ++
    } else {
      r --
    }
  }
  return [-1, -1]
};
```

### 344. 反转字符串

[我是链接](https://leetcode-cn.com/problems/reverse-string/)

```js
// 思路： 头尾指针，相遇之前交换值
var reverseString = function(s) {
  for (let i = 0, j = s.length - 1; i < j; i++, j--) {
    [s[i], s[j]] = [s[j], s[i]]
  }
  return s
};
```
### 5. 最长回文子串

[我是链接](https://leetcode-cn.com/problems/longest-palindromic-substring/)

> 找回文串的难点在于，回文串的的长度可能是奇数也可能是偶数，解决该问题的核心是**从中心向两端扩散的双指针技巧**

```js
var longestPalindrome = function(s) {
  // 在 s 中寻找以 s[l] 和 s[r] 为中心的最长回文串
  const palindrome = (s, l, r) => {
    while (l >= 0 && r < s.length && s[l] === s[r]) {
      l --
      r ++
    }
    return s.substring(l + 1, r)
  }
  let res = ''
  for (let i = 0; i < s.length; i ++) {
    // 奇数： 以 s[i] 为中心的最长回文子串
    const s1 = palindrome(s, i, i)
    // 偶数：以 s[i] 和 s[i+1] 为中心的最长回文子串
    const s2 = palindrome(s, i, i + 1)
    // res = longest(res, s1, s2)
    res = res.length > s1.length ? res : s1
    res = res.length > s2.length ? res : s2
  }
  return res
};
```



## 前缀和数组

### 560. 和为 K 的子数组

[我是链接](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

> **前缀和主要适用的场景是原始数组不会被修改的情况下，频繁查询某个区间的累加和**。

构建一个前缀和数组, `preSum[i + 1] = preSum[i] + nums[i]`, nums数组区间[i, j]内的数组和为`preSum[j + 1] - preSum[i]`

例如数组`[1, 1, 1]`的前缀和数组为`[0, 1, 2, 3]`，区间[0, 1]间的数组和为2(2 - 0)

```js
var subarraySum = function(nums, k) {
  const preSum = new Array(nums.length + 1).fill(0)
  // 得到前缀和数组:eg. [1, 1, 1] => [0, 1, 2, 3]
  for (let i = 0; i < nums.length; i ++) {
    preSum[i + 1] = preSum[i] + nums[i]
  }
  let res = 0
  // 计算区间和为k的数目
  for (let i = 0; i < preSum.length; i ++) {
    for (let j = i + 1; j < preSum.length; j ++) {
      if (preSum[j] - preSum[i] === k) {
        res ++
      }
    }
  }
  return res
};
```

以上写法时间复杂度为O(N^2), 嵌套循环可看做求出有多少个 i 满足`preSum[i] = preSum[j] - k`，因此可以用`哈希表`优化

[1, 1, 1] ,k = 2

前缀和数组为[0, 1, **2**（0）, **3**（1）] , 只需找到`preSum[i] - k 的前缀和`出现次数即可

```js
var subarraySum = function(nums, k) {
  let preSum = 0
  // 前缀和 -> 该前缀和出现的次数
  let hash = {
    0: 1 // basecase：和为 0 的默认就为 1
  }
  let res = 0
  for (let i = 0; i < nums.length; i ++) {
    // 累加前缀和
    preSum += nums[i]
    if (hash[preSum - k]) {
      res += hash[preSum - k]
    }
    hash[preSum] = hash[preSum] ? hash[preSum] + 1: 1
  }
  return　res
};
```

## 差分数组

> **差分数组的主要适用场景是频繁对原始数组的某个区间的元素进行增减**
>
> eg. 差分数组：[8, 2, 6, 3, 1] => [8, - 6, 4, -3, -2]

实现一个差分类

```js
class Difference {
  constructor (nums) {
    // 构建差分数组
    this.diff = [nums[0]]
    for (let i = 1; i < nums.length; i ++) {
      this.diff[i] = nums[i] - nums[i - 1]
    }
  }
  // 区间[i, j]内增加一个数值 val 可为负数
  increment (i, j, val) {
    // [i...] 区间内所有元素都增加val
    this.diff[i] += val
    if (j + 1 < this.diff.length) {
      // [j...]区间内所有元素都减少val => [i, j]区间内增加val
      this.diff[j + 1] -= val
    }
  }
  // 根据差分数组构造结果数组
  getRes () {
    let res = [this.diff[0]]
    for (let i = 1; i < this.diff.length; i ++) {
      res[i] = res[i - 1] + this.diff[i]
    }
    return res
  }
}

let diffArr = new Difference([3, 2, 1])
diffArr.increment(0, 1, 2)
diffArr.getRes()
```

### 1109. 航班预订统计

[我是链接](https://leetcode-cn.com/problems/corporate-flight-bookings/)

相当于[1, 2]区间增加10航班, [2, 3]区间增加20航班, [2, 5]区间增加25航班

```js
var corpFlightBookings = function(bookings, n) {
  let nums = new Array(n).fill(0)
  // 实现一个差分类
  let diff = new Difference(nums)
  bookings.map(([i, j, val]) => {
    // 航班从 1 算起，下标从 0 算起
    diff.increment(i - 1 ,j - 1, val)
  })
  return diff.getRes()
};
```

### 1094. 拼车

[我是链接](https://leetcode-cn.com/problems/car-pooling/)

算出所有区间内，车内所有乘客数，再比较是否超载即可

```js
var carPooling = function(trips, capacity) {
  // 最多1000个车站
  let nums = new Array(1001).fill(0)
  let diff = new Difference(nums)
  trips.map(([val, i, j]) => {
    // 乘客在[i, j)在车内
    diff.increment(i, j - 1, val)
  })
  // 得到所有区间内车内乘客数
  const res = diff.getRes()
  // 任意区间内都不该超载
  return res.every(i => i <= capacity)
};
```

## 旋转二维数组

### 48.旋转图像

[我是链接](https://leetcode-cn.com/problems/rotate-image/)

顺时针反转90度：先沿对角线对称反转，再每行反转

![](https://pic.leetcode-cn.com/1643922516-yVtvEl-image.png)

逆时针反转90度：选择另一条对角线对称反转，再每行反转

![img](https://labuladong.gitee.io/algo/images/%e8%8a%b1%e5%bc%8f%e9%81%8d%e5%8e%86/5.jpeg)

```js
var rotate = function(matrix) {
  // 1. 沿对角线对称二维矩阵
  const n = matrix.length
  for (let i = 0 ;i < n; i ++) {
    for (let j = i; j < n; j ++) {
      [matrix[i][j], matrix[j][i]] = [matrix[j][i], matrix[i][j]]
    }
  }
  /* 反转数组 */
  const reverseArr = (arr) => {
    let l = 0
    let r = arr.length - 1
    while (l < r) {
      [arr[l], arr[r]] = [arr[r], arr[l]]
      l ++ 
      r --
    }
  }
  // 2. 每行反转
  for (let i = 0; i < n; i ++) {
    reverseArr(matrix[i])
  }
};

```

### 54.螺旋矩阵

[我是链接](https://leetcode-cn.com/problems/spiral-matrix/)

核心用右、下、左、上顺序遍历，并用 4 个变量圈定未遍历元素的边界

```js
 var spiralOrder = function(matrix) {
  let res = []
  const [m, n]= [matrix.length, matrix[0]?.length]
  // 定义边界变量
  let [upper, lower, left, right] = [0, m - 1, 0, n - 1]
  while (res.length < m * n) {
    if (upper <= lower) {
      // 顶部：从左到右
      for (let i = left; i <= right; i ++) {
        res.push(matrix[upper][i])
      }
      // 顶部边界下移
      upper ++
    }
    if (left <= right) {
      // 右侧： 从上到下
      for (let i = upper; i <= lower; i ++) {
        res.push(matrix[i][right])
      }
      // 右侧边界左移
      right --
    }
    if (upper <= lower) {
      // 底部：从右到左
      for (let i = right; i >= left; i --) {
        res.push(matrix[lower][i])
      }
      // 底部边界上移
      lower --
    }
    if (left <= right) {
      // 左侧： 从下到上
      for (let i = lower; i >= upper; i --) {
        res.push(matrix[i][left])
      }
      // 左边界右移
      left ++
    }
  }
  return res
};
```

### 59.螺旋矩阵II

[我是链接](https://leetcode-cn.com/problems/spiral-matrix-ii/)

```js
var generateMatrix = function(n) {
  let matrix = new Array(n);
  for (let i = 0; i < n; i ++) {
    matrix[i] = Array(n).fill(0)
  }
  let num = 1
  let [upper, lower, left, right] = [0, n - 1, 0, n - 1]
  while (num <= n * n) {
    if (upper <= lower) {
      // 顶部：从左到右
      for (let i = left; i <= right; i ++) {
        matrix[upper][i] = num ++
      }
      // 顶部边界下移
      upper ++
    }
    if (left <= right) {
      // 右侧： 从上到下
      for (let i = upper; i <= lower; i ++) {
        matrix[i][right] = num ++
      }
      // 右侧边界左移
      right --
    }
    if (upper <= lower) {
      // 底部：从右到左
      for (let i = right; i >= left; i --) {
        matrix[lower][i] = num ++
      }
      // 底部边界上移
      lower --
    }
    if (left <= right) {
      // 左侧： 从下到上
      for (let i = lower; i >= upper; i --) {
        matrix[i][left] = num ++
      }
      // 左边界右移
      left ++
    }
  }
  return matrix
};
```

## 滑动窗口

### 76. 最小覆盖子串

[我是链接](https://leetcode-cn.com/problems/minimum-window-substring/)

取`[left, right)`左闭右开区间作为窗口，

先不断增大right指针直到窗口满足t（target）条件

此时停止增加right指针，转而增加left指针，更新窗口，以缩减窗口。

重复以上步骤，知道right到达s字符串的尽头(s.length)

```js
var minWindow = function(s, t) {
  // need记录 t 中字符出现次数，slideWindow记录窗口中字符出现次数
  let need = {}
  let slideWindow = {}
  let left = 0
  let right = 0
  // valid: 窗口中满足 need 条件的字符个数 
  // 当valid === Object.keys(need).length 则说明窗口已满足条件，已经完全覆盖了串 T。
  let valid = 0 
  // 结果记录, 用于截取字符串
  let startIndex = 0
  let endIndex = Infinity
  // 统计t字符串的字母分布
  for (let i = 0; i < t.length; i ++) {
    need[t[i]] = need[t[i]] ? need[t[i]] + 1 : 1
  }
  // 区间左闭右开[left, right)
  while (right < s.length) {
    let wr = s[right ++]
    slideWindow[wr] = slideWindow[wr] ? slideWindow[wr] + 1 : 1
    if (slideWindow[wr] === need[wr]) {
      valid ++
    }
    // 缩减窗口条件
    while (valid === Object.keys(need).length) {
      // 结果处理
      if (right - left < endIndex - startIndex) {
        startIndex = left
        endIndex = right
      }
      // 左指针移动，更新窗口
      let wl = s[left ++]
      if (need[wl]) {
        if (slideWindow[wl] === need[wl]) {
          valid --
        }
        slideWindow[wl] --
      }
    }
  }
  return endIndex === Infinity ? '' : s.slice(startIndex, endIndex)
};
```

### 567. 字符串的排列

[我是链接](https://leetcode-cn.com/problems/permutation-in-string/)

```js
var checkInclusion = function(s1, s2) {
  // need记录 s1 中字符出现次数，slideWindow记录[窗口]中字符出现次数
  let need = {}
  let sildeWindow = {}
  let left = 0
  let right = 0
  // valid: 窗口中满足 need 条件的字符个数 
  // 当valid === Object.keys(need).length 则说明窗口已满足条件，已经完全覆盖了串 T。
  let valid = 0
  for (let i = 0; i < s1.length; i ++) {
    need[s1[i]] = need[s1[i]] ? need[s1[i]] + 1 : 1
  }
  let res = false
  // 区间左闭右开[left, right)
  while (right < s2.length) {
    let wr = s2[right ++]
    sildeWindow[wr] = sildeWindow[wr] ? sildeWindow[wr] + 1 : 1
    if (sildeWindow[wr] === need[wr]) {
      valid ++
    }
    // 缩减窗口条件
    while (right - left >= s1.length) {
      // 结果处理
      if (valid === Object.keys(need).length) {
        res = true
        break
      } else {
        // 左指针移动，更新窗口
        const wl = s2[left ++]
        if (need[wl]) {
          if (sildeWindow[wl] === need[wl]) {
            valid -- 
          }
          sildeWindow[wl] --
        }
      }
    }
  }
  return res
};
```

### 438. 找到字符串中所有字母异位词

[我是链接](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

```js
var findAnagrams = function(s, p) {
  // need记录 p 中字符出现次数，slideWindow 记录[窗口]中字符出现次数
  let need = {}
  let slideWindow = {}
  let left = 0
  let right = 0
  let valid = 0
  let res = []
  for (let i = 0; i < p.length; i ++) {
    need[p[i]] = need[p[i]] ? need[p[i]] + 1 : 1
  }
  while (right < s.length) {
    const r = s[right ++]
    slideWindow[r] = slideWindow[r] ? slideWindow[r] + 1 : 1
    if (slideWindow[r] === need[r]) {
      valid ++
    }
    // 判断左侧窗口是否要收缩
    while (right - left >= p.length) {
      // 结果处理
      if (valid === Object.keys(need).length) {
        res.push(left)
      }
      // 缩减窗口
      const l  = s[left ++]
      if (need[l]) {
        if (slideWindow[l] === need[l]) {
          valid --
        }
        slideWindow[l] --
      }
    }
  }
  return res
};
```

### 3. 无重复字符的最长子串

[我是链接](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

```js
var lengthOfLongestSubstring = function(s) {
  let slideWindow = {}
  let left = 0
  let right = 0
  let res = 0
  while (right < s.length) {
    const r = s[right ++]
    slideWindow[r] = slideWindow[r] ? slideWindow[r] + 1 : 1
    // 缩减条件：存在重复字符
    while (slideWindow[r] > 1) {
      const l = s[left ++]
      slideWindow[l] --
    }
    res = Math.max(res, right - left)
  }
  return res
};
```

## 二分查找

> 分析二分查找代码时，不要出现 else，全部展开成 else if 方便理解

```js
/ ** 常规二分搜索 **/
const binaryBound = (nums, target) => {
  // 记录数组的起始和结尾下标
  let left = 0
  let right = nums.length - 1
  // 搜索区间为[left, right]
  while (left <= right) {
    const mid = Math.floor((left + right) / 2)
    if (nums[mid] === target) {
      // 直接返回结果
      return mid
    } else if (nums[mid] > target) {
      right = mid - 1
    } else if (nums[mid] < target) {
      left = mid + 1
    }
  }
  return -1
}
/ ** 左侧边界二分搜索 **/
const leftBound = (nums, target) => {
  // 记录数组的起始和结尾下标
  let left = 0
  let right = nums.length - 1
  // 搜索区间为[left, right]
  while (left <= right) {
    const mid = Math.floor((left + right) / 2)
    if (nums[mid] === target) {
      // 收缩右侧边界
      right = mid - 1
    } else if (nums[mid] > target) {
      right = mid - 1
    } else if (nums[mid] < target) {
      left = mid + 1
    }
  }
  // 处理target不在nums内的情况
  if (left > nums.length || nums[left] !== target) {
    return -1
  }
  return left
}

/ ** 右侧边界二分搜索 **/
const rightBound = (nums, target) => {
  // 记录数组的起始和结尾下标
  let left = 0
  let right = nums.length - 1
  // 搜索区间为[left, right]
  while (left <= right) {
    const mid = Math.floor((left + right) / 2)
    if (nums[mid] === target) {
      // 收缩左侧边界
      left = mid + 1
    } else if (nums[mid] > target) {
      right = mid - 1
    } else if (nums[mid] < target) {
      left = mid + 1
    }
  }
  // 处理target不在nums内的情况
  if (right < 0 || nums[right] !== target) {
    return -1
  }
  return right
}
console.log(rightBound([1,2,2,2,2,3], 2)) // 4
```

### 704.二分查找

[我是链接](https://leetcode-cn.com/problems/binary-search/)

```js
var search = function(nums, target) {
  return binaryBound(nums, target)
};
```



### 34. 在排序数组中查找元素的第一个和最后一个位置

[我是链接](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

```js
var searchRange = function(nums, target) {
  const left = leftBound(nums, target) // 获取左侧边界下标
  const right = rightBound(nums, target) // 获取右侧边界下标
  return [left, right]
};
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

### 1011.在 D 天内送达包裹的能力

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

## twoSum 问题
### 1.两数之和

[我是链接](https://leetcode-cn.com/problems/two-sum/)

- 有序： `前后指针`

- 无序： `hash表`

哈希表存**已遍历的值和下标**，若在哈希表已存在说明当前值和已遍历的某一值加起来刚好为target值

```js
var twoSum = function(nums, target) {
  let hash = {}
  for (let i = 0; i < nums.length; i ++) {
    const x = target - nums[i]
    if (hash[x] || hash[x] === 0) {
      return [i, hash[x]]
    } else {
      hash[nums[i]] = i
    }
  }
};
```

# 数据机构

## LRU缓存

### 146.LRU缓存机制

[我是链接](https://leetcode-cn.com/problems/lru-cache/)

LRU 的全称是` Least Recently Used`，**最近最少使用**缓存淘汰策略。

```js
// 实现双向链表节点类
class ListNode {
  constructor (key, value) {
    this.key = key
    this.value = value
    this.prev = null
    this.next = null
  }
}
class LRUCache {
  constructor (capacity) {
    this.capacity = capacity // 容量
    this.count = 0 // 已存储个数
    this.hash = {} // 记录 key => 节点 的映射
    this.dummyHead = new ListNode()
    this.dummyTail = new ListNode()
    this.dummyHead.next = this.dummyTail
    this.dummyTail.prev = this.dummyHead
  }
  // key 存在，将节点移到链表头部（删除当前节点 + 将节点添加到头部），否则，返回 -1
  get (key) {
    const node = this.hash[key]
    if (node) {
      this.removeFromList(node)
      this.addToHead(node)
      return node.value
    }
    return -1
  }
  // key存在，将value值替换，将节点移动到最前面。
  // key不存在：1.容量已满，删除尾节点，节点数减一，将新节点加入头部； 2.容量未满，将新节点加入头部，节点数加一
  put (key, value) {
    let node = this.hash[key]
    if (node) {
      node.value = value
      this.removeFromList(node)
      this.addToHead(node)
    } else {
      node = new ListNode(key, value)
      if (this.count === this.capacity) {
        const tail = this.dummyTail.prev
        delete this.hash[tail.key]
        this.count --
        this.removeFromList(tail)
      }
      this.addToHead(node)
      this.hash[key] = node
      this.count ++
    }
  }
  removeFromList (node) {
    node.prev.next = node.next
    node.next.prev = node.prev
  }
  // 添加节点至头部
  addToHead (node) {
    node.prev = this.dummyHead
    node.next = this.dummyHead.next
    this.dummyHead.next.prev = node
    this.dummyHead.next = node

  }
}
```

### 460. LFU 缓存

[我是链接](https://leetcode-cn.com/problems/lfu-cache/)


维护 `KV` 表，`KF` 表，`FK` 表三个映射。

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

## 单调栈

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
  let res = []
  let deque = []
  for (let i = temperatures.length - 1; i >= 0; i --) {
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

#### 处理环形数组 🌟🌟

对索引进行求模，模拟长度加长的情况

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

## 单调队列

### 239.滑动窗口最大的值 

[我是链接](https://leetcode-cn.com/problems/sliding-window-maximum/)

```js
var maxSlidingWindow = function(nums, k) {
  const n = nums.length
  let dequeue = []
  let res = []
  for (let i = 0; i < n; i ++) {
    // 合法性检测,超出 k个 窗口删除
    if (i - dequeue[0] >= k) {
      dequeue.shift()
    }
    while (dequeue.length && nums[dequeue[dequeue.length - 1]] <= nums[i]) {
      dequeue.pop()
    }
    dequeue.push(i)
    // k 为 3， 区间[0-2]开始算最大值
    if (i >= k -1) {
      res.push(nums[dequeue[0]])
    }
  }
  return res
};
```

## 最小栈

### 155. 最小栈

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

