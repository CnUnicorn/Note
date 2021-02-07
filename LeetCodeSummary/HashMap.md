# 剑指offer 50. 第一个只出现一次的字符

遍历两次字符数组。

第一次遍历，使用HashMap来存储是否重复过这个字符。

第二次遍历，找到第一个不重复的字符。

```java
class Solution {
    public char firstUniqChar(String s) {
        
        Map<Character, Boolean> map = new HashMap<>();
        char[] chars = s.toCharArray();
        
        for (char c : chars) {
            map.put(c, !map.containsKey(c)); // 如果重复出现，就一直使用false覆盖
        }

        for (char c : chars) {
            if (map.get(c)) return c;
        }

        return ' ';
    }
}
```

