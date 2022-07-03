# 链表

## Day1: 单链表的六大解题思路

### [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

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

### [23.合并k个链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

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



### [19.删除链表的倒数第 N 个结点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)

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

### [876. 链表的中间结点](https://leetcode-cn.com/problems/middle-of-the-linked-list/)

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

### [142.环形链表II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

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

### [160. 相交链表](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)

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

### [206. 反转整个链表](https://leetcode-cn.com/problems/reverse-linked-list/)

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



### [92.反转链表的一部分节点](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

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

### [25. K个一组反转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

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

### [234.回文链表](https://leetcode-cn.com/problems/palindrome-linked-list/)

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

### [26.删除有序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

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

### [83.删除排序链表中的重复元素](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

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

### [27.移除元素](https://leetcode-cn.com/problems/remove-element/)

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

### [283.移动零](https://leetcode-cn.com/problems/move-zeroes/)

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

### [167. 两数之和 II - 输入有序数组](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/)

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

### [344. 反转字符串](https://leetcode-cn.com/problems/reverse-string/)

```js
// 思路： 头尾指针，相遇之前交换值
var reverseString = function(s) {
  for (let i = 0, j = s.length - 1; i < j; i++, j--) {
    [s[i], s[j]] = [s[j], s[i]]
  }
  return s
};
```
### [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

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

### [560. 和为 K 的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

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

### [1109. 航班预订统计](https://leetcode-cn.com/problems/corporate-flight-bookings/)

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

### [1094. 拼车](https://leetcode-cn.com/problems/car-pooling/)

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

### [48.旋转图像](https://leetcode-cn.com/problems/rotate-image/)

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

### [54.螺旋矩阵](https://leetcode-cn.com/problems/spiral-matrix/)

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

### [59.螺旋矩阵II](https://leetcode-cn.com/problems/spiral-matrix-ii/)

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

### [76. 最小覆盖子串](https://leetcode-cn.com/problems/minimum-window-substring/)

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

### [567. 字符串的排列](https://leetcode-cn.com/problems/permutation-in-string/)

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

### [438. 找到字符串中所有字母异位词](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

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

### [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

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

### [704.二分查找](https://leetcode-cn.com/problems/binary-search/)

```js
var search = function(nums, target) {
  return binaryBound(nums, target)
};
```

### [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

```js
var searchRange = function(nums, target) {
  const left = leftBound(nums, target) // 获取左侧边界下标
  const right = rightBound(nums, target) // 获取右侧边界下标
  return [left, right]
};
```

### [69. x的平方根](https://leetcode-cn.com/problems/sqrtx/)

> 题示是求平方根，实际是[0, x]区间内其平方最接近x的数。相当于**求二分法的右边界**

```js
var mySqrt = function (x) {
  let [left, right] = [0, x]
  while (left <= right) {
    const mid = Math.floor((l + r) / 2) // 中间位置索引 x>>1 表示除以2并取整，缩小一下遍历的范围
    if (mid * mid <= x) {
      left = mid + 1
    } else {
      right = mid - 1
    }
  }
  return right
};
```

### [50. Pow(x, n)](https://leetcode-cn.com/problems/powx-n/) 

>  x<sup>n</sup> = {
>
> ​	(x<sup>2</sup>)<sup>(n/2)</sup> // n为偶数
>  						
> ​		x * (x<sup>2</sup>)<sup>(n/2)</sup>// n为奇数
>
>  }

```js
var myPow = function(x, n) {
  if (n === 0) return 1
  // n 为负数
  if  (n < 0) {
    return 1 / myPow(x, -n)
  }
  // n 为奇数
  if (n % 2) {
    return x * myPow(x, n - 1)
  }
  // n 为偶数
  return myPow(x * x, n / 2)
};
```

### [875.爱吃香蕉的珂珂](https://leetcode-cn.com/problems/koko-eating-bananas/)

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

### [1011.在 D 天内送达包裹的能力](https://leetcode-cn.com/problems/capacity-to-ship-packages-within-d-days)

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
### [1.两数之和](https://leetcode-cn.com/problems/two-sum/)

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

# 排序

> [912. 排序数组https://leetcode.cn/problems/sort-an-array/](https://leetcode-cn.com/problems/sort-an-array/) 

冒泡、选择、插入、快排、归并

https://leetcode-cn.com/problems/sort-an-array/solution/5chong-chang-yong-pai-xu-suan-fa-by-jsyt/

9种排序

## 时间复杂度O(n²)的排序算法

### 冒泡排序,空间O(1)

```js
var sortArray = function(nums) {
  // 冒泡排序
  // 一边比较一边向后两两交换，将最大值冒泡到最后一位
  for (let i = 0; i < nums.length; i++) {
    for (let j = i + 1; j < nums.length; j++){
      if (nums[i] > nums[j]) {
        let temp = nums[i]
        nums[i] = nums[j];
        nums[j] = temp
      }
    }
  }
  return nums
};
```

### 选择排序,空间O(1)

```js
var sortArray = function(nums) {
  // 选择排序
  // 找出未排序部分的最小值，交换到未排序部分的第一个
  for (let i = 0; i < nums.length; i++) {
    let minIndex = i
    // [i+1, ...] 未排序部分
    for (let j = i + 1; j < nums.length; j++){
      // 找到最小值
      if (nums[minIndex] > nums[j]) {
        minIndex = j
      }
    }
    // 交换
    if (i !== minIndex) {
      [nums[i], nums[minIndex]] = [nums[minIndex], nums[i]]
    }
  }
  return nums
};
```

### 插入排序,空间O(1)

```js
var sortArray = function (nums) {
  // 插入排序
  // 假设位置i之前是已经排序好的数组
  // 在已排序好的数组中将大于当前位置i的全部后移，将当前元素插入
  for (let i = 1; i < nums.length; i++) {
    let preIndex = i - 1;
    const current = nums[i];
    while (preIndex >= 0 && nums[preIndex] > current) {
      nums[preIndex + 1] = nums[preIndex];
      preIndex = preIndex - 1;
    }
    nums[preIndex + 1] = current;
  }
  return nums;
};
```



## 时间复杂度O(n²)到时间复杂度O(nlogn)间的排序算法

### 希尔排序

> h=3*h+1 时，希尔排序平均时间复杂度最优
>
> 需要注意的是，js除法需要调用Math.floor获取商

```js
var sortArray = function (nums) {
  // 希尔排序
  // 插入排序是将数组分割成1个已经排序的数组，希尔排序则是切割成h间隔的数组
  let h = 1;
  while (h < Math.floor(nums.length / 3)) {
    h = 3 * h + 1;
  }
  while (h > 0) {
    for (let i = h; i < nums.length; i++) {
      let preIndex = i - h;
      const current = nums[i];
      while (preIndex >= 0 && nums[preIndex] > current) {
        nums[preIndex + h] = nums[preIndex];
        preIndex = preIndex - h;
      }
      nums[preIndex + h] = current;
    }
    h = Math.floor(h / 3);
  }
  return nums;
};
```

## 时间复杂度O(nlogn)的排序算法

### 快速排序

```js
var sortArray = function(nums) {
    if (nums.length <= 1) { return nums; }
    var pivotIndex = Math.floor(nums.length / 2);
    var pivot = nums.splice(pivotIndex, 1)[0]; // 基准元素
    var left = [];
    var right = [];
    for (var i = 0; i < nums.length; i++){
        if (nums[i] < pivot) {
            left.push(nums[i]);
        } else {
            right.push(nums[i]);
        }
    }
    return sortArray(left).concat([pivot], sortArray(right));
};
```

### 归并排序

```js
var sortArray = function(nums) {
    return mergeSort(nums, 0, nums.length - 1)
};

function mergeSort(nums, left, right) {
    if (left >= right) return nums;
    let mid = (left + right) >> 1;
    mergeSort(nums, left, mid)
    mergeSort(nums, mid + 1, right)
    return merge(nums, left, mid, right)
}

function merge(nums, left, mid, right) {
    let ans = [];
    let c = 0, i = left, j = mid + 1;
    while (i <= mid && j <= right) {
        if (nums[i] < nums[j]) {
            ans[c++] = nums[i++];
        } else {
            ans[c++] = nums[j++]
        }
    }
    while (i <= mid) {
        ans[c++] = nums[i++];
    }
    while (j <= right) {
        ans[c++] = nums[j++];
    }
    for (let i = 0; i < ans.length; i++) {
        nums[i + left] = ans[i];
    }
    return nums;
}
```



# 数据结构

## LRU缓存

### [146.LRU缓存机制](https://leetcode-cn.com/problems/lru-cache/)

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

### [460. LFU 缓存](https://leetcode-cn.com/problems/lfu-cache/)


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

### [739.每日温度](https://leetcode-cn.com/problems/daily-temperatures/)

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

### [496.下一个更大的元素I](https://leetcode-cn.com/problems/next-greater-element-i/) 

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

### [503.下一个更大的元素II](https://leetcode-cn.com/problems/next-greater-element-ii/)  

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

### [239.滑动窗口最大的值](https://leetcode-cn.com/problems/sliding-window-maximum/) 

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

### [155. 最小栈](https://leetcode-cn.com/problems/min-stack/)

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

# 回溯法（DFS）

## 子集、组合、排列

**解决一个回溯问题，实际上就是一个决策树的遍历过程**

### 元素无重不可复选

>  元素无重不可复选，即 nums 中的元素都是唯一的，每个元素最多只能被使用一次

```js
/* 组合/子集问题回溯算法框架 */
void backtrack(int[] nums, int start) {
    // 回溯算法标准框架
    for (int i = start; i < nums.length; i++) {
        // 做选择
        track.addLast(nums[i]);
        // 注意参数
        backtrack(nums, i + 1);
        // 撤销选择
        track.removeLast();
    }
}

/* 排列问题回溯算法框架 */
let res = []
let used = [] // 记录是否已选择
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        // 剪枝逻辑
        if (used[i]) {
            continue;
        }
        // 做选择
        used[i] = true;
        track.addLast(nums[i]);

        backtrack(nums);
        // 撤销选择
        track.removeLast();
        used[i] = false;
    }
}
```

#### [78. 子集](https://leetcode-cn.com/problems/subsets/)

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
}
```

#### [77.组合](https://leetcode-cn.com/problems/combinations/)

```js
var combine = function(n, k) {
  let res = []
  const backTrack = (n, track = [], startIndex = 1) => {
    if (track.length === k) {
      res.push(track)
    }
    for (let i = startIndex; i <= n; i ++) {
      track.push(i)
      backTrack(n, [...track], i + 1)
      track.pop()
    }
  }
  backTrack(n, [], 1)
  return res
}
```

#### [46.全排列](https://leetcode-cn.com/problems/permutations/)

```js
var permute = function(nums) {
  let res = []
  let used = []
  const backTrack = (nums, track = []) => {
    if (track.length === nums.length) {
      res.push(track)
    }
    for (let i = 0; i < nums.length; i ++) {
      if (used[i]) {
        continue
      }
      if (i > 0 && nums[i - 1] === nums[i] && used[i - 1]) {
        continue
      }
      used[i] = true
      track.push(nums[i])
      backTrack(nums, [...track])
      track.pop()
      used[i] = false
    }
  }
  backTrack(nums, [])
  return res
};
```

### 元素可重不可复选

> **元素可重不可复选，即 `nums` 中的元素可以存在重复，每个元素最多只能被使用一次**，其关键在于排序和剪枝

```js
/* 组合/子集问题回溯算法框架 */
Arrays.sort(nums); // 排序
void backtrack(int[] nums, int start) {
    // 回溯算法标准框架
    for (int i = start; i < nums.length; i++) {
        // 剪枝逻辑，跳过值相同的相邻树枝
        if (i > start && nums[i] == nums[i - 1]) {
            continue;
        }
        // 做选择
        track.addLast(nums[i]);
        // 注意参数
        backtrack(nums, i + 1);
        // 撤销选择
        track.removeLast();
    }
}



/* 排列问题回溯算法框架 */
let used = []
Arrays.sort(nums); // 排序
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        // 剪枝逻辑
        if (used[i]) {
            continue;
        }
        // 剪枝逻辑，固定相同的元素在排列中的相对位置
        if (i > 0 && nums[i] == nums[i - 1] && !used[i - 1]) {
            continue;
        }
        // 做选择
        used[i] = true;
        track.addLast(nums[i]);

        backtrack(nums);
        // 撤销选择
        track.removeLast();
        used[i] = false;
    }
}
```

#### [47.全排列II](https://leetcode-cn.com/problems/permutations-ii/)

```js
var permuteUnique = function(nums) {
  let res = []
  let used = []
  // 对 nums 进行了排序
  nums.sort((a, b) => a - b)
  const backTrack = (nums, track = []) => {
    if (track.length === nums.length) {
      res.push(track)
    }
    for (let i = 0; i < nums.length; i ++) {
      if (used[i]) {
        continue
      }
      // 剪枝
      if (i > 0 && nums[i - 1] === nums[i] && used[i - 1]) {
        continue
      }
      used[i] = true
      track.push(nums[i])
      backTrack(nums, [...track])
      track.pop()
      used[i] = false
    }
  }
  backTrack(nums, [])
  return res
};
```

#### [90. 子集 II](https://leetcode-cn.com/problems/subsets-ii/)

需要先进行排序，让相同的元素靠在一起，如果发现 `nums[i] == nums[i-1]`，则跳过

```js
var subsetsWithDup = function(nums) {
  // 先排序，让相同的元素靠在一起
  nums = nums.sort((a, b) => a - b)
  let res = []
  const backTrack = (nums, track = [], startIndex = 0) => {
    res.push(track)
    for (let i = startIndex; i < nums.length; i ++) {
      // 剪枝逻辑，值相同的相邻树枝，只遍历第一条
      if (i > startIndex && nums[i] === nums[i - 1]) {
        continue
      }
      track.push(nums[i])
      backTrack(nums, [...track], i + 1)
      track.pop()
    }
  }
  backTrack(nums, [], 0)
  return res
};
```

#### [组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/)

```js
var combinationSum2 = function(candidates, target) {
  candidates = candidates.sort((a, b) => a - b)
  let res = []
  let trackSum = 0
  const backTrack = (candidates, track = [], startIndex = 0) => {
    // base case，超过目标和，直接结束
    if (trackSum > target) {
        return;
    }
    // 存入结果
    if (trackSum === target) {
      res.push(track)
    }
    for (let i = startIndex; i < candidates.length; i ++) {
      if (i > startIndex && candidates[i] === candidates[i - 1]) {
        continue
      }
      track.push(candidates[i])
      trackSum += candidates[i]
      backTrack(candidates, [...track], i + 1)
      track.pop()
      trackSum -= candidates[i]
    }
  }
  backTrack(candidates, [], 0)
  return res
};
```

### 元素无重可复选

> **形式三、元素无重可复选，即 `nums` 中的元素都是唯一的，每个元素可以被使用若干次**，只要删掉去重逻辑即可

```js
/* 组合/子集问题回溯算法框架 */
void backtrack(int[] nums, int start) {
    // 回溯算法标准框架
    for (int i = start; i < nums.length; i++) {
        // 做选择
        track.addLast(nums[i]);
        // 注意参数
        backtrack(nums, i);
        // 撤销选择
        track.removeLast();
    }
}


/* 排列问题回溯算法框架 */
void backtrack(int[] nums) {
    for (int i = 0; i < nums.length; i++) {
        // 做选择
        track.addLast(nums[i]);
        backtrack(nums);
        // 撤销选择
        track.removeLast();
    }
}
```

#### [39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

```js
var combinationSum = function(candidates, target) {
  let res = []
  let trackSum = 0
  const backTrack = (candidates, track = [], startIndex = 0) => {
    if (trackSum === target) {
      res.push(track)
    }
    if (trackSum > target) {
      return
    }
    for (let i = startIndex; i < candidates.length; i ++) {
      if (i > 0 && candidates[i - 1] === candidates[i]) {
        continue
      }
      track.push(candidates[i])
      trackSum += candidates[i]
      backTrack(candidates, [...track], i)
      track.pop()
      trackSum -= candidates[i]
    }
  }
  backTrack(candidates, [], 0)
  return res
};
```

## 回溯法最佳实践

### [51.N 皇后](https://leetcode-cn.com/problems/n-queens/)

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

### [37. 解数独](https://leetcode-cn.com/problems/sudoku-solver/)

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

### [22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

```js
var generateParenthesis = function(n) {
  let res = []
  const backTrack = (left = n, right = n, track = []) => {
    // baseCase1: 合法的括号一定先用左括号
    if (right < left) {
      return 
    }
    // baseCase2: 合法的括号左右括号刚好一起用完
    if (left < 0 || right < 0) {
      return
    }
    if (left === 0 && right === 0) {
      res.push(track.join(''))
    }
    for (let item of ['(', ')']) {
      track.push(item)
      item === '(' && backTrack(left - 1, right, [...track])
      item === ')' && backTrack(left, right - 1, [...track])
      track.pop()
    }
  }
  backTrack(n, n, [])
  return res
};
```

# 树

## 二叉树的纲领

```js
二叉树解题的思维模式分两类：

1、是否可以通过遍历一遍二叉树得到答案？如果可以，用一个 traverse 函数配合外部变量来实现，这叫「遍历」的思维模式。

2、是否可以定义一个递归函数，通过子问题（子树）的答案推导出原问题的答案？如果可以，写出这个递归函数的定义，并充分利用这个函数的返回值，这叫「分解问题」的思维模式。

无论使用哪种思维模式，你都需要思考：

如果单独抽出一个二叉树节点，它需要做什么事情？需要在什么时候（前/中/后序位置）做？其他的节点不用你操心，递归函数会帮你在所有节点上执行相同的操作。
```

举具体的例子，现在给你一棵二叉树，我问你两个简单的问题：

1、如果把根节点看做第 1 层，如何打印出每一个节点所在的层数？

2、如何打印出每个节点的左右子树各有多少节点？

```js
// 问题1二叉树遍历函数
void traverse(TreeNode root, int level) {
    if (root == null) {
        return;
    }
    // 前序位置
    printf("节点 %s 在第 %d 层", root, level);
    traverse(root.left, level + 1);
    traverse(root.right, level + 1);
}

// 这样调用
traverse(root, 1);
```

```js
// 定义：输入一棵二叉树，返回这棵二叉树的节点总数
int count(TreeNode root) {
    if (root == null) {
        return 0;
    }
    int leftCount = count(root.left);
    int rightCount = count(root.right);
    // 后序位置
    printf("节点 %s 的左子树有 %d 个节点，右子树有 %d 个节点",
            root, leftCount, rightCount);

    return leftCount + rightCount + 1;
}
```

> 只有后序位置才能通过返回值获取子树的信息

### [104. 二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

```js
var maxDepth = function(root) {
  // 当前节点的最大深度 🟰 其左右子树的最大深度 + 1（当前节点）
  if (root === null) {
    return 0
  }
  const left = maxDepth(root.left)
  const right = maxDepth(root.right)
  return Math.max(left, right) + 1
};
```

### [543. 二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

**每一条二叉树的「直径」长度，就是一个节点的左右子树的最大深度之和**

```js
var diameterOfBinaryTree = function(root) {
  let res = 0
  const maxDepth = root => {
    if (root === null) {
      return 0
    }
    const leftMax = maxDepth(root.left)
    const rightMax = maxDepth(root.right)
    res = Math.max(res, leftMax + rightMax)
    return Math.max(leftMax, rightMax) + 1
  }
  maxDepth(root)
  return res
};
```

## 二叉树的思路

### [226. 翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

写一个 `traverse` 函数遍历每个节点，让每个节点的左右子节点颠倒过来

```js
var invertTree = function(root) {
  const reverse = root => {
    if (root === null) {
      return null
    }
    const temp= root.left
    root.left = root.right
    root.right = temp
    reverse(root.left)
    reverse(root.right)
  }
  reverse(root)
  return root
};
```

「分解问题」的思路，核心在于你要给递归函数一个合适的定义，然后用函数的定义来解释你的代码；如果你的逻辑成功自恰，那么说明你这个算法是正确的。

```js
// 定义：将以 root 为根的这棵二叉树翻转，返回翻转后的二叉树的根节点
var invertTree = function(root) {
  if (root === null) {
    return null
  }
  const left = invertTree(root.left)
  const right = invertTree(root.right)
  root.left = right
  root.right = left
  return root
};
```

### [116. 填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

```js
var connect = function(root) {
  if (root === null) return root
  const connectTwoNodes = (nodeL, nodeR) => {
    if (nodeL === null || nodeR === null) {
      return null
    }
    /**** 前序位置 ****/
    // 将传入的两个节点穿起来
    nodeL.next = nodeR
   	// 连接相同父节点的两个子节点
    connectTwoNodes(nodeL.left, nodeL.right)
    connectTwoNodes(nodeR.left, nodeR.right)
    // 连接跨越父节点的两个子节点
    connectTwoNodes(nodeL.right, nodeR.left)
  }
  connectTwoNodes(root.left, root.right)
  return root
};
```

### [114. 二叉树展开为链表](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

```js
// 定义：将以 root 为根的树拉平为链表
var flatten = function(root) {
  if (root === null) {
    return null
  }
  flatten(root.left)
  flatten(root.right)
  /* 后序位置 */
  // 1、左右子树已经被拉平成一条链表
  const left = root.left
  const right = root.right
  // 2、将左子树作为右子树
  root.left = null
  root.right = left
  let p = root
  // 3、将原先的右子树接到当前右子树的末端
  while (p.right) {
    p = p.right
  }
  p.right = right
};
```

TODO: https://labuladong.github.io/algo/2/19/35/

## 二叉树的构造

**二叉树的构造问题一般都是使用「分解问题」的思路：构造整棵树 = 根节点 + 构造左子树 + 构造右子树**。

### [654. 最大二叉树](https://leetcode-cn.com/problems/maximum-binary-tree/)

找到数组的最大值，然后递归的构造左右子树。

```js
var constructMaximumBinaryTree = function(nums) {
  // 定义： 构造nums[lo...hi]区间内的树，返回根节点
  const buildTree = (lo, hi) => {
     // baseCase
     if (lo > hi) {
       return null
     }
     // 找出最大值作为根节点
     let maxNum = -Infinity
     let maxIndex = -1
     for (let i = lo; i <= hi; i ++) {
       if (nums[i] > maxNum) {
         maxNum = nums[i]
         maxIndex = i
       }
     }
     let root = new TreeNode(maxNum)
     // 左子树取最大值左侧，右子树取最大值右侧
     root.left = buildTree(lo, maxIndex - 1)
     root.right = buildTree(maxIndex + 1, hi)
    return root
  }
  return buildTree(0, nums.length - 1)
};
```

### [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

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
  let inMap = {}
  for (let i = 0; i < inorder.length; i ++) {
    inMap[inorder[i]] = i
  }
  const buildTree = (preStart, preEnd, inStart, inEnd) => {
    // baseCase
    if (preStart > preEnd) {
      return null
    }
    // 取前序数组第一个为根节点
    const rootNum = preorder[preStart]
    const root = new TreeNode(rootNum)
    const inIndex = inMap[rootNum]
    const leftSize = inIndex - inStart
    root.left = buildTree(preStart + 1, preStart + leftSize, inStart, inIndex - 1)
    root.right = buildTree(preStart + leftSize + 1, preEnd, inIndex + 1, inEnd)
    return root
  }
  return buildTree(0, preorder.length - 1, 0, inorder.length - 1)
};
```

### [106. 从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

```js
var buildTree = function(inorder, postorder) {
  let inMap = {}
  for (let i = 0; i < inorder.length; i ++) {
    inMap[inorder[i]] = i
  }
  const build = (postStart, postEnd, inStart, inEnd) => {
    // baseCase
    if (inStart > inEnd) {
      return null
    }
    // 取后序数组最后一个为根节点
    const rootNum = postorder[postEnd]
    const root = new TreeNode(rootNum)
    const inIndex = inMap[rootNum]
    // 左子树的节点个数
    const leftSize = inIndex - inStart
    root.left = build(postStart, postStart + leftSize - 1, inStart, inIndex - 1)
    root.right = build(postStart + leftSize, postEnd - 1, inIndex + 1, inEnd)
    return root
  }
  return build(0, postorder.length - 1, 0, inorder.length - 1)
};
```

## 二叉搜索树BST的特性

### [230. 二叉搜索树中第K小的元素](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/)

二叉搜索树的中序遍历就是升序

```js
var kthSmallest = function(root, k) {
	// 记录当前元素的排名
  let rank = 0
  // 记录结果
  let res = 0
  const traverse = root => {
    if (root === null) return
    traverse(root.left)
		/* 中序遍历代码位置 */
    rank ++
    // 找到第 k 小的元素
    if (rank === k) {
      res = root.val
      return 
    }
    traverse(root.right)
  } 
  traverse(root)
  return res
};
```

### [538. 把二叉搜索树转换为累加树](https://leetcode-cn.com/problems/convert-bst-to-greater-tree/)

```js
var convertBST = function(root) {
  // BST中序遍历互换即是降序遍历，维护一个外部累加变量 sum，然后把 sum 赋值给 BST 中的每一个节点，就将 BST 转化成累加树
  let sum = 0
  const traverse = root => {
    if (root === null) return 
    traverse(root.right)
    sum += root.val
    root.val = sum
    traverse(root.left)
  }
  traverse(root)
  return root
};
```

## 二叉搜索树BST的基本操作

BST 的完整定义如下：

1、BST 中任意一个节点的左子树所有节点的值都小于该节点的值，右子树所有节点的值都大于该节点的值。

2、BST 中任意一个节点的左右子树都是 BST。

有了 BST 的这种特性，就可以在二叉树中做类似二分搜索的操作，搜索一个元素的效率很高。

### [100. 相同的树](https://leetcode-cn.com/problems/same-tree/)

```js
var isSameTree = function(p, q) {
  if (p === null && q == null) {
    return true
  }
  if (p === null || q === null) {
    // 仅一个为null
    return false
  }
  if (p.val !== q.val) {
    return false
  }
  return isSameTree(p.left, q.left) && isSameTree(p.right, q.right)
};
```

### [98. 验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

```js
/**
 * 根节点的值是所有其左子树的最大值，所有右子树的最小值
 */
var isValidBST = function(root) {
  const isValid = (root, min, max) => {
    if (root === null) {
      return true
    }
    if (min && root.val <= min.val) {
      return false
    } 
    if (max && root.val >= max.val) {
      return false
    }
    return isValid(root.left, min, root) && isValid(root.right, root, max)
  }
  return isValid(root, null, null)
};
```

### [700. 二叉搜索树中的搜索](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

```js
var searchBST = function(root, val) {
  if (root === null) return null
  if (root.val < val) return searchBST(root.right, val)
  if (root.val > val) return searchBST(root.left, val)
  return root
};
```

### [701. 二叉搜索树中的插入操作](https://leetcode-cn.com/problems/insert-into-a-binary-search-tree/)

```js
var insertIntoBST = function(root, val) {
  // 当前节点为空，生成一个新节点
  if (root === null) {
    root = new TreeNode(val)
  }
  // 左右子节点分情况生成
  if (root.val < val) {
    root.right =  insertIntoBST(root.right, val)
  }
  if (root.val > val) {
    root.left = insertIntoBST(root.left, val)
  }
  return root
};
```

### [450. 删除二叉搜索树中的节点](https://leetcode-cn.com/problems/delete-node-in-a-bst/)

```js
var deleteNode = function(root, key) {
  if (root === null) return null
  // 获取最小节点
  const getMinNode = node => {
    while (node.left) {
      node = node.left
    }
    return node
  }
  if (root.val === key) {
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

## 二叉搜索树BST的构造

### [96. 不同的二叉搜索树](https://leetcode-cn.com/problems/unique-binary-search-trees/)

```js
var numTrees = function(n) {
  let memo = new Array(n + 1).fill(0).map(_ => Array(n + 1).fill(0))
  // 计算[start, n]间的节点
  const countNodes = (start, end) => {
    if (start > end) return 1
    if (memo[start][end]) {
      return memo[start][end]
    }
    let res = 0
    for (let i = start; i <= end; i ++) {
      res += countNodes(start, i - 1) * countNodes(i + 1, end)
    }
    memo[start][end] = res
    return res
  }
  return countNodes(1, n)
};
```

### [95. 不同的二叉搜索树 II](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

```js
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

## 遍历

### [144.二叉树的前序遍历](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/) 


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

### [145.二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/) 


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

### [94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

```js
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

```js
// 迭代
var inorderTraversal = function(root) {
  if (root === null) return []
  let stack = []
  let res  = []
  // 左中右，先把左子树全进栈
  while (root || stack.length) {
    if (root) {
      // 当前节点入栈，直到所有左子树全进栈
      stack.push(root)
      root = root.left
    } else {
      root = stack.pop()
      res.push(root.val)
      root = root.right
    }
  }
  return res
};
```



### 层序遍历

#### [102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

```js
var levelOrder = function(root) {
  if (root === null) return []
  let res = []
  let queue = [root]
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

#### [111. 二叉树的最小深度](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)

```js
var minDepth = function(root) {
  // 层序遍历，第一个没有子树的节点即为最小深度
  if (root === null) return 0
  let queue = [root]
  let res = 1
  while (queue.length) {
    const n = queue.length
    for (let i = 0; i < n; i ++) {
      const node = queue.shift()
      if (!node.left && !node.right) {
        return res
      }
      node.left && queue.push(node.left)
      node.right && queue.push(node.right)
    }
    res ++
  }
  return res
};
```

## 路径和

### [124. 二叉树中的最大路径和](https://leetcode.cn/problems/binary-tree-maximum-path-sum/)

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

## 最近公共祖先 LCA 问题

**如果一个节点能够在它的左右子树中分别找到`p`和`q`，则该节点为`LCA`节点**

### [236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

```js
var lowestCommonAncestor = function(root, p, q) {
  if (root === null) {
    return null
  }
  // 公共祖先是自己
  if (root === p || root === q) {
    return root
  }
  let left = lowestCommonAncestor(root.left, p, q)
  let right = lowestCommonAncestor(root.right, p, q)
  // 节点的左右子树同时存在，则该节点为公共祖先
  if (left && right) {
    return root
  }
  return left || right || null
};
```



## 计算完全二叉树的节点数

#### [222. 完全二叉树的节点个数](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

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

# 动态规划

## 子序列问题

### [300. 最长递增子序列](https://leetcode.cn/problems/longest-increasing-subsequence/)

```js
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

### [53. 最大子数组和](https://leetcode.cn/problems/maximum-subarray/)

```js
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

### [1143. 最长公共子序列](https://leetcode.cn/problems/longest-common-subsequence/)

```js
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

### [516. 最长回文子序列](https://leetcode.cn/problems/longest-palindromic-subsequence/)

```js
/**
 * base_case: i === j 时 dp[i][j] = 1
 * dp[i][j]表示s[i...j] 的最长回文子序列
 * s[i] === s[j]时 dp[i][j] = dp[i+1][j-1] + 2,头尾2个字符算上啊
 * dp[i][j] = max(dp[i+1][j], dp[i][j-1])
 */
var longestPalindromeSubseq = function(s) {
  const n = s.length
  let dp = new Array(n).fill(0).map(() => Array(n).fill(0))
  for (let i = n - 1; i >= 0; i --) {
    dp[i][i] = 1
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

