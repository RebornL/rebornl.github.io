## 001 Two Sum

**题目**
Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

**Example:** 
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9, 

return [0, 1].

> 思路：直接用HashMap存储差值，之后遍历找到这个差值即可返回。

```java
public int[] twoSum(int[] nums, int target) {      
    Map<Integer, Integer> numToIndex = new HashMap<>();
    int len = nums.length;
    int[] result = new int[2];
    for (int i = 0; i < len; i++) {
        if (numToIndex.containsKey(nums[i])) {
            result[0] = numToIndex.get(nums[i]);
            result[1] = i;
            return result;
        } else {
            numToIndex.put(target-nums[i], i);
        }
    }
    return new int[2];
}
```

## 002 Add Two Numbers

给出两个 **非空** 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 **逆序** 的方式存储的，并且它们的每个节点只能存储 **一位** 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**示例：**

```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

```java
public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode head = new ListNode(0);
        ListNode next = head;

        int sum = 0;
        while(l1 != null || l2 != null) {
            sum /= 10;
            if(l1!=null) {
                sum += l1.val;
                l1 = l1.next;
            }

            if(l2 != null) {
                sum += l2.val;
                l2 = l2.next;
            }

            next.next = new ListNode(sum % 10);
            next = next.next;
        }

        if(sum / 10 == 1) next.next = new ListNode(1);

        return head.next;
    }
```

## 003 Longest Substring Without Repeating 

给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

**示例 1:**

```
输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**示例 2:**

```
输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

```java
public int lengthOfLongestSubstring(String s) {
    if(s.length() == 0 || s.length() == 1) return s.length();
    
    HashMap<Character, Integer> hashMap = new HashMap<>();
    int head = 0;
    int max = 0;
    for (int i = 0; i < s.length(); i++) {
        if(hashMap.containsKey(s.charAt(i))) {
            //跳过重复的字符串，重新设定新的head
            head = Math.max(hashMap.get(s.charAt(i))+1, head);
        }
        //实时更新最长不重复字符串
        max = Math.max(i-head+1, max);
        //重置每个字符最后出现的位置
        hashMap.put(s.charAt(i), i);
    }
    
    return max;
}
```

## 005 Longest Palindromic Substring 最长的回文子字符串

给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。

**示例 1：**

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

**示例 2：**

```
输入: "cbbd"
输出: "bb"
```

> 思路采用动态规划，定义一个check\[i\]\[j\]表示是不是字符串从i到j是不是回文字符串

```java
 public String longestPalindrome(String s) {
 	if(s.length() == 0 || s.length()==1) return s;
    if(s.length() == 2 && s.charAt(0) == s.charAt(1)) return s;
 
     //动态规划
     int left = 0, right = 1;
     int maxLength = 0;
     int len = s.length();
     boolean[][] check = new boolean[len][len];
     for(int i = 0; i < len; i+=1) {
         check[i][i] =true;

         for(int j = 0; j < i; j++) {
             check[j][i] = s.charAt(i)==s.charAt(j) && (i-j<2 || (i > 0 && check[j+1][i-1]));

             if(check[j][i]) {
                 if(i-j+1 > maxLength) {
                     maxLength = i - j + 1;
                     left = j;
                     right = i + 1;
                 }
             }
         }
     }

     return s.substring(left, right);
 }
```

## 11. 盛最多水的容器

给定 *n* 个非负整数 *a*1，*a*2，...，*a*n，每个数代表坐标中的一个点 (*i*, *ai*) 。在坐标内画 *n* 条垂直线，垂直线 *i* 的两个端点分别为 (*i*, *ai*) 和 (*i*, 0)。找出其中的两条线，使得它们与 *x* 轴共同构成的容器可以容纳最多的水。

**说明：**你不能倾斜容器，且 *n* 的值至少为 2。

![img](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

**示例:**

```
输入: [1,8,6,2,5,4,8,3,7]
输出: 49
```

```java
public int maxArea(int[] height) {
    if (height == null || height.length <= 1) return 0;

    int len = height.length;
    int i = 0, j = len-1;
    int maxAre = 0;
    while (i < j) {
        maxAre = Math.max(Math.min(height[i], height[j])*(j-i), maxAre);

        if (height[i]<=height[j]) {
            i++;
        } else {
            j--;
        }
    }
    return maxAre;
}
```

## 20. 有效的括号

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。

注意空字符串可被认为是有效字符串。

**示例 1:**

```
输入: "()"
输出: true
```

**示例 2:**

```
输入: "()[]{}"
输出: true
```

**示例 3:**

```
输入: "(]"
输出: false
```

**示例 4:**

```
输入: "([)]"
输出: false
```

> 这个思路也是很简单，直接使用栈，用于存储左括号，遇到右括号就弹出，采用HashMap保存，左括号为key，右括号为value，利用HashMap的containsKey判断是否为左括号。

```java
public boolean isValid(String s) {
    HashMap<Character, Character> hashMap = new HashMap<>();
    hashMap.put('(', ')');
    hashMap.put('[', ']');
    hashMap.put('{', '}');
    Stack<Character> stack = new Stack<>();
    int len = s.length();
    for(int i = 0; i < len; i++) {

        if(hashMap.containsKey(s.charAt(i))) {
            stack.push(s.charAt(i));//左括号添加
            continue;
        } else {
            if(stack.isEmpty()) return false;
        }
        if(hashMap.get(stack.peek()) == s.charAt(i)) {
            stack.pop();
        } else {
            return false;
        }
    }

    return stack.isEmpty();
}
```

## 21. 合并两个有序链表

将两个有序链表合并为一个新的有序链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**示例：**

```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

> 思路也很简单，就是采用归并排序或者简单不省内存，先构建一个有序数组，再构建链表

```java
public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        //写一种通用的解法，采用小顶堆，用在两个链表中会超时
//        PriorityQueue<Integer> heap = new PriorityQueue<>();
//        while (l1 != null) {
//            heap.add(l1.val);
//        }
//        while (l2 != null) {
//            heap.add(l2.val);
//        }
//
//        ListNode newHead = new ListNode(-1);
//        ListNode temp = newHead;
//        while (!heap.isEmpty()) {
//            temp.next = new ListNode(heap.poll());
//            temp = temp.next;
//        }
//
//        return newHead.next;

    //对于两个链表，采用简单的方式，不会超时，4ms
    ArrayList<Integer> arr = new ArrayList<>();
    while (l1 !=null && l2 != null) {
        if (l1.val < l2.val) {
            arr.add(l1.val);
            l1 = l1.next;
        } else {
            arr.add(l2.val);
            l2 = l2.next;
        }
    }

    while (l1 != null) {
        arr.add(l1.val);
        l1 = l1.next;
    }

    while (l2 != null) {
        arr.add(l2.val);
        l2 = l2.next;
    }

    // int size = arr.size();
    ListNode newHead = new ListNode(-1);
    ListNode temp = newHead;
    for (int num: arr) {
        temp.next = new ListNode(num);
        temp = temp.next;
    }

    return newHead.next;
}
```

## 22. 括号生成

给出 *n* 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且**有效的**括号组合。

例如，给出 *n* = 3，生成结果为：

```
[
  "((()))",
  "(()())",
  "(())()",
  "()(())",
  "()()()"
]
```

> 思路：应该怎么说呢，就是采用递归方式，产生排列组合，递归结束的条件就是左右括号生成的字符串长度等于2n，用两个变量分别表示左右括号的数量。

```java
public List<String> generateParenthesis(int n) {
    int left = n, right = n;
    List<String> result = new ArrayList<>();
    String temp = "(";
    generate(left - 1, right, result, temp);
    return result;
}

public void generate(int left, int right, List<String> result, String temp) {
    if(left == 0 && right == 0) result.add(temp);

    if(left > 0) {
        generate(left-1, right, result, temp+"(");
    }

    if(right > 0 && right > left) {//要在有左括号的情况，才能添加右括号
        generate(left, right-1, result, temp+")");
    }

}
```

## 46. 全排列

给定一个**没有重复**数字的序列，返回其所有可能的全排列。

**示例:**

```
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

> 还是回溯法

```java
public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        
        if(nums == null && nums.length == 0) return result;

        exchange(nums, 0, result);

        return result;
    }

    public void exchange(int[] nums, int index, List<List<Integer>> result) {
        if(index == nums.length-1) {
            List<Integer> temp = new ArrayList<>(nums.length);
            for(int n: nums) {
                temp.add(n);
            }
            result.add(temp);
            return;
        }

        for(int i = index; i < nums.length; i++) {
            swap(nums, index, i);//跟后面的元素交换
            exchange(nums, index+1, result);//探索下一种情况
            swap(nums, i, index);//交换，恢复回来的顺序，再去跟后面的其他元素交换，即固定在一个位置上不断跟其他元素进行交换
        }
    }

    public void swap(int[] nums, int i, int j) {
        int num = nums[i];
        nums[i] = nums[j];
        nums[j] = num;
    }
```

第二种回溯比较简洁，利用标志位判断是否遍历过。

```java
public List<List<Integer>> permute(int[] nums) {
    //第二种做法
    if (nums == null || nums.length == 0) return new ArrayList<>();

    List<List<Integer>> result = new ArrayList<>();
    List<Integer> temp = new ArrayList<>();
    if (nums.length == 1) {
        temp.add(nums[0]);
        result.add(temp);
        return result;
    }

    boolean[] flags = new boolean[nums.length];
    permute(nums, flags, temp, result);
    return result;
}

private void permute(int[] nums, boolean[] flags, List<Integer> temp, List<List<Integer>> result) {
    if (temp.size() == nums.length) {
        result.add(new ArrayList<>(temp));
    }

    for (int i = 0; i < nums.length; i++) {
        if (flags[i]) continue;
        flags[i] = true;
        temp.add(nums[i]);
        permute(nums, flags, temp, result);
        temp.remove(temp.size()-1);
        flags[i] = false;
    }

}
```

## 53. 最大子序和

给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**示例:**

```
输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

```java
public int maxSubArray(int[] nums) {
    //这道题有点像贪心算法
    if (nums == null && nums.length == 0) return 0;
    if (nums.length == 1) return nums[0];

    int sum = nums[0];
    int max = nums[0];
    for (int i = 1; i < nums.length; i++) {
        if(sum > 0) sum += nums[i];
        else sum = nums[i];

        max = Math.max(sum, max);

    }
    System.out.println(max);
    return max;
}
```

## 70.爬楼梯

假设你正在爬楼梯。需要 *n* 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

**注意：**给定 *n* 是一个正整数。

**示例 1：**

```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶
```

**示例 2：**

```
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶
```

> 跟斐波那契数列类似，推到
>
> f(1)=1, f(2)=2, f(3)=f(1)+f(2)=3, f(4) = f(3)+f(2)=5.....

```java
private int[] f = new int[1024];
public int climbStairs(int n) {
    if(f[n]!=0) return f[n];
    f[0] = 1;
    f[1] = 1;
    f[2] = 2;
    for(int i = 3; i<=n; i++) {
        f[i] = f[i-1]+f[i-2];
    }
    return f[n];
}
```

## 94. 二叉树的中序遍历

给定一个二叉树，返回它的*中序* 遍历。

**示例:**

```
输入: [1,null,2,3]
   1
    \
     2
    /
   3

输出: [1,3,2]
```

> 思路很简单，就按照中序遍历的思想，递归调用比较简洁，非递归调用，可以采用队列暂存节点，当遍历，再把值取出来即可。

```java
//递归方式
public List<Integer> inorderTraversal(TreeNode root) {
    //递归调用方式
    if (root == null) return result;
    if (root.left != null) inorderTraversal(root.left);
    result.add(root.val);
    if (root.right != null) inorderTraversal(root.right);
    return result;
}

//非递归方式
List<Integer> inorderLoop = new ArrayList<>();
Deque<TreeNode> nodeQueue = new ArrayDeque<>();
TreeNode point = root;
while(point!=null || !nodeQueue.isEmpty()) {
    // inorderLoop.add(nodeQueue.pop().val);
    while(point!=null) {
        nodeQueue.push(point);
        point = point.left;
    }
    TreeNode tmp = nodeQueue.pop();
    inorderLoop.add(tmp.val);
    point = tmp.right;
}
return inorderLoop;
```

## 101.对称二叉树

给定一个二叉树，检查它是否是镜像对称的。

例如，二叉树 `[1,2,2,3,4,4,3]` 是对称的。

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

但是下面这个 `[1,2,2,null,3,null,3]` 则不是镜像对称的:

```
    1
   / \
  2   2
   \   \
   3    3
```

```java
public boolean isSymmetric(TreeNode root) {
    if (root == null) return true;
    if (root.left == null && root.right == null) return true;
    if (root.left == null || root.right == null) return false;

    return isSymmetric(root.left, root.right);
}

private boolean isSymmetric(TreeNode left, TreeNode right) {
    if (left == null && right == null) return true;
    if (left == null || right == null) return false;

    if (left.val != right.val) return false;

    return isSymmetric(left.left, right.right) && isSymmetric(left.right, right.left);

}
```

## 104.二叉树的最大深度

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

**说明:** 叶子节点是指没有子节点的节点。

**示例：**
给定二叉树 `[3,9,20,null,null,15,7]`，

```
    3
   / \
  9  20
    /  \
   15   7
```

返回它的最大深度 3 。

> 还是递归操作。

```java
public int maxDepth(TreeNode root) {
    if (root == null) return 0;
    if (root.left == null && root.right == null) return 1;

    return Math.max(maxDepth(root.left), maxDepth(root.right))+1;
}
```



## 110.平衡二叉树

给定一个二叉树，判断它是否是高度平衡的二叉树。

本题中，一棵高度平衡二叉树定义为：

> 一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过1。

**示例 1:**

给定二叉树 `[3,9,20,null,null,15,7]`

```
    3
   / \
  9  20
    /  \
   15   7
```

返回 `true` 。

**示例 2:**

给定二叉树 `[1,2,2,3,3,null,null,4,4]`

```
       1
      / \
     2   2
    / \
   3   3
  / \
 4   4
```

返回 `false` 。

> 这道题考察就是递归的使用获取树的最大高度。

```java
public boolean isBalanced(TreeNode root) {
    if(root == null) return true;
    if(root.left == null && root.right == null) return true;

    int left = getHeight(root.left);
    int right = getHeight(root.right);

    if (Math.abs(left-right) <= 1) return isBalanced(root.left) && isBalanced(root.right);
    else return false;

}

private int getHeight(TreeNode root) {
    if(root == null) return 0;
    if(root.left == null && root.right == null) return 1;

    return Math.max(getHeight(root.left), getHeight(root.right))+1;
}
```



## 111. 二叉树的最小深度

给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

**说明:** 叶子节点是指没有子节点的节点。

**示例:**

给定二叉树 `[3,9,20,null,null,15,7]`,

```
    3
   / \
  9  20
    /  \
   15   7
```

> 思路：同上一题类似，但是要考虑题目说的从根节点到叶子节点的最短路径。

```java
public int minDepth(TreeNode root) {
        return getHeight(root);

    }

    private int getHeight(TreeNode root) {
        if(root == null) return 0;
        if(root.left == null && root.right == null) return 1;
        
        int left, right;
        if(root.left == null) left = Integer.MAX_VALUE;
        else left = getHeight(root.left);
        if(root.right == null) right = Integer.MAX_VALUE;
        else right = getHeight(root.right);
        
        return Math.min(left, right)+1;
    }
```



##  124.二叉树中的最大路径和

给定一个**非空**二叉树，返回其最大路径和。

本题中，路径被定义为一条从树中任意节点出发，达到任意节点的序列。该路径**至少包含一个**节点，且不一定经过根节点。

**示例 1:**

```
输入: [1,2,3]

       1
      / \
     2   3

输出: 6
```

**示例 2:**

```
输入: [-10,9,20,null,null,15,7]

   -10
   / \
  9  20
    /  \
   15   7

输出: 42
```

> 思路：递归获取在每个节点能获得最大路径和的值，从下往上推，全局变量实时记录所有路径和的最大。

```java
private int max = Integer.MIN_VALUE;
    public int maxPathSum(TreeNode root) {
        // if(root==null) return 0;
        getMax(root);
        return max;
    }
    
    private int getMax(TreeNode root) {
        if(root == null) return 0;
        int left = getMax(root.left);
        int right = getMax(root.right);
        max = Math.max(Math.max(max, left+right+root.val), Math.max(left, right)+root.val);
        max = Math.max(max, root.val);
        return Math.max(root.val, Math.max(left, right)+root.val);//返回从该节点能获取的最大值
    }
```



## 136.只出现一次的数字

给定一个**非空**整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

**说明：**

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

**示例 1:**

```
输入: [2,2,1]
输出: 1
```

**示例 2:**

```
输入: [4,1,2,1,2]
输出: 4
```

> 普通的for循环用在数组上的速度比foreach快一点
>
> ^= 相对耗内存一点，num1^num2则相对好一点
>
> 思路：两个相同的数字异或为0

```java
public int singleNumber(int[] nums) {
    int result = 0;
    for (int i = 0; i < nums.length; i++) {
        result = result ^ nums[i];
    }
    return result;
}
```



## 141.环形链表

给定一个链表，判断链表中是否有环。

为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。

 

**示例 1：**

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png)

**示例 2：**

```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png)

**示例 3：**

```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png)

 

**进阶：**

你能用 *O(1)*（即，常量）内存解决此问题吗？

> 思路：快慢指针即可，在环中，快慢指针必然相遇。

```java
public boolean hasCycle(ListNode head) {
    if(head == null || head.next == null) return false;

    ListNode slow = head, fast = head.next != null ? head.next.next : null;
    while(slow!=null && fast != null) {
        if (slow == fast) return true;

        slow = slow.next;
        if (fast.next != null) fast = fast.next.next;
        else fast = null;
    }
    return false;
}
```



## 146. LRU缓存机制

运用你所掌握的数据结构，设计和实现一个  [LRU (最近最少使用) 缓存机制](https://baike.baidu.com/item/LRU)。它应该支持以下操作： 获取数据 `get` 和 写入数据 `put` 。

获取数据 `get(key)` - 如果密钥 (key) 存在于缓存中，则获取密钥的值（总是正数），否则返回 -1。
写入数据 `put(key, value)` - 如果密钥不存在，则写入其数据值。当缓存容量达到上限时，它应该在写入新数据之前删除最近最少使用的数据值，从而为新的数据值留出空间。

**进阶:**

你是否可以在 **O(1)** 时间复杂度内完成这两种操作？

**示例:**

```
LRUCache cache = new LRUCache( 2 /* 缓存容量 */ );

cache.put(1, 1);
cache.put(2, 2);
cache.get(1);       // 返回  1
cache.put(3, 3);    // 该操作会使得密钥 2 作废
cache.get(2);       // 返回 -1 (未找到)
cache.put(4, 4);    // 该操作会使得密钥 1 作废
cache.get(1);       // 返回 -1 (未找到)
cache.get(3);       // 返回  3
cache.get(4);       // 返回  4
```

> 思路：用HashMap存储Key-Value值，用链表保持顺序，节点插入采用头插法，获取数据后需要将该节点放到头部，插入节点时需要考虑容量大小，当超过时，删除尾节点，在添加新的节点



## 150. 逆波兰表达式求值

根据[逆波兰表示法](https://baike.baidu.com/item/%E9%80%86%E6%B3%A2%E5%85%B0%E5%BC%8F/128437)，求表达式的值。

有效的运算符包括 `+`, `-`, `*`, `/` 。每个运算对象可以是整数，也可以是另一个逆波兰表达式。

**说明：**

- 整数除法只保留整数部分。
- 给定逆波兰表达式总是有效的。换句话说，表达式总会得出有效数值且不存在除数为 0 的情况。

**示例 1：**

```
输入: ["2", "1", "+", "3", "*"]
输出: 9
解释: ((2 + 1) * 3) = 9
```

**示例 2：**

```
输入: ["4", "13", "5", "/", "+"]
输出: 6
解释: (4 + (13 / 5)) = 6
```

> 这个思路很简单，就是用一个栈专门用来存储一个数字，当遇到运算符号时，就弹出栈中前两个数字，进行运算即可，再将其压回栈中。

```java
public int evalRPN(String[] tokens) {
    Stack<Integer> s = new Stack<>();
    int len = tokens.length;
    int temp1 = 0;
    int temp2 = 0;
    for(int i = 0; i < len; i++) {
        if("*".equals(tokens[i])) {
            temp1 = s.pop();
            temp2 = s.pop();
            s.push(temp1*temp2);
        } else if("/".equals(tokens[i])) {
            temp1 = s.pop();
            temp2 = s.pop();
            s.push(temp2/temp1);
        } else if("+".equals(tokens[i])) {
            temp1 = s.pop();
            temp2 = s.pop();
            s.push(temp1+temp2);
        } else if("-".equals(tokens[i])) {
            temp1 = s.pop();
            temp2 = s.pop();
            s.push(temp2-temp1);
        } else {
            s.push(Integer.parseInt(tokens[i]));
        }
    }
    return s.pop();
}
```



## 121. 买卖股票的最佳时机

给定一个数组，它的第 *i* 个元素是一支给定股票第 *i* 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。

注意你不能在买入股票前卖出股票。

**示例 1:**

```
输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
```

**示例 2:**

```
输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```

> 思路：记录最小值，计算当前值到最小值的获利

```java
public int maxProfit(int[] prices) {
    if (prices == null || prices.length <= 1) return 0;

    int min = prices[0], maxPro = 0;
    for (int i = 1; i < prices.length; i++) {
        maxPro = Math.max(prices[i]-min, maxPro);
        min = Math.min(min, prices[i]);
    }

    return maxPro;
}
```



## [141. 环形链表](https://leetcode-cn.com/problems/linked-list-cycle/)

给定一个链表，判断链表中是否有环。

为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。

 

**示例 1：**

```
输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png)

**示例 2：**

```
输入：head = [1,2], pos = 0
输出：true
解释：链表中有一个环，其尾部连接到第一个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png)

**示例 3：**

```
输入：head = [1], pos = -1
输出：false
解释：链表中没有环。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png)

> 使用快慢指针便可

```java
public boolean hasCycle(ListNode head) {
    if (head == null || head.next == null) return false;

    ListNode fast = head, slow = head;
    do {
        fast = fast.next.next;
        slow = slow.next;
    } while(fast != null && fast.next != null && fast != slow);

    return slow == fast;
}
```





## [142. 环形链表 II](https://leetcode-cn.com/problems/linked-list-cycle-ii/)

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 `null`。

为了表示给定链表中的环，我们使用整数 `pos` 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 `pos` 是 `-1`，则在该链表中没有环。

**说明：**不允许修改给定的链表。

 

**示例 1：**

```
输入：head = [3,2,0,-4], pos = 1
输出：tail connects to node index 1
解释：链表中有一个环，其尾部连接到第二个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png)

**示例 2：**

```
输入：head = [1,2], pos = 0
输出：tail connects to node index 0
解释：链表中有一个环，其尾部连接到第一个节点。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test2.png)

**示例 3：**

```
输入：head = [1], pos = -1
输出：no cycle
解释：链表中没有环。
```

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist_test3.png)

> 思路：使用快慢指针先判断是不是有环的存在，环存在的话，那么将快指针重置即可，快慢指针按照相同步速前进直至相遇，即为入口。

```java
public ListNode detectCycle(ListNode head) {
    if(head == null || head.next == null) return null;

    ListNode slow = head, fast = head;

    while(fast != null && fast.next != null) {
        slow = slow.next;
        fast = fast.next.next;

        if(fast == slow) break;//找到环了
    }

    if(fast == null || fast.next == null) return null;//不存在环

    fast = head;
    while(fast!=slow) {
        fast = fast.next;
        slow = slow.next;
    }
    return slow;
}
```





## [152. 乘积最大子序列](https://leetcode-cn.com/problems/maximum-product-subarray/)

给定一个整数数组 `nums` ，找出一个序列中乘积最大的连续子序列（该序列至少包含一个数）。

**示例 1:**

```
输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```

**示例 2:**

```
输入: [-2,0,-1]
输出: 0
解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。
```

> 大致思路是这样的，最大值可能由最小值乘以一个负数变成最大，而最小值有可能是最大值乘以一个负数变成服务。使用动态规划构建两个数组max[i],min[i]，表示前i个数字的最大值和最小值。

```java
public int maxProduct(int[] nums) {
    if (nums.length == 1) return nums[0];

    int len = nums.length;
    int[] max = new int[len];
    int[] min = new int[len];
    max[0] = nums[0];
    min[0] = nums[0];

    int result = max[0];
    for (int i = 1; i < len; i++) {
        max[i] = Math.max(nums[i], Math.max(nums[i]*max[i-1], nums[i]*min[i-1]));
        min[i] = Math.min(nums[i], Math.max(nums[i]*max[i-1], nums[i]*min[i-1]));
        result = Math.max(result, max[i]);
    }

    return result;
}
```





## 155.最小栈

设计一个支持 push，pop，top 操作，并能在常数时间内检索到最小元素的栈。

- push(x) -- 将元素 x 推入栈中。
- pop() -- 删除栈顶的元素。
- top() -- 获取栈顶元素。
- getMin() -- 检索栈中的最小元素。

**示例:**

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
```

> 思路：构造两个栈，一个用于存储数据，另外一个则用来存储最小

```java
class MinStack {

    Stack<Integer> stack, minStack;

    /** initialize your data structure here. */
    public MinStack() {
        stack = new Stack<>();
        minStack = new Stack<>();
    }

    public void push(int x) {
        stack.push(x);

        if (minStack.isEmpty() || minStack.peek()>=x) {
            minStack.push(x);
        }

    }

    public void pop() {
        int popNum = stack.pop();
        if (minStack.peek()==popNum) {
            minStack.pop();
        }
    }

    public int top() {
        return stack.peek();
    }

    public int getMin() {
        return minStack.peek();
    }
}
```



## 160.相交链表

编写一个程序，找到两个单链表相交的起始节点。

如下面的两个链表**：**

[![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_statement.png)

在节点 c1 开始相交。

**示例 1：**

[![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_1.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_1.png)

```
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Reference of the node with value = 8
输入解释：相交节点的值为 8 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```

**示例 2：**

[![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_2.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_2.png)

```
输入：intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
输出：Reference of the node with value = 2
输入解释：相交节点的值为 2 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [0,9,1,2,4]，链表 B 为 [3,2,4]。在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。
```

 

**示例 3：**

[![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_3.png)](https://assets.leetcode.com/uploads/2018/12/13/160_example_3.png)

```
输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
输入解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
解释：这两个链表不相交，因此返回 null。
```

**注意：**

- 如果两个链表没有交点，返回 `null`.
- 在返回结果后，两个链表仍须保持原有的结构。
- 可假定整个链表结构中没有循环。
- 程序尽量满足 O(*n*) 时间复杂度，且仅用 O(*1*) 内存。

>思路：这道题的想法跟有环的链表类似。设A从初始节点到相交点的距离为a，B从初始节点到相交节点的距离为b，公共节点为c，相交的话必然有a+c+b = b+c+a。

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
    ListNode l1 = headA, l2 = headB;
    while (l1 != l2) {
        l1 = (l1 == null) ? headB : l1.next;
        l2 = (l2 == null) ? headA : l2.next;
    }
    return l1;
}
```



## 169.求众数

给定一个大小为 *n* 的数组，找到其中的众数。众数是指在数组中出现次数**大于** `⌊ n/2 ⌋` 的元素。

你可以假设数组是非空的，并且给定的数组总是存在众数。

**示例 1:**

```
输入: [3,2,3]
输出: 3
```

**示例 2:**

```
输入: [2,2,1,1,1,2,2]
输出: 2
```

> 可以排序后，获取中间值，也可以通过计数的方式获得众数

```java
public int majorityElement(int[] nums) {
	int count = 0, result = nums[0];
    for(int num: nums) {
        if (count == 0) {
            result = num;
            count++;
        } else {
            if(result == num) count++;
            else count--;
        }
    }
    return result;
}
```



## [198. 打家劫舍](https://leetcode-cn.com/problems/house-robber/)

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，**如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警**。

给定一个代表每个房屋存放金额的非负整数数组，计算你**在不触动警报装置的情况下，**能够偷窃到的最高金额。

**示例 1:**

```
输入: [1,2,3,1]
输出: 4
解释: 偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
```

**示例 2:**

```
输入: [2,7,9,3,1]
输出: 12
解释: 偷窃 1 号房屋 (金额 = 2), 偷窃 3 号房屋 (金额 = 9)，接着偷窃 5 号房屋 (金额 = 1)。
     偷窃到的最高金额 = 2 + 9 + 1 = 12 。
```

> 用两个变量记录相隔能获取的最大值，rob为前两间之前能获得最大值，pass为其前一间之前获的最大值

```java
public int rob(int[] nums) {
    if (nums == null || nums.length == 0) return 0;
    if (nums.length <= 2) return nums.length == 1 ? nums[0] : Math.max(nums[1], nums[0]);

    int rob = nums[0], pass = Math.max(nums[1], nums[0]);
    for (int i = 2; i < nums.length; i++) {
        int temp = pass;
        pass = Math.max(pass, rob+nums[i]);
        rob = temp;
    }
    return pass;

}
```

> 摘抄自别的博客，解释的更清楚：解释： 
> 一看到求最大值最小值或者子字符串种类应该首先想到动态规划，一看这道题目是求money的最大值，使用动态规划一维即可解决，现在主要的难题是求出状态转移方程，题目要求是不能相邻，可得出dp[0]=nums[0]，dp[1] = max(dp[0],dp[1]) 。现在对于第i个数有两种选择要么选要么不选，因此求出选和不选的最大值即可，得dp[i] = max(dp[i-2]+nums[i],dp[i-1])



## [200. 岛屿的个数](https://leetcode-cn.com/problems/number-of-islands/)

给定一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，计算岛屿的数量。一个岛被水包围，并且它是通过水平方向或垂直方向上相邻的陆地连接而成的。你可以假设网格的四个边均被水包围。

**示例 1:**

```
输入:
11110
11010
11000
00000

输出: 1
```

**示例 2:**

```
输入:
11000
11000
00100
00011

输出: 3
```

> 思路：深度搜索遍历，将是岛屿的周围都遍历一遍即可。

```java
public int numIslands(char[][] grid) {
    if(grid == null || grid.length == 0) return 0;

    int row = grid.length;
    int col = grid[0].length;

    int islandNums = 0;
    for(int i = 0; i < row; i++) {
        for(int j = 0; j < col; j++) {
            if(grid[i][j] == '1') {
                //发现岛屿时
                islandNums++;
                //判断四周是否有岛屿
                checkIsland(grid, i, j, row, col);

            }
        }
    }

    return islandNums;
}

public void checkIsland(char[][] grid, int i, int j, int row, int col) {
    grid[i][j] = '0';
    //检测右方
    if(j+1<col && grid[i][j+1]=='1') {
        checkIsland(grid, i, j+1, row, col);
    }
    //检测下方
    if(i+1<row && grid[i+1][j]=='1') {
        checkIsland(grid, i+1, j, row, col);
    }
    //检测左方
    if(j>0 && grid[i][j-1]=='1') {
        checkIsland(grid, i, j-1, row, col);
    }
    //检测上方
    if(i>0 && grid[i-1][j]=='1') {
        checkIsland(grid, i-1, j, row, col);
    }
}
```







## [206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)

反转一个单链表。

**示例:**

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

**进阶:**
你可以迭代或递归地反转链表。你能否用两种方法解决这道题？

> 头插法实现迭代反转翻转

```java
public ListNode reverseList(ListNode head) {
    if (head == null || head.next == null) return head;

    ListNode temp1 = null;
    ListNode node = head;
    ListNode next = null;
    while (node != null) {
        next = node.next;
        node.next = temp1;
        temp1 = node;
        node = next;
    }

    return temp1;
}
```



## [215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

在未排序的数组中找到第 **k** 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

**示例 1:**

```
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```

**示例 2:**

```
输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
```

**说明:**

你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。

> 直接小顶堆实现即可，用Java中PriorityQueue实现。另外还可以用快速排序法实现

```java
public int findKthLargest(int[] nums, int k) {
    PriorityQueue<Integer> pQueue = new PriorityQueue<>(k);
    for(int num : nums) {
        if(pQueue.size() < k) pQueue.offer(num);
        else {
            if(num>pQueue.peek()) {
                pQueue.poll();
                pQueue.offer(num);
            }
        }
    }
    return pQueue.peek();
}
```





## [221. 最大正方形](https://leetcode-cn.com/problems/maximal-square/)

在一个由 0 和 1 组成的二维矩阵内，找到只包含 1 的最大正方形，并返回其面积。

**示例:**

```
输入: 

1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0

输出: 4
```

```java
public int maximalSquare(char[][] matrix) {
    if (matrix == null || matrix.length==0 || matrix[0].length == 0) return 0;

    int[][] dp = new int[matrix.length][matrix[0].length];
    int result = 0;
    for (int i = 0; i < matrix[0].length; i++) {
        if (matrix[0][i] == '1') {
            dp[0][i] = 1;
            result = 1;
        }

    }

    for (int j = 0; j < matrix.length; j++) {
        if (matrix[j][0] == '1') {
            dp[j][0] = 1;
            result = 1;
        }
    }

    for (int i = 1; i < matrix.length; i++) {
        for (int j = 1; j < matrix[0].length; j++) {
            //扫描每一行，看看结果
            if (matrix[i][j] == '1') {
                dp[i][j] = Math.min(dp[i-1][j-1], Math.min(dp[i][j-1], dp[i-1][j]))+1;
                result = Math.max(dp[i][j], result);
            }
        }
    }
    return result*result;
}
```





## 226.翻转二叉树

翻转一棵二叉树。

**示例：**

输入：

```
     4
   /   \
  2     7
 / \   / \
1   3 6   9
```

输出：

```
     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

```java
public TreeNode invertTree(TreeNode root) {
    if (root == null) return root;
    if (root.left == null && root.right == null) return root;

    TreeNode left = root.left;
    root.left = root.right;
    root.right = left;
    invertTree(root.left);
    invertTree(root.right);
    return root;
}
```



## 234.回转链表

请判断一个链表是否为回文链表。

**示例 1:**

```
输入: 1->2
输出: false
```

**示例 2:**

```
输入: 1->2->2->1
输出: true
```

**进阶：**
你能否用 O(n) 时间复杂度和 O(1) 空间复杂度解决此题？

> 使用辅助空间，可以用数组和栈解决。不使用辅助空间的话，可以使用反转链表方式。

```java
//使用辅助空间
public boolean isPalindrome(ListNode head) {
    if (head == null || head.next == null) return true;

    ListNode temp1 = head;
    Stack<Integer> s = new Stack<>();
    while (temp1 != null) {
        s.push(temp1.val);
        temp1 = temp1.next;
    }

    int size = s.size();
    for (int i = 0; i < size/2; i++) {
        if (!s.get(i).equals(s.pop())) return false;
    }

    return s.size()==size/2 || s.size()==(size/2+1);
}
```

```java
//不使用辅助空间
public boolean isPalindrome(ListNode head) {
    if(head == null || head.next == null) {
        return true;
    }

    ListNode p1 = head;
    ListNode p2 = head;
    //快慢指针寻找重点
    while(p2.next != null && p2.next.next != null) {
        p1 = p1.next;
        p2 = p2.next.next;
    }

    //反转后半段链表
    p1 = p1.next;
    ListNode prev = null;
    while(p1 != null) {
        ListNode temp = p1.next;
        p1.next = prev;
        prev = p1;
        p1 = temp;
    }

    //prev指向尾节点
    p1 = prev;
    p2 = head;
    while(p1 != null && p2 != null) {
        if(p1.val != p2.val) {
            return false;
        }
        p1 = p1.next;
        p2 = p2.next;
    }

    return p1==null;
}
```



## [236. 二叉树的最近公共祖先](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

[百度百科](https://baike.baidu.com/item/%E6%9C%80%E8%BF%91%E5%85%AC%E5%85%B1%E7%A5%96%E5%85%88/8918834?fr=aladdin)中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（**一个节点也可以是它自己的祖先**）。”

例如，给定如下二叉树:  root = [3,5,1,6,2,0,8,null,null,7,4]

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/15/binarytree.png)

 

**示例 1:**

```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出: 3
解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
```

**示例 2:**

```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 4
输出: 5
解释: 节点 5 和节点 4 的最近公共祖先是节点 5。因为根据定义最近公共祖先节点可以为节点本身。
```

**说明:**

- 所有节点的值都是唯一的。
- p、q 为不同节点且均存在于给定的二叉树中。

> 递归查找，当在左右子树找到自身时，表明公共节点为根节点，否则全都在左子树或右子树，为其自身。
>
> - 判断当期节点是否是待判断节点中的一个，如果是，返回当前节点
> - 判断左子树
> - 判断右子树
> - 如果左右子树都不是空，返回root
> - 如果其中一个为空，返回不为空的那个

```java
public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
    if (root == null || root == p || root == q) return root;

    TreeNode leftNode = lowestCommonAncestor(root.left, p, q);
    TreeNode rightNode = lowestCommonAncestor(root.right, p, q);

    if (leftNode!=null && rightNode!=null) return root;
    if (leftNode == null) return rightNode;
    return leftNode;
}
```





## [238. 除自身以外数组的乘积](https://leetcode-cn.com/problems/product-of-array-except-self/)

给定长度为 *n* 的整数数组 `nums`，其中 *n* > 1，返回输出数组 `output` ，其中 `output[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积。

**示例:**

```
输入: [1,2,3,4]
输出: [24,12,8,6]
```

**说明:** 请**不要使用除法，**且在 O(*n*) 时间复杂度内完成此题。

**进阶：**
你可以在常数空间复杂度内完成这个题目吗？（ 出于对空间复杂度分析的目的，输出数组**不被视为**额外空间。）

```java
public int[] productExceptSelf(int[] nums) {
    int[] result = new int[nums.length];
    result[nums.length-1] = 1;
    for(int i = nums.length-2; i >= 0; i--) {
        result[i] = nums[i+1]*result[i+1];
    }

    for(int i = 1; i < nums.length; i++) {
        result[i] *= nums[i-1];
        nums[i] *= nums[i-1];
    }

    return result;
}
```



## [239. 滑动窗口最大值](https://leetcode-cn.com/problems/sliding-window-maximum/)

给定一个数组 *nums*，有一个大小为 *k* 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口 *k* 内的数字。滑动窗口每次只向右移动一位。

返回滑动窗口最大值。

**示例:**

```
输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7] 
解释: 

  滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**注意：**

你可以假设 *k* 总是有效的，1 ≤ k ≤ 输入数组的大小，且输入数组不为空。

**进阶：**

你能在线性时间复杂度内解决此题吗？

```java
public int[] maxSlidingWindow(int[] nums, int k) {
    if(k==0) return new int[]{};
    if(k==1) return nums;
    int max = nums[0];
    int len = nums.length;
    int[] result = new int[len-k+1];
    for (int i = 1; i < k; i++) {
        max = Math.max(nums[i], max);
    }
    result[0] = max;
    for (int i = 1; i < len-k+1; i++) {
        if (nums[i+k-1]>max) max = nums[i+k-1];//因为localmax能覆盖他所在的好几个区间，但却没有和i+k-1这个值比较过
        else if (nums[i-1] == max) {
            //max不在接下来这个区间重新判断
            max = nums[i];
            for (int j = i+1; j < i+k; j++) {
                max = Math.max(nums[j], max);
            }
        }
        result[i] = max;
    }

    return result;
}
```







## [240. 搜索二维矩阵 II](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/)

编写一个高效的算法来搜索 *m* x *n* 矩阵 matrix 中的一个目标值 target。该矩阵具有以下特性：

- 每行的元素从左到右升序排列。
- 每列的元素从上到下升序排列。

**示例:**

现有矩阵 matrix 如下：

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```

给定 target = `5`，返回 `true`。

给定 target = `20`，返回 `false`。

> 根据从左到右，元素升序，从上到下，元素升序的特点，从右上角开始比较，如果比右上角元素小，必然在这一行；若大于右上角，则继续往下走

```java
public boolean searchMatrix(int[][] matrix, int target) {
    if(matrix == null || matrix.length == 0 || matrix[0] == null) return false;
    int row = matrix.length;
    int col = matrix[0].length;
    
    int i = 0, j = col-1;
    while(j >= 0 && i < row) {
        if(matrix[i][j] == target) return true;
        else if(matrix[i][j] > target) j--;
        else if(matrix[i][j] < target) i++;
    }
    
    return false;
}
```





## [238. 除自身以外数组的乘积](https://leetcode-cn.com/problems/product-of-array-except-self/)

给定长度为 *n* 的整数数组 `nums`，其中 *n* > 1，返回输出数组 `output` ，其中 `output[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积。

**示例:**

```
输入: [1,2,3,4]
输出: [24,12,8,6]
```

**说明:** 请**不要使用除法，**且在 O(*n*) 时间复杂度内完成此题。

**进阶：**你可以在常数空间复杂度内完成这个题目吗？（ 出于对空间复杂度分析的目的，输出数组**不被视为**额外空间。）

```java
public int[] productExceptSelf(int[] nums) {
    int[] result = new int[nums.length];
    // Arrays.fill(result, 1);
    result[0] = 1;
    for (int i = 1; i < nums.length; i++) {
        result[i] = result[i-1]*nums[i-1];//获取相乘的结果
    }
    int temp = 1;
    for (int i = nums.length-2; i >= 0; i--) {
        temp *= nums[i+1];
        result[i] *= temp;
    }
    return result;
}
```



## [279. 完全平方数](https://leetcode-cn.com/problems/perfect-squares/)

给定正整数 *n*，找到若干个完全平方数（比如 `1, 4, 9, 16, ...`）使得它们的和等于 *n*。你需要让组成和的完全平方数的个数最少。

**示例 1:**

```
输入: n = 12
输出: 3 
解释: 12 = 4 + 4 + 4.
```

**示例 2:**

```
输入: n = 13
输出: 2
解释: 13 = 4 + 9.
```

> 思路：动态规划，从小到大往前推，dp[i]表示前i个可以有多少个平方数组成。
>
> 另外一种思路就是数学定理，由完全平方数组成的情况只有四种情况1，2，3，4

```java
public int numSquares(int n) {
    /*动态规划*/
    int[] dp = new int[n+1];//dp[i]表示i这个这个数可以分为多少个正整数的组合
    List<Integer> squares = getSquaresNumbers(n);

    for (int i = 1; i <= n; i++) {
        int min = Integer.MAX_VALUE;
        for (int square: squares) {
            if (square > i) break;
            min = Math.min(min, dp[i-square]+1);
        }
        dp[i] = min;
    }

    return dp[n];
}

private List<Integer> getSquaresNumbers(int n) {
    int end = (int) Math.sqrt(n);
    List<Integer> result = new ArrayList<>();
    for (int i = 1; i <= end; i++) {
        result.add(i*i);
    }

    return result;

}
```







## [283. 移动零](https://leetcode-cn.com/problems/move-zeroes/)

给定一个数组 `nums`，编写一个函数将所有 `0` 移动到数组的末尾，同时保持非零元素的相对顺序。

**示例:**

```
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```

**说明**:

1. 必须在原数组上操作，不能拷贝额外的数组。
2. 尽量减少操作次数。

> 思路：就是直接把非零数字往前移，然后剩下的填0	

```java
public void moveZeroes(int[] nums) {
    int j = 0;
    int len = nums.length;
    for (int i = 0; i<len; i++) {
        if(nums[i]!=0) {
            nums[j++] = nums[i];
        }
    }
    while(j < len) {
        nums[j++] = 0;
    }
}
```



## [287. 寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)

给定一个包含 *n* + 1 个整数的数组 *nums*，其数字都在 1 到 *n* 之间（包括 1 和 *n*），可知至少存在一个重复的整数。假设只有一个重复的整数，找出这个重复的数。

**示例 1:**

```
输入: [1,3,4,2,2]
输出: 2
```

**示例 2:**

```
输入: [3,1,3,4,2]
输出: 3
```

**说明：**

1. **不能**更改原数组（假设数组是只读的）。
2. 只能使用额外的 *O*(1) 的空间。
3. 时间复杂度小于 *O*(*n*2) 。
4. 数组中只有一个重复的数字，但它可能不止重复出现一次。

> 思路：可以将其画成一个有环的链表，重复的数就相当于环的入口。使用快慢指针解决。

```java
public int findDuplicate(int[] nums) {
    if (nums == null) return 0;
    int len = nums.length;
    int slow = nums[0], fast = nums[0];
    do {
        slow = nums[slow];
        fast = nums[nums[fast]];
    } while (slow!=fast);//在环中相遇

    fast = nums[0];
    while (fast != slow) {
        slow = nums[slow];
        fast = nums[fast];
    }

    return slow;
}
```



## [300. 最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)

给定一个无序的整数数组，找到其中最长上升子序列的长度。

**示例:**

```
输入: [10,9,2,5,3,7,101,18]
输出: 4 
解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。
```

**说明:**

- 可能会有多种最长上升子序列的组合，你只需要输出对应的长度即可。
- 你算法的时间复杂度应该为 O(*n2*) 。

**进阶:** 你能将算法的时间复杂度降低到 O(*n* log *n*) 吗?

> 这可以用动态规划解决，result[i]代表着前i个数字中最长的子序列

```java
public int lengthOfLIS(int[] nums) {
    if (nums == null) return 0;
    if (nums.length <= 1) return nums.length;

    int len = nums.length;
    int[] result = new int[len];
    Arrays.fill(result, 1);
    int maxLength = 0;
    for (int i = 1; i < len; i++) {
        for (int j = 0; j < i; j++) {
            result[i] = Math.max(result[i], result[j]+1);
        }
        maxLength = Math.max(maxLength, result[i]);
    }

    return maxLength;
}
```



## [301. 删除无效的括号](https://leetcode-cn.com/problems/remove-invalid-parentheses/)

删除最小数量的无效括号，使得输入的字符串有效，返回所有可能的结果。

**说明:** 输入可能包含了除 `(` 和 `)` 以外的字符。

**示例 1:**

```
输入: "()())()"
输出: ["()()()", "(())()"]
```

**示例 2:**

```
输入: "(a)())()"
输出: ["(a)()()", "(a())()"]
```

**示例 3:**

```
输入: ")("
输出: [""]
```

> 困难级别，暂时没完成，还在小调整中。最后通过率还是只达到91/126，暂时不花时间了，参考一下别人的思路。<https://www.jiuzhang.com/solution/remove-invalid-parentheses/>

```java
public class Solution {
    
    char[][] patterns = { {'(', ')'}, {')', '('} };
    
    public List<String> removeInvalidParentheses(String s) {
        List<String> ret = new ArrayList<>();
       
        dfs(s, 0, 0, patterns[0], ret);
        return ret;
    }
    
    private void dfs(String s, int start, int lastRemove, char[] pattern, List<String> ret) {
        int count = 0, n = s.length();
        for (int i = start; i < n; i ++) {
            if (s.charAt(i) == pattern[0]) 
            { 
                count ++;
            }
            if (s.charAt(i) == pattern[1]) 
            { 
                count --;
            }
            if (count < 0) 
            {
                for (int j = lastRemove; j <= i; j ++) 
                {
                    if (s.charAt(j) == pattern[1] && (j == lastRemove || s.charAt(j) != s.charAt(j - 1))) 
                    {
                        dfs(s.substring(0, j) + s.substring(j + 1), i, j, pattern, ret);
                    }
                }
                return;
            }
        }


        s = new StringBuilder(s).reverse().toString();
        if (pattern[0] == patterns[0][0]) 
        {
            dfs(s, 0, 0, patterns[1], ret);
        } 
        else 
        { 
            ret.add(s); 
        }
    }
}
```





## [309. 最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

给定一个整数数组，其中第 *i* 个元素代表了第 *i* 天的股票价格 。

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

- 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
- 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

**示例:**

```
输入: [1,2,3,0,2]
输出: 3 
解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
```

> 动态规划：<https://zhuanlan.zhihu.com/p/46098185>

```Java
public int maxProfit2(int[] prices) {
    if (prices.length <= 1) {
        return 0;
    }

    if (prices.length == 2) {
        return prices[1]-prices[0]<0 ? 0 : prices[1]-prices[0];
    }

    int len = prices.length;
    int[] dp1 = new int[len];//当天未持有股票的情况
    int[] dp2 = new int[len];//当天持有股票的情况

    dp2[0] = 0 - prices[0];
    for (int i = 1; i < len; i++) {
        dp1[i] = Math.max(dp1[i-1], dp2[i-1]+prices[i]);
        int temp = 0;
        if (i >= 2) {
            temp = dp1[i-2];
        }
        dp2[i] = Math.max(dp2[i-1], temp-prices[i]);
    }

    return Math.max(dp1[len-1], dp2[len-1]);
}
```



## [312. 戳气球](https://leetcode-cn.com/problems/burst-balloons/)

有 `n` 个气球，编号为`0` 到 `n-1`，每个气球上都标有一个数字，这些数字存在数组 `nums` 中。

现在要求你戳破所有的气球。每当你戳破一个气球 `i` 时，你可以获得 `nums[left] * nums[i] * nums[right]` 个硬币。 这里的 `left` 和 `right` 代表和 `i` 相邻的两个气球的序号。注意当你戳破了气球 `i` 后，气球 `left` 和气球 `right` 就变成了相邻的气球。

求所能获得硬币的最大数量。

**说明:**

- 你可以假设 `nums[-1] = nums[n] = 1`，但注意它们不是真实存在的所以并不能被戳破。
- 0 ≤ `n` ≤ 500, 0 ≤ `nums[i]` ≤ 100

**示例:**

```
输入: [3,1,5,8]
输出: 167 
解释: nums = [3,1,5,8] --> [3,5,8] -->   [3,8]   -->  [8]  --> []
     coins =  3*1*5      +  3*5*8    +  1*3*8      + 1*8*1   = 167
```

> 这个动态规划还得考虑间隔，比较复杂，
>
> 详细可查看这几位博客：
>
> <https://blog.csdn.net/jmspan/article/details/51208865>
>
> <https://blog.csdn.net/XX_123_1_RJ/article/details/81638353>

```java
public int maxCoins(int[] nums) {
    int[] dpnums = new int[nums.length+2];
    dpnums[0] = 1;
    dpnums[nums.length+1] = 1;
    System.arraycopy(nums, 0, dpnums, 1, nums.length);

    int[][] coins = new int[dpnums.length][dpnums.length];
    for (int i = 2; i < dpnums.length; i++) {
        for (int j = i-2; j >= 0; j--) {
            for (int k = i-1; k > j; k--) {
                coins[j][i] = Math.max(coins[j][i], coins[j][k]+dpnums[j]*dpnums[k]*dpnums[i]+coins[k][i]);
            }
        }
    }

    return coins[0][dpnums.length-1];
}
```





## [322. 零钱兑换](https://leetcode-cn.com/problems/coin-change/)

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 `-1`。

**示例 1:**

```
输入: coins = [1, 2, 5], amount = 11
输出: 3 
解释: 11 = 5 + 5 + 1
```

**示例 2:**

```
输入: coins = [2], amount = 3
输出: -1
```

**说明**: 你可以认为每种硬币的数量是无限的。

> 动态规划：统计amount之前所有金额需要构成的金币个数

```java
public int coinChange(int[] coins, int amount) {
    int len = coins.length;
    int[] result = new int[amount+1];
    result[0]  = 0;//0的组成只有0种
    for (int i = 1; i <= amount; i++) {
        result[i] = Integer.MAX_VALUE-1;
        for (int j = 0; j < len; j++) {
            if(coins[j] <= i) {
                result[i] = Math.min(result[i], result[i-coins[j]]+1);
            }
        }
    }

    return result[amount] == Integer.MAX_VALUE-1 ? -1 : result[amount];
}
```





## [337. 打家劫舍 III](https://leetcode-cn.com/problems/house-robber-iii/)

在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。

计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

**示例 1:**

```
输入: [3,2,3,null,3,null,1]

     3
    / \
   2   3
    \   \ 
     3   1

输出: 7 
解释: 小偷一晚能够盗取的最高金额 = 3 + 3 + 1 = 7.
```

**示例 2:**

```
输入: [3,4,5,1,3,null,1]

     3
    / \
   4   5
  / \   \ 
 1   3   1

输出: 9
解释: 小偷一晚能够盗取的最高金额 = 4 + 5 = 9.
```

> 思路就是回溯法，通过递归获取当前节点能获取的最大值，然后比较偷当前的还是之前比较

```java
public int rob(TreeNode root) {
    if(root == null)
        return 0;
    int s0 = 0;
    int s1 = root.val;

    s0 = rob(root.left) + rob(root.right);//不偷当前这一层的金额

    if(root.left != null)
        //偷取当前的这层的金额，只能隔层再偷
        s1 += rob(root.left.left) + rob(root.left.right);
    if(root.right != null)
        //偷取当前的这层的金额，只能隔层再偷
        s1 += rob(root.right.left) + rob(root.right.right);
    return Math.max(s0,s1);//最后比较偷当前层和不偷当前层的结果

}
```



## [338. 比特位计数](https://leetcode-cn.com/problems/counting-bits/)

给定一个非负整数 **num**。对于 **0 ≤ i ≤ num** 范围中的每个数字 **i** ，计算其二进制数中的 1 的数目并将它们作为数组返回。

**示例 1:**

```
输入: 2
输出: [0,1,1]
```

**示例 2:**

```
输入: 5
输出: [0,1,1,2,1,2]
```

> 使用自带的内置函数Integer.bitCount(int n)，可以直接获取二进制1的个数。
>
> 第二种方法就是发现规律：result[i] = result[i/2]+(i%2)。

```java
public int[] countBits(int num) {
    int[] result = new int[num+1];
    result[0] = 0;
    for(int i = 1; i <= num; i++) {
		result[i] = Integer.bitCount(i);
        //result[i] = result[i/2]+(i%2);
    }
    return result;
}
```



## [347. 前K个高频元素](https://leetcode-cn.com/problems/top-k-frequent-elements/)

给定一个非空的整数数组，返回其中出现频率前 **k** 高的元素。

**示例 1:**

```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
```

**示例 2:**

```
输入: nums = [1], k = 1
输出: [1]
```

**说明：**

- 你可以假设给定的 *k* 总是合理的，且 1 ≤ k ≤ 数组中不相同的元素的个数。
- 你的算法的时间复杂度**必须**优于 O(*n* log *n*) , *n* 是数组的大小。

```java
public List<Integer> topKFrequent(int[] nums, int k) {
    HashMap<Integer, Integer> map = new HashMap<>();
    int maxFreq = 0;
    for (int num: nums) {
        map.put(num, map.getOrDefault(num, 0)+1);
        maxFreq = Math.max(maxFreq, map.get(num));
    }
    List<ArrayList<Integer>> arr = new ArrayList<>(nums.length+1);
    for (int i = 0; i<=nums.length; i++) {
        arr.add(new ArrayList<>(maxFreq));
    }
    //        Arrays.fill(arr, 0);
    for (Map.Entry<Integer, Integer> kv: map.entrySet()) {
        arr.get(kv.getValue()).add(kv.getKey());
    }

    List<Integer> result = new ArrayList<>(k);
    for (int i = nums.length; i > 0; i--) {
        if (arr.get(i).size()==0) {
            continue;
        }
        if (k==0) {
            break;
        }
        for (int num: arr.get(i)) {
            if(k>0) {
                result.add(num);
                k--;
            }
        }
    }

    return result;
}
```



## [394. 字符串解码](https://leetcode-cn.com/problems/decode-string/)

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: `k[encoded_string]`，表示其中方括号内部的 *encoded_string* 正好重复 *k* 次。注意 *k* 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 *k* ，例如不会出现像 `3a` 或 `2[4]` 的输入。

**示例:**

```
s = "3[a]2[bc]", 返回 "aaabcbc".
s = "3[a2[c]]", 返回 "accaccacc".
s = "2[abc]3[cd]ef", 返回 "abcabccdcdcdef".
```

> 思路：利用栈，一个栈存储数字，另一个栈存储字符串，遇到数字时，将其存储到数字栈中，遇到'['开始遇到需要重复的字段，将原有字符串暂存到字符串栈中，遇到']'，取出字符串栈中缓存的字符串，添加需要重复的字符串。

```java
public String decodeString(String s) {
    int len = s.length();
    int index = 0;
    Stack<Integer> repeatStack = new Stack<>();
    Stack<String> strStack = new Stack<>();
    String res = "";
    while (index < len) {
        if (Character.isDigit(s.charAt(index))) {
            int repeat = 0;
            while (Character.isDigit(s.charAt(index))) {
                repeat = repeat*10 + Integer.parseInt(s.substring(index, index+1));
                index++;
            }
            repeatStack.add(repeat);
        } else if (s.charAt(index)=='[') {
            strStack.add(res);
            res = "";
            index++;
        } else if (s.charAt(index)==']') {
            StringBuilder temp = new StringBuilder(strStack.pop());
            int repeat = repeatStack.pop();
            while (repeat-->0) temp.append(res);
            res = temp.toString();
            index++;
        } else {
            res += s.charAt(index++);
        }
    }
    return res;
}
```







## [406. 根据身高重建队列](https://leetcode-cn.com/problems/queue-reconstruction-by-height/)

假设有打乱顺序的一群人站成一个队列。 每个人由一个整数对`(h, k)`表示，其中`h`是这个人的身高，`k`是排在这个人前面且身高大于或等于`h`的人数。 编写一个算法来重建这个队列。

**注意：**
总人数少于1100人。

**示例**

```
输入:
[[7,0], [4,4], [7,1], [5,0], [6,1], [5,2]]

输出:
[[5,0], [7,0], [5,2], [6,1], [4,4], [7,1]]
```

> 首先进行排序，身高按照降序排列，k按照升序排列，最后按照排序后列表，根据k插入到列表中

```java
public int[][] reconstructQueue(int[][] people) {
    if(people == null || people.length == 0) return new int[0][];
    Arrays.sort(people, new Comparator<int[]>() {
        @Override
        public int compare(int[] o1, int[] o2) {
            return o1[0]==o2[0] ? o1[1]-o2[1] : o2[0]-o1[1];
        }
    });//建议使用Comparator，速度比lambda更快
    ArrayList<int[]> result = new ArrayList<>();
    for(int[] p : people) {
        result.add(p[1], p);
    }
    return result.toArray(new int[result.size()][]);
}
```





## [416. 分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)

给定一个**只包含正整数**的**非空**数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

**注意:**

1. 每个数组中的元素不会超过 100
2. 数组的大小不会超过 200

**示例 1:**

```
输入: [1, 5, 11, 5]

输出: true

解释: 数组可以分割成 [1, 5, 5] 和 [11].
```

**示例 2:**

```
输入: [1, 2, 3, 5]

输出: false

解释: 数组不能分割成两个元素和相等的子集.
```

> 可以使用动态规划，dp\[i][j]表示的是前面i在包的大小为j的情况能获得的最大值。
>
> 另外一种比较简答是的是采用DFS深度搜索遍历。

```java
public boolean canPartition(int[] nums) {
    int sum=0;
    for(int i=0;i<nums.length;i++) {
        sum+=nums[i];
    }
    if (sum % 2 == 1) return false;
    sum /= 2;
    int n = nums.length;
    int[][] dp = new int[n][sum+1];
    for (int i = nums[0]; i <= sum; i++) {
        dp[0][i] = nums[0];//从0开始计数， 从0开始的最大可获得的价值，只有nums[0]
    }
	//采用动态规划的方法
    for (int i = 1; i < n; i++) {
        for (int j = nums[i]; j <= sum; j++) {
            dp[i][j] = Math.max(dp[i-1][j], dp[i-1][j-nums[i]]+nums[i]);//这里判断取不取当前的商品，不取为dp[i-1][j]
        }
    }

    return dp[n-1][sum] == sum;//最后判断前n件中的获取的商品的值是否为有等于sum,其实是一半
}
```

```java
//dfs方式
public boolean canPartition(int[] nums) {
    int sum=0;
    for(int i=0;i<nums.length;i++) {
        sum+=nums[i];
    }
    if (sum % 2 == 1) return false;
    sum /= 2;
    int n = nums.length;

    return dfs(nums, nums.length-1, sum);
}

private boolean dfs(int[] nums, int index, int sum) {
    if(sum == 0) return true;

    if(index < 0 || sum < 0 || sum < nums[index]) {
        return false;
    }

    return dfs(nums, index-1, sum-nums[index]) || dfs(nums, index-1, sum);
}
```



## [437. 路径总和 III](https://leetcode-cn.com/problems/path-sum-iii/)

给定一个二叉树，它的每个结点都存放着一个整数值。

找出路径和等于给定数值的路径总数。

路径不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

二叉树不超过1000个节点，且节点数值范围是 [-1000000,1000000] 的整数。

**示例：**

```
root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8

      10
     /  \
    5   -3
   / \    \
  3   2   11
 / \   \
3  -2   1

返回 3。和等于 8 的路径有:

1.  5 -> 3
2.  5 -> 2 -> 1
3.  -3 -> 11
```

```java
public int pathSum(TreeNode root, int sum) {
    if (root == null) return 0;
    return checkPathSum(root, sum)+pathSum(root.left, sum)+pathSum(root.right, sum);
}

private int checkPathSum(TreeNode root, int sum) {
    int res = 0;

    if (root == null) return res;
    if (sum == root.val) {
        res++;
    }

    res += checkPathSum(root.left, sum-root.val);
    res += checkPathSum(root.right, sum-root.val);

    return res;
}
```





## [438. 找到字符串中所有字母异位词](https://leetcode-cn.com/problems/find-all-anagrams-in-a-string/)

给定一个字符串 **s** 和一个非空字符串 **p**，找到 **s** 中所有是 **p** 的字母异位词的子串，返回这些子串的起始索引。

字符串只包含小写英文字母，并且字符串 **s** 和 **p** 的长度都不超过 20100。

**说明：**

- 字母异位词指字母相同，但排列不同的字符串。
- 不考虑答案输出的顺序。

**示例 1:**

```
输入:
s: "cbaebabacd" p: "abc"

输出:
[0, 6]

解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的字母异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的字母异位词。
```

 **示例 2:**

```
输入:
s: "abab" p: "ab"

输出:
[0, 1, 2]

解释:
起始索引等于 0 的子串是 "ab", 它是 "ab" 的字母异位词。
起始索引等于 1 的子串是 "ba", 它是 "ab" 的字母异位词。
起始索引等于 2 的子串是 "ab", 它是 "ab" 的字母异位词。
```

```java
public List<Integer> findAnagrams(String s, String p) {
    if(p.length() > s.length()) return new ArrayList<>();

    List<Integer> result = new ArrayList<>();
    int[] arr = new int[26];//记录p中出现的字母
    int len = p.length();
    for (int i = 0; i < len; i++) {
        arr[p.charAt(i)-'a'] += 1;
    }

    int[] temp = arr;
    for (int i = 0; i < s.length()-len+1; i++) {
        arr = temp.clone();
        if (arr[s.charAt(i)-'a']==0) continue;
        else {
            for (int j = i; j < i+len; j++) {
                if (arr[s.charAt(j)-'a']==0) break;
                else arr[s.charAt(j)-'a'] -= 1;
                if (j==i+len-1) result.add(i);
            }
        }
    }

    return result;
}
```



## [448. 找到所有数组中消失的数字](https://leetcode-cn.com/problems/find-all-numbers-disappeared-in-an-array/)

给定一个范围在  1 ≤ a[i] ≤ *n* ( *n* = 数组大小 ) 的 整型数组，数组中的元素一些出现了两次，另一些只出现一次。

找到所有在 [1, *n*] 范围之间没有出现在数组中的数字。

您能在不使用额外空间且时间复杂度为*O(n)*的情况下完成这个任务吗? 你可以假定返回的数组不算在额外空间内。

**示例:**

```
输入:
[4,3,2,7,8,2,3,1]

输出:
[5,6]
```

> 思路：新建一个新的数组，数组存储的值代表着下标数字出现的次数，次数为0，即该数字不存在。

```java
public List<Integer> findDisappearedNumbers(int[] nums) {
    List<Integer> result = new ArrayList<>();
    int[] index = new int[nums.length+1];
    for (int num: nums) {
        index[num] += 1;
    }
    for (int i = 1; i <= nums.length; i++) {
        if (index[i]==0) result.add(i);
    }
    return result;
}
```





## [463. 岛屿的周长](https://leetcode-cn.com/problems/island-perimeter/)

给定一个包含 0 和 1 的二维网格地图，其中 1 表示陆地 0 表示水域。

网格中的格子水平和垂直方向相连（对角线方向不相连）。整个网格被水完全包围，但其中恰好有一个岛屿（或者说，一个或多个表示陆地的格子相连组成的岛屿）。

岛屿中没有“湖”（“湖” 指水域在岛屿内部且不和岛屿周围的水相连）。格子是边长为 1 的正方形。网格为长方形，且宽度和高度均不超过 100 。计算这个岛屿的周长。

 

**示例 :**

> 输入:
> [[0,1,0,0],
>  [1,1,1,0],
>  [0,1,0,0],
>  [1,1,0,0]]
>
> 输出: 16
>
> 解释: 它的周长是下面图片中的 16 个黄色的边：
>
> ![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/10/12/island.png)

> 思路：扩展边界，便于处理边界条件

```java
public int islandPerimeter(int[][] grid) {
    int row = grid.length, col = grid[0].length;
    int[][] newgrid = new int[row+2][col+2];
    for (int i = 0; i < row; i++) {
        for (int j = 0; j < col; j++) {
            newgrid[i+1][j+1] = grid[i][j];
        }
    }


    int result = 0;
    for (int i = 1; i < row+2; i++) {
        for (int j = 1; j < col+2; j++) {
            if (newgrid[i][j] == 1){
                if (newgrid[i-1][j] == 0) result++;// 若1的上边是0，则周长加1
                if (newgrid[i][j+1] == 0) result++;// 若1的右边是0，则周长加1
                if (newgrid[i+1][j] == 0) result++;// 若1的下边是0，则周长加1
                if (newgrid[i][j-1] == 0) result++;// 若1的左边是0，则周长加1
            }
        }
    }

    return result;
}
```



## [494. 目标和](https://leetcode-cn.com/problems/target-sum/)

给定一个非负整数数组，a1, a2, ..., an, 和一个目标数，S。现在你有两个符号 `+` 和 `-`。对于数组中的任意一个整数，你都可以从 `+` 或 `-`中选择一个符号添加在前面。

返回可以使最终数组和为目标数 S 的所有添加符号的方法数。

**示例 1:**

```
输入: nums: [1, 1, 1, 1, 1], S: 3
输出: 5
解释: 

-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3

一共有5种方法让最终目标和为3。
```

> 思路：使用递归（不过看了别人的解答，发现别的耗时都很短，而且思路完全不同）

```java
private int counter = 0;
public int findTargetSumWays(int[] nums, int S) {
    countSumWays(nums, S, 0, 0);
    return counter;
}

private void countSumWays(int[] nums, int s, int index, int sum) {
    if (sum == s && index == nums.length) {
        counter++;
    } else {
        if (index < nums.length) {
            countSumWays(nums, s, index+1, sum+nums[index]);
            countSumWays(nums, s, index+1, sum-nums[index]);
        }
    }

}
```



## [538. 把二叉搜索树转换为累加树](https://leetcode-cn.com/problems/convert-bst-to-greater-tree/)

给定一个二叉搜索树（Binary Search Tree），把它转换成为累加树（Greater Tree)，使得每个节点的值是原来的节点值加上所有大于它的节点值之和。

**例如：**

```
输入: 二叉搜索树:
              5
            /   \
           2     13

输出: 转换为累加树:
             18
            /   \
          20     13
```

> 思路：中序遍历

```java


private int sum = 0;
public TreeNode convertBST(TreeNode root) {
    if (root == null) return root;

    inorderSum(root);
    inorderConvertBST(root);
    return root;
}

private void inorderConvertBST(TreeNode root) {
    if (root == null) return;

    inorderConvertBST(root.left);
    int temp = root.val;
    root.val = sum;
    sum -= temp;
    inorderConvertBST(root.right);
}

private void inorderSum(TreeNode root) {
    if (root == null) return;
    inorderSum(root.left);
    sum+=root.val;
    inorderSum(root.right);

}
```





## [543. 二叉树的直径](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过根结点。

**示例 :**
给定二叉树

```
          1
         / \
        2   3
       / \     
      4   5    
```

返回 **3**, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。

**注意：**两结点之间的路径长度是以它们之间边的数目表示。

> 思路：对于每一个结点，经过它的最长路径的长度 = 它的左子树的最大深度 + 右子树的最大深度。

```Java
private int maxDiameter = 0;
public int diameterOfBinaryTree(TreeNode root) {
    if (root == null) return 0;
    if (root.left == null && root.right == null) return 0;

    maxDepth2(root);
    return maxDiameter;
}

private int maxDepth2(TreeNode root) {
    if (root == null) return 0;
    if (root.left == null && root.right == null) return 1;

    int left = maxDepth2(root.left);
    int right = maxDepth2(root.right);

    this.maxDiameter = Math.max(this.maxDiameter, left+right);

    return Math.max(left, right)+1;
}
```


## [560. 和为K的子数组](https://leetcode-cn.com/problems/subarray-sum-equals-k/)

给定一个整数数组和一个整数 **k，**你需要找到该数组中和为 **k** 的连续的子数组的个数。

**示例 1 :**

```
输入:nums = [1,1,1], k = 2
输出: 2 , [1,1] 与 [1,1] 为两种不同的情况。
```

**说明 :**

1. 数组的长度为 [1, 20,000]。
2. 数组中元素的范围是 [-1000, 1000] ，且整数 **k** 的范围是 [-1e7, 1e7]。

> 思路：除了暴力搜索之外，还可以使用hashMap来保存得到的sum的次数，key为sum值，value为次数值，查找sum-k在hashmap中是否有对应的值，就知道有没有对应的连续连续数组。

```java
public int subarraySum(int[] nums, int k) {
    //HashMap存储出现过的sum
    HashMap<Integer, Integer> map = new HashMap<>();
    int sum = 0;
    int count = 0;
    for (int num: nums) {
        sum += num;
        if (map.containsKey(sum-k)) {
            count += map.get(sum-k);
        }
        map.put(sum, map.getOrDefault(sum, 0)+1);
    }
    return count;
}
```



## [547. 朋友圈](https://leetcode-cn.com/problems/friend-circles/)

班上有 **N** 名学生。其中有些人是朋友，有些则不是。他们的友谊具有是传递性。如果已知 A 是 B 的朋友，B 是 C 的朋友，那么我们可以认为 A 也是 C 的朋友。所谓的朋友圈，是指所有朋友的集合。

给定一个 **N \* N** 的矩阵 **M**，表示班级中学生之间的朋友关系。如果M[i][j] = 1，表示已知第 i 个和 j 个学生**互为**朋友关系，否则为不知道。你必须输出所有学生中的已知的朋友圈总数。

**示例 1:**

```
输入: 
[[1,1,0],
 [1,1,0],
 [0,0,1]]
输出: 2 
说明：已知学生0和学生1互为朋友，他们在一个朋友圈。
第2个学生自己在一个朋友圈。所以返回2。
```

**示例 2:**

```
输入: 
[[1,1,0],
 [1,1,1],
 [0,1,1]]
输出: 1
说明：已知学生0和学生1互为朋友，学生1和学生2互为朋友，所以学生0和学生2也是朋友，所以他们三个在一个朋友圈，返回1。
```

**注意：**

1. N 在[1,200]的范围内。
2. 对于所有学生，有M\[i][i] = 1。
3. 如果有M\[i][j] = 1，则有M\[j][i] = 1。

> 思路：使用深度遍历搜索即可，查找到该节点后，得到与之相关联的节点，继续查找该节点

```java
public int findCircleNum(int[][] M) {
    boolean[] visit = new boolean[M.length];
    int count = 0;
    for (int i = 0; i < M.length; i++) {
        if (!visit[i]) {
            count++;
            visit[i] = true;
            dfs(M, visit, i);
        }
    }
    return count;
}

private void dfs(int[][] m, boolean[] visit, int i) {
    int len = m[i].length;
    for (int j = 0; j < len; j++) {
        if (!visit[j] && m[i][j] == 1) {
            visit[j] = true;//找到与之关联的节点
            dfs(m, visit, j);//继续查找与该关联节点相连的节点
        }
    }
}

```



## [572. 另一个树的子树](https://leetcode-cn.com/problems/subtree-of-another-tree/)

给定两个非空二叉树 **s** 和 **t**，检验 **s** 中是否包含和 **t** 具有相同结构和节点值的子树。**s** 的一个子树包括 **s**的一个节点和这个节点的所有子孙。**s** 也可以看做它自身的一棵子树。

**示例 1:**
给定的树 s:

```
     3
    / \
   4   5
  / \
 1   2
```

给定的树 t：

```
   4 
  / \
 1   2
```

返回 **true**，因为 t 与 s 的一个子树拥有相同的结构和节点值。

**示例 2:**
给定的树 s：

```
     3
    / \
   4   5
  / \
 1   2
    /
   0
```

给定的树 t：

```
   4
  / \
 1   2
```

返回 **false**。

> 对于树结构，常用迭代的方式快速查找

```java
public boolean isSubtree(TreeNode s, TreeNode t) {
    boolean result = false;
    if (s != null && t != null) {
        if (s.val == t.val) result = equalCheck(s, t);
        if (!result) {
            result = isSubtree(s.left, t);
        }
        if (!result) {
            result = isSubtree(s.right, t);
        }
    }
    return result;
}

private boolean equalCheck(TreeNode s, TreeNode t) {
    if (s == null && t == null) return true;
    if (s == null || t == null) return false;

    if (s.val != t.val) return false;

    return equalCheck(s.left, t.left) && equalCheck(s.right, t.right);
}
```

#### [581. 最短无序连续子数组](https://leetcode-cn.com/problems/shortest-unsorted-continuous-subarray/)

给定一个整数数组，你需要寻找一个**连续的子数组**，如果对这个子数组进行升序排序，那么整个数组都会变为升序排序。

你找到的子数组应是**最短**的，请输出它的长度。

**示例 1:**

```
输入: [2, 6, 4, 8, 10, 9, 15]
输出: 5
解释: 你只需要对 [6, 4, 8, 10, 9] 进行升序排序，那么整个表都会变为升序排序。
```

**说明 :**

1. 输入的数组长度范围在 [1, 10,000]。
2. 输入的数组可能包含**重复**元素 ，所以**升序**的意思是**<=。**

> 发现规律： 
> 起始点的特征，右边存在小于其的元素； 
> 终止点的特征，左边存在大于其的元素。
>
> (这道题很神奇，一开始都不明白题目的意思，看了别人解释，才知道是要寻找一个起始点【右边比存在一个小于它的】，另外一个终止点【左边元素必然有一个大于它的】)

```java
public int findUnsortedSubarray(int[] nums) {
    if (nums == null || nums.length <= 1) return 0;

    int max = nums[0];
    int min = nums[nums.length-1];
    int start = 0, end = 0;
    for (int i = 1; i < nums.length; i++) {
        max = Math.max(max, nums[i]);
        if (nums[i]<max) end = i;
    }

    for (int j = nums.length-2; j >= 0; j--) {
        min = Math.min(min, nums[j]);
        if (nums[j]>min) start = j;
    }

    return end==0? 0 : end-start+1;
}
```



## [617. 合并二叉树](https://leetcode-cn.com/problems/merge-two-binary-trees/)

给定两个二叉树，想象当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。

你需要将他们合并为一个新的二叉树。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则**不为** NULL 的节点将直接作为新二叉树的节点。

**示例 1:**

```
输入: 
	Tree 1                     Tree 2                  
          1                         2                             
         / \                       / \                            
        3   2                     1   3                        
       /                           \   \                      
      5                             4   7                  
输出: 
合并后的树:
	     3
	    / \
	   4   5
	  / \   \ 
	 5   4   7
```

**注意:** 合并必须从两个树的根节点开始。

> 二叉树的题目常用方法就是递归法。需要考虑好递归的结束条件。

```java
public TreeNode mergeTrees(TreeNode t1, TreeNode t2) {
    if (t1 == null) return t2;
    if (t2 == null) return t1;


    TreeNode root = new TreeNode(t1.val+t2.val);
    root.left = mergeTrees(t1.left, t2.left);
    root.right = mergeTrees(t1.right, t2.right);
    return root;
}
```



## [621. 任务调度器](https://leetcode-cn.com/problems/task-scheduler/)

给定一个用字符数组表示的 CPU 需要执行的任务列表。其中包含使用大写的 A - Z 字母表示的26 种不同种类的任务。任务可以以任意顺序执行，并且每个任务都可以在 1 个单位时间内执行完。CPU 在任何一个单位时间内都可以执行一个任务，或者在待命状态。

然而，两个**相同种类**的任务之间必须有长度为 **n** 的冷却时间，因此至少有连续 n 个单位时间内 CPU 在执行不同的任务，或者在待命状态。

你需要计算完成所有任务所需要的**最短时间**。

**示例 1：**

```
输入: tasks = ["A","A","A","B","B","B"], n = 2
输出: 8
执行顺序: A -> B -> (待命) -> A -> B -> (待命) -> A -> B.
```

**注：**

1. 任务的总个数为 [1, 10000]。
2. n 的取值范围为 [0, 100]。

> 思路只需要考虑出现频率最多的任务，对其进行安排即可得到最短时间，在冷却时间插入其他任务。

```java
public int leastInterval(char[] tasks, int n) {
    int[] count = new int[26];

    for(char task: tasks) {
        count[task-'A']++;
    }

    Arrays.sort(count);
    int highIndex = 25;//判断有多少个相同频率的字符

    while(highIndex >= 0 && count[25]==count[highIndex]) {
        highIndex--;
    }
    //只需要考虑出现的最多的任务需要的次数即可,(count[25]-1)*(n+1)是至少需要出现的次数，而25-highIndex则是出现最多次数的任务最后执行的次数。
    return Math.max((count[25]-1)*(n+1)+25-highIndex, tasks.length);
}
```



## [647. 回文子串](https://leetcode-cn.com/problems/palindromic-substrings/)

给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被计为是不同的子串。

**示例 1:**

```
输入: "abc"
输出: 3
解释: 三个回文子串: "a", "b", "c".
```

**示例 2:**

```
输入: "aaa"
输出: 6
说明: 6个回文子串: "a", "a", "a", "aa", "aa", "aaa".
```

**注意:**

1. 输入的字符串长度不会超过1000。

```java
//version1: 使用两重循环
public int countSubstrings(String s) {
    int len = s.length();
    if (len == 0) return 0;
    boolean[][] paliFlags = new boolean[len][len];
    for (int i = 0; i < len; i++) {
        for (int j = i; j < len; j++) {
            if (i==j) paliFlags[i][j] = true;
            else checkPali(s, paliFlags, i, j);
        }
    }
    int count = 0;
    for (int i = 0; i < len; i++) {
        for (int j = 0; j < len; j++) {
            if (paliFlags[i][j]) count++;
        }
    }
    return count;
}

private void checkPali(String s, boolean[][] paliFlags, int i, int j) {
    int left = i, right = j;
    while (left <= right) {
        if (s.charAt(left++) != s.charAt(right--)) return;
    }
    paliFlags[i][j] = true;

}
```

```java
//version2：更节约时间
private int count = 0;
public int countSubstrings(String s) {
    int len = s.length();
    if (len == 0) return 0;
    for (int i = 0; i < len; i++) {
        checkPali(s, i, i);
        checkPali(s, i, i+1);
    }
    return count;
}

private void checkPali(String s, int i, int j) {
    while (i >= 0 && j < s.length() && s.charAt(i) == s.charAt(j)) {
        count++;
        i--;
        j++;
    }
}
```



## [771. 宝石与石头](https://leetcode-cn.com/problems/jewels-and-stones/)

 给定字符串`J` 代表石头中宝石的类型，和字符串 `S`代表你拥有的石头。 `S` 中每个字符代表了一种你拥有的石头的类型，你想知道你拥有的石头中有多少是宝石。

`J` 中的字母不重复，`J` 和 `S`中的所有字符都是字母。字母区分大小写，因此`"a"`和`"A"`是不同类型的石头。

**示例 1:**

```
输入: J = "aA", S = "aAAbbbb"
输出: 3
```

**示例 2:**

```
输入: J = "z", S = "ZZ"
输出: 0
```

**注意:**

- `S` 和 `J` 最多含有50个字母。
-  `J` 中的字符不重复。

> 思路：直接统计S中字母出现次数，然后再把J中出现的字母的次数加起来即可。

```java
public int numJewelsInStones(String J, String S) {
    int[] arr = new int[58];
    int len = S.length();
    for (int i = 0; i < len; i++) {
        arr[S.charAt(i)-'A'] += 1;
    }
    int count = 0;
    for(int i = 0; i < J.length(); i++) {
        count += arr[J.charAt(i)-'A'];
    }
    return count;
}
```

