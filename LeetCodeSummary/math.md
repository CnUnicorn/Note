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

