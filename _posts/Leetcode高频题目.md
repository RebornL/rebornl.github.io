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
        
        max = Math.max(i-head+1, max);
        
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

private int maxDiameter = 0;

```Java
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