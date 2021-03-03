# 剑指offer 15. 二进制中1的个数

```java
public class Solution {
    // you need to treat n as an unsigned value
    /**
    由于需要将n看成一个无符号数，所以不能直接用模2来判断余数，因为这个时候最高位是做数字位的
    而不是做符号位
    */
    public int hammingWeight(int n) {
        int res = 0;
        while (n != 0) {
            res += n & 1; // n与1做位与，用来判断第一位是否为1，如果为1，那么n&1结果等于1，反之为0
            n = n >>> 1;  // 将n向右移动一位，需要用无符号右移，即符号位带着一起移动
        }
        return res;
    }
}
```



# 剑指offer 14- I. 减绳子

高赞证明：尽可能把每一段绳段长度切成3的时候是最好的。

我的第一反应和信息熵（学通信应该一下就能明白）差不多，尽可能把每一段分成相等的，这样乘积就是最大的。



m的取值范围是：$2 \leq m \leq n$

那么只需要把每一种可能的m，对应分割的方案，都计算一下，取出最大值即可。



那么问题来了：如何实现尽可能相等地分割成m段呢？

* 计算商：`int base =  n / m;`
* 计算余数：`int reminder = n % m;`
* 余数的取值范围是：$0 \leq reminder \leq m - 1$，余数一定不会超过m，所以将余数平均地分配到其他段上即可。即在某些段的值是base + 1。



```java
class Solution {
    public int cuttingRope(int n) {
        
        int res = Integer.MIN_VALUE;
        for (int m = 2;m <= n;m++) { // 2 <= m <= n
            int[] cutArray = subInteger(n, m);
            int tmp = multiplyOfArray(cutArray);
            if (tmp > res) res = tmp;
        }

        return res;
    }

    // 将整数n分成m段，每一段长度尽可能
    public int[] subInteger(int n, int m) {
        int base = n / m;         // 求商
        int reminder = n % m;     // 求余数

        int[] res = new int[m];   
        for (int i = 0;i < m;i++) { // 将余数平均分配到其他段上
            if (reminder == 0) {
                res[i] = base;
            }
            else {
                res[i] = base + 1;
                reminder -= 1;
            }
        }
        
        return res;
    }

    // 计算乘积
    public int multiplyOfArray(int[] arr) {
        int res = 1;
        for (int num : arr) {
            res *= num;
        }
        return res;
    }
}
```



# 剑指offer 44. 数字序列中的某一位数字

找规律，具体分析过程看题解。

大致思路是：

* 先找到第n位数字所在的范围
* 再找到第n位具体是哪个数字
* 再找第n位是数字中的哪一位

```java
class Solution {
    public int findNthDigit(int n) {
        if (n == 0) return 0;
        int digit = 1;             // 第n位所在范围的位数。1位，2位，3位等等 
        long start = 1, count = 9; // start是范围开始的数，count是范围内所有的数字位数的数量
                                   // start和digit会超出int范围，所以用long
        while (n > count) {
            n -= count;            // 递推关系
            start *= 10;           // start = start * 10
            digit += 1;            // digit = digit + 1
            count = 9 * start * digit; // count = 9 * start * digit
        }

        long num = start + (n - 1) / digit; // 找到具体是哪一个数字
        char c = Long.toString(num).charAt((n - 1) % digit); // 再找是数字中的哪一位
        return c - '0';
    }
}
```



# 剑指offer 56-I. 数组中数字出现的次数

**关键知识：**0和任何数字做位异或，得到的都是那个数字本身，并且多个数字做位异或满足交换律，`1^2^3` 和 `3^1^2` 的结果相同。



**简单的情况：**如果数组中，只有一个数字的数量为一个，其他数字的数量都是两个，那么，对整个数组做一个位异或即可（相同的数字位异或，全部为0，剩下的那么只有一个的数字和0异或就是它本身）。



**现在有两个数字该怎么办？**

同样的还是对整个数组做位异或，得到的结果中，**位为1的那几位就是这两个数字不同的位**。使用这些位中的任意一位来区分这两个数字（代码中使用最低的那一位）。

**通过这一位将整个数组分为两组。**和这一位相同（即为1）的，分为一组；不同（即为0）的，分为另一组。这样既可以把这两个数字分开，也可以把出现了两次的数字分到同一组（当然，这两组的数字的数量不一定是相同的）。满足：

1. 出现一次的两个数字分到不同组
2. 相同的数字分到同一组。

最后，对不同组的数组全部做位异或，就能得到出现一次的两个数字了。

```java
class Solution {
    public int[] singleNumbers(int[] nums) {
        int ret = 0;
        for (int n : nums) { // ret中为1的位，就是那两个只出现了一次的数字不同的位
            ret ^= n;
        }
        
        int div = 1;
        while ((ret & div) == 0) { // 为了将这两个数分到不同的组，只取其中不同的一位即可，找到这一位
            div = div << 1;
        }

        int a = 0, b =0;
        for (int n : nums) {
            if ((n & div) == 0) { // 和这一位相与为0说明是一组，为1说明是另一组
                a ^= n;           // 每一组的数全部做异或，得到的就是只出现了一次的那个数
            }
            else b ^= n;
        }
        
        return new int[]{a, b};
    }
}
```



# 剑指 offer 56 - II. 数组中数字出现的次数 II

**遍历思路：**

出现了三次的数字，所有数字**各个**二进制位上出现1的次数的总数为3的倍数，用3对每个二进制位出现1的总次数取余，得到的结果就是出现一次的那个数字在这个二进制位上的值（0或1）。

<img src="https://pic.leetcode-cn.com/28f2379be5beccb877c8f1586d8673a256594e0fc45422b03773b8d4c8418825-Picture1.png" alt="Picture1.png" style="zoom: 50%;" />



**方法一：遍历统计**

```java
class Solution {
    public int singleNumber(int[] nums) {
        int[] count = new int[32];       // 统计每一位上出现1的总次数

        for (int n : nums) {
            for (int j = 31;j >= 0;j--) { // 统计每个数字每一位上是否出现了1
                count[j] += n & 1;
                n = n >>> 1;
            }
        }

        int res = 0, m = 3;          // m替换成其他数值，即可以解决，一个数出现一次，其他数出现m次的问题
        for (int i = 0;i < 32;i++) { // 还原数字
            res = res << 1;
            res |= count[i] % m;
        }
        return res;
    }
}
```



**方法二：优先自动状态机**

使用状态转移图，对状态进行编码，推出状态转换的公式。具体推导见题解。

```java
class Solution {
    public int singleNumber(int[] nums) {
        int ones = 0, twos = 0;

        for (int num : nums) {
            ones = ones ^ num & ~twos;
            twos = twos ^ num & ~ones;
        }

        return ones;
    }
}
```



# 剑指offer 64. 求1+2+...+n

由于不能使用乘除法，所以不能用求和公式。

由于不能使用for和while循环，所以不能循环求和。

剩下的还有递归求和，但是递归求和需要通过 if 判断来结束递归。如下。

```java
class Solution {
    public int sumNums(int n) {
        if (n == 0) return n;
        return n + sumNums(n - 1);
    }
}
```



使用 && 和 || 的短路原则来实现递归结束。

A && B，如果A为false，那么不会再去执行表达式B。

A || B，如果A为true，那么不会再去执行表达式B。

```java
class Solution {
    public int sumNums(int n) {
        boolean flag = n > 0 && (n += sumNums(n - 1)) > 0; // 使用&&前面的 n>0 来做递归结束的出口，如果n <= 0，那么n > 0为false，就不会再做后面递归的表达式运算了，递归结束
//        boolean flag = n <= 0 || (n += sumNums(n - 1)) > 0; // 
        
        return n;
    }
}
```



# 剑指offer 65. 不用加减乘除做加法

由于不能使用加减乘除，所以用位运算来实现加法。



* 非进位和：$n = a \bigoplus b$
* 进位和：$c = (a \& b) << 1$
* 最后结果 = $n + c$，由于不允许出现加法，所以对$n,c$递归做加法，直到进位和为0为止。

**递归写法：**

```java
class Solution {
    public int add(int a, int b) {
        if (b == 0) return a;

        return add(a ^ b, (a & b) << 1);
    }
}
```



**非递归写法：**

```java
class Solution {
    public int add(int a, int b) {
        
        while (b != 0) {
            int c = (a & b) << 1; // c是进位和
            a ^= b;   // a ^ b是非进位和
            b = c;    // 进位和不为0时，对非进位和、进位和迭代做加法
        }
        return a;
    }
}
```



# 剑指offer 62. 圆圈中最后剩下的数字

暂时还没看明白题解

https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/solution/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-by-lee/

```java
class Solution {
    public int lastRemaining(int n, int m) {
        int f = 0;
        for (int i = 2; i != n + 1; ++i) {
            f = (m + f) % i;
        }
        return f;
    }
}
```



# 剑指offer 43. 1~n整数中1出现的次数

https://leetcode-cn.com/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/solution/mian-shi-ti-43-1n-zheng-shu-zhong-1-chu-xian-de-2/

具体的三种情况的推导可以看题解，图中的“易得”，可以对标到生活中的滚轮密码锁，固定其中一位，转动其他位，这样好理解一点，**因为1可以出现的数字范围，最后cur都固定到1**。

三种情况：

1. cur == 0，当前位1出现的次数只由 high（高位）决定，high $\times$ digit，digit为当前位的数量级
2. cur == 1，当前位1出现的次数 = high $\times$ digit + low + 1
3. cur == 2,3,····,9，当前位1出现的次数 = （high + 1） $\times$ digit

```java
class Solution {
    public int countDigitOne(int n) {
        int high = n / 10, cur = n % 10, low = 0;
        int res = 0, digit = 1;
        while(high != 0 || cur != 0) { // 判断条件使用两个或，是为了把最高位也加进去，如果只有high!=0一个条件，那么最高位的计算就会被忽略
            if (cur == 0) res += high * digit;
            else if (cur == 1) res += high * digit + low + 1;
            else res += (high + 1) * digit;
            low += digit * cur;
            cur = high % 10;
            high /= 10;
            digit *= 10;
        }
        return res;
    }
}
```

