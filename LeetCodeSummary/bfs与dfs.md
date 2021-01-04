编写递归的三个重点：

* 递归总有一个**最简单的情况**——方法的第一条语句总是一个包含return的条件语句（也就是递归的结束条件）
* 递归调用总是去尝试解决一个**规模更小的子问题**，这样递归才能收敛到最简单的情况（即第一点）。
* 递归调用的**父问题和尝试解决的子问题之间不应该有交集**。



# 剑指offer 12. 矩阵中的路径

具体思路看题解：

https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        char[] words = word.toCharArray();
        for (int i = 0;i < board.length;i++) {
            for (int j = 0;j < board[0].length;j++) {
                if (dfs(board, words, i, j, 0)) return true; // 以坐标i，j为起点，是否存在可能的路径
            }
        }
        return false;
    }

    public boolean dfs(char[][] board, char[] word, int i, int j, int k) {
        if (i < 0 || i >= board.length || j < 0 || j >= board[0].length || board[i][j] != word[k]) return false; // 排除越界、不匹配和返回原路径的情况 
        if (k == word.length - 1) return true; // 运行到这里，说明字符匹配，如果是最后一个，返回true
        board[i][j] = '\0'; // 把经过的点标记成空格，保证不会重新去遍历经过的点
        boolean res = dfs(board, word, i - 1, j, k + 1) || dfs(board, word, i + 1, j, k + 1) || 
                      dfs(board, word, i, j - 1, k + 1) || dfs(board, word, i, j + 1, k + 1);
        board[i][j] = word[k]; // dfs结束后，将空格还原成原来的值，只针对单个起点的修改，回溯
        return res;
    }
}
```



# 剑指offer 13. 机器人的运动范围

本质是搜索，由于限制条件是坐标点位数和小于等于k，所以只需要向右和向下搜索即可。

**一定需要是搜索，不可以暴力遍历每一个点，判断坐标点和是否小于等于k**。如（10，20）和（1，2）的坐标点位数和是相同的，但是从（0，0）开始，没有办法一次走一步，走到（10，20）这个点上。



1. 使用BFS，遍历右边和下边的点

   ```java
   class Solution {
       public int movingCount(int m, int n, int k) {
           if (k == 0) return 1; // 当k=0时，只有（0，0）可以到达
   
           Queue<int[]> queue = new LinkedList<>(); // 存储可以访问到的点的坐标，BFS
           queue.add(new int[]{0, 0});
           boolean[][] visit = new boolean[m][n]; // 用来标记已经踏入过的格子
           visit[0][0] = true; // 已经将初始（0，0）添加到队列中了，标记为已经过
           int res = 1; // 已经将初始点添加到队列中，统计数初始化为1
   
           int[] dx = {0, 1}, dy = {1, 0}; // 向右和向下的方向数组（方向向量）
           while (!queue.isEmpty()) {
               int[] tmp = queue.poll();
               int x = tmp[0], y = tmp[1];
               // 广度优先遍历，遍历相邻右边和相邻下边的坐标点
               for (int i = 0;i < 2;i++) {
                   int tx = dx[i] + x;
                   int ty = dy[i] + y;
                   if (tx < 0 || tx >= m || ty < 0 || ty >= n || visit[tx][ty] || sumOfNum(tx, ty) > k) {
                       continue; // 越界,或者这个点已经访问过，或者坐标点位数和大于k
                   }
                   visit[tx][ty] = true; // 踏入坐标点，标记为true
                   res++; // 如果没越界且满足小于k的要求，可达数量+1
                   queue.add(new int[]{tx, ty}); // 并将可达点添加到队列中，继续BFS
               }
           }
   
           return res;
   
       }
   
       public int sumOfNum(int i, int j) {
           return i % 10 + i / 10 + j % 10 + j / 10;
       }
   }
   ```



2. 使用递推

   定义使用 `visit[i][j]` 来标记这个坐标点是否可达。

   由上面分析，只能向下或者向右搜索，所以可以得到下列的的递推关系
   $$
   visit[i][j] = visit[i-1][j]\quad || \quad visit[i][j-1]
   $$
   

   ```java
   class Solution {
       public int movingCount(int m, int n, int k) {
           if (k == 0) return 1; // 当k=0时，只有（0，0）可以到达
   
           boolean[][] visit = new boolean[m][n];
           visit[0][0] = true;
           int res = 1;
   
           for (int i = 0;i < m;i++) {
               for (int j = 0;j < n;j++) {
                   if ((i == 0 && j == 0) || sumOfNum(i, j) > k) {
                       continue; // 判断这个坐标点的格子是否可进入，并排除初始点（0，0），因为（0，0）已经在前面计算进去了
                   }
                   if ((i - 1) >= 0) {
                       visit[i][j] |= visit[i - 1][j];
                   }
                   if ((j - 1) >= 0) {
                       visit[i][j] |= visit[i][j - 1];
                   }
                   res += visit[i][j] ? 1 : 0; // 判断当前坐标点是否是可进入的，如果是结果统计+1
               }
           }
   
           return res;
   
       }
   
       public int sumOfNum(int i, int j) {
           return i % 10 + i / 10 + j % 10 + j / 10;
       }
   }
   ```

   上面两种方法的时间复杂度都是O(mn)，都是遍历所有的坐标点。