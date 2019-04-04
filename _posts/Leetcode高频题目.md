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

> 这个思路也是很简单，直接使用栈，用于存储左括号，遇到右括号就弹出

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

