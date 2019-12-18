# 20 | 理论六：我为何说KISS、YAGNI原则看似简单，却经常被用错？



## 如何理解KISS原则

KISS原则，英文解释版本

- Keep It Simple and Stupid.
- Keep It Short and Simple.
- Keep It Simple and Straightforward.

中文意思，就是尽量保持简单。

KISS原则就是保持代码可读和可维护性。代码简单，就容易读懂，即使有bug，也容易修复。

那么什么是简单的代码，看接下来示例

## 代码行数越少就越“简单”吗？

实现一个功能：检查输入的字符串ipAddress是否是合法的IP地址

一个合法的 IP 地址由四个数字组成，并且通过“.”来进行分割。每组数字的取值范围是 0~255。第一组数字比较特殊，不允许为 0。

```java

// 第一种实现方式: 使用正则表达式
public boolean isValidIpAddressV1(String ipAddress) {
  if (StringUtils.isBlank(ipAddress)) return false;
  String regex = "^(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|[1-9])\\."
          + "(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|\\d)\\."
          + "(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|\\d)\\."
          + "(1\\d{2}|2[0-4]\\d|25[0-5]|[1-9]\\d|\\d)$";
  return ipAddress.matches(regex);
}

// 第二种实现方式: 使用现成的工具类
public boolean isValidIpAddressV2(String ipAddress) {
  if (StringUtils.isBlank(ipAddress)) return false;
  String[] ipUnits = StringUtils.split(ipAddress, '.');
  if (ipUnits.length != 4) {
    return false;
  }
  for (int i = 0; i < 4; ++i) {
    int ipUnitIntValue;
    try {
      ipUnitIntValue = Integer.parseInt(ipUnits[i]);
    } catch (NumberFormatException e) {
      return false;
    }
    if (ipUnitIntValue < 0 || ipUnitIntValue > 255) {
      return false;
    }
    if (i == 0 && ipUnitIntValue == 0) {
      return false;
    }
  }
  return true;
}

// 第三种实现方式: 不使用任何工具类
public boolean isValidIpAddressV3(String ipAddress) {
  char[] ipChars = ipAddress.toCharArray();
  int length = ipChars.length;
  int ipUnitIntValue = -1;
  boolean isFirstUnit = true;
  int unitsCount = 0;
  for (int i = 0; i < length; ++i) {
    char c = ipChars[i];
    if (c == '.') {
      if (ipUnitIntValue < 0 || ipUnitIntValue > 255) return false;
      if (isFirstUnit && ipUnitIntValue == 0) return false;
      if (isFirstUnit) isFirstUnit = false;
      ipUnitIntValue = -1;
      unitsCount++;
      continue;
    }
    if (c < '0' || c > '9') {
      return false;
    }
    if (ipUnitIntValue == -1) ipUnitIntValue = 0;
    ipUnitIntValue = ipUnitIntValue * 10 + (c - '0');
  }
  if (ipUnitIntValue < 0 || ipUnitIntValue > 255) return false;
  if (unitsCount != 3) return false;
  return true;
}
```

第一种是采用正则表达式验证ip地址是否合法，虽然三行代码解决了问题，但是最不符合KISS原则。因为它其实很复杂，正则表达式难以维护和编写，出现了bug也难以解决，所以代码的可读性和可维护性其实是变差了，所以不服可KISS原则。

第二种使用`StringUtils`工具类处理ip地址中出现的数字字符串。而第三种方式，则是取消工具类的使用，手动去实现处理字符。代码行数两者是差不多的。但是，第二种的使用工具类实现的代码逻辑会比第三种实现方式更加清晰和容易理解，而第三种虽然是手动实现，性能会稍好一些，但是也容易出bug（主要在处理字符的那段代码）。一般情况，都会选择工具类实现的第二种，因为常用的工具类的功能都比较全面和通用，考虑的情况更多，bug的出现概率低。除非该函数是影响性能的瓶颈代码，才会选择第三种实现方式。



## 代码逻辑复杂就违背 KISS 原则吗？

```java

// KMP algorithm: a, b分别是主串和模式串；n, m分别是主串和模式串的长度。
public static int kmp(char[] a, int n, char[] b, int m) {
  int[] next = getNexts(b, m);
  int j = 0;
  for (int i = 0; i < n; ++i) {
    while (j > 0 && a[i] != b[j]) { // 一直找到a[i]和b[j]
      j = next[j - 1] + 1;
    }
    if (a[i] == b[j]) {
      ++j;
    }
    if (j == m) { // 找到匹配模式串的了
      return i - m + 1;
    }
  }
  return -1;
}

// b表示模式串，m表示模式串的长度
private static int[] getNexts(char[] b, int m) {
  int[] next = new int[m];
  next[0] = -1;
  int k = -1;
  for (int i = 1; i < m; ++i) {
    while (k != -1 && b[k + 1] != b[i]) {
      k = next[k];
    }
    if (b[k + 1] == b[i]) {
      ++k;
    }
    next[i] = k;
  }
  return next;
}
```

KMP 算法的逻辑复杂，实现难度大，可读性差。但是并不违背KISS，因为需要考虑实际的应用场景。

KMP 算法以快速高效著称。当我们需要处理长文本字符串匹配问题（几百 MB 大小文本内容的匹配），或者字符串匹配是某个产品的核心功能（比如 Vim、Word 等文本编辑器），又或者字符串匹配算法是系统性能瓶颈的时候，我们就应该选择尽可能高效的 KMP 算法。而 KMP 算法本身具有逻辑复杂、实现难度大、可读性差的特点。**本身就复杂的问题，用复杂的方法解决，并不违背 KISS 原则**。

## 如何写出满足 KISS 原则的代码？

- 不要使用同事可能不懂的技术来实现代码
- 不要重复造轮子
- 不要过度优化，过度使用各种奇技淫巧（使用位运算代替算术运算等等）来优化代码，牺牲代码可读性

## YAGNI 跟 KISS 说的是一回事吗？

YAGNI原则的英文全称：You Ain't Gonna Need it. 

直译过来就是你不会需要它。意思就是，在软件开发中，不要去设计当前用不到的功能；不要去编写当前用不到的代码，核心就是，不要过度设计。

另外注意，不要过度设计的意思，并不是不要考虑代码的扩展性。例如系统暂时只用到Redis存储配置信息，以后可能会用到ZooKeeper，但是目前暂时不需要编写ZooKeeper部分的代码，但是我们还是需要保证扩展性，预留好扩展点，等到需要ZooKeeper存储信息时，便于快速实现。

总结来讲就是，KISS原则讲的是“如何做”的问题，尽量保持简单，而YAGNI原则讲的是“要不要做”的问题，当前不需要就不用做。















