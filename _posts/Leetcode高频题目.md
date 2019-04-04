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

