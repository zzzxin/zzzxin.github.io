---
layout: post
title: " Unique Paths II"
date: 2018-09-10 11:15:06 
description: "动态规划"
tag: dp

---


##LeetCode-63. Unique Paths II(有障碍物的不同路径)

 - 记忆化
 - 二维dp
 - 一维dp

###[题目链接](https://leetcode.com/problems/unique-paths-ii/description/)
###题目
![这里写图片描述](https://img-blog.csdn.net/20180905194540111?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
***
###记忆化
**其实这类题目都是比较套路的，例如[LeetCode62](https://blog.csdn.net/zxzxzx0119/article/details/81807910)，就是一点点改造而已。**
记忆化就是递归加上一个二维数组的记录: 
![这里写图片描述](https://img-blog.csdn.net/20180905194518213?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4enh6eDAxMTk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
	public int[][] map;
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        map = new int[obstacleGrid.length][obstacleGrid[0].length];
        for(int i = 0; i < map.length; i++){
            Arrays.fill(map[i],-1);
        }
        return process(obstacleGrid,0,0);
    }
    
    public int process(int[][] grid,int i,int j){
        if(i == grid.length-1 && j == grid[0].length-1){
            return grid[i][j] == 0 ? 1 : 0;
        }
        if(map[i][j] != -1) return map[i][j];
        if(i == grid.length - 1){
            map[i][j] = grid[i][j] == 1 ? 0 : ( grid[i][j+1] == 0 ? process(grid,i,j+1) : 0);
            return map[i][j];
        }
        if(j == grid[0].length - 1){
            map[i][j] = grid[i][j] == 1 ? 0 : (grid[i+1][j] == 0 ? process(grid,i+1,j) : 0);
            return  map[i][j];
        }
        int right = grid[i][j+1] == 0 ? process(grid,i,j+1) : 0;
        int down = grid[i+1][j] == 0 ? process(grid,i+1,j) : 0;
        map[i][j] = (grid[i][j] == 1) ? 0 : (right + down);
        return map[i][j];
    }
```
简单优化: 

 - <font color = red>由于右边和下面的不管是0还是真的>0的值，都是一个值，不影响；
 - <font color = red>或者说每一个(i,j)没有必要重复判断(i,j+1)或者(i+1,j)位置的值，所以只需要判断自己(i,j)；</font>

```
    public int[][] map;
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        map = new int[obstacleGrid.length][obstacleGrid[0].length];
        for(int i = 0; i < map.length; i++){
            Arrays.fill(map[i],-1);
        }
        return process(obstacleGrid,0,0);
    }
    
    public int process(int[][] grid,int i,int j){
        if(i == grid.length-1 && j == grid[0].length-1){
            return grid[i][j] == 0 ? 1 : 0;
        }
        if(map[i][j] != -1) return map[i][j];
        if(i == grid.length - 1){
            map[i][j] = grid[i][j] == 1 ? 0 : process(grid,i,j+1);
            return map[i][j];
        }
        if(j == grid[0].length - 1){
            map[i][j] = grid[i][j] == 1 ? 0 : process(grid,i+1,j);
            return  map[i][j];
        }
        map[i][j] = (grid[i][j] == 1) ? 0 : (process(grid,i,j+1) + process(grid,i+1,j));
        return map[i][j];
    }
    
```

***
###二维dp
第一种未优化的二维dp，完全根据记忆化的逆向改造: 
```
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int[][] dp = new int[obstacleGrid.length][obstacleGrid[0].length];
        int n = obstacleGrid.length;
        int m = obstacleGrid[0].length;
        dp[n-1][m-1] = obstacleGrid[n-1][m-1] == 1 ? 0 : 1;
        for(int i = n-2; i >= 0; i--)
            dp[i][m-1] = obstacleGrid[i][m-1] == 1 ? 0 : (obstacleGrid[i+1][m-1] == 1 ? 0 : dp[i+1][m-1]);
        for(int j = m-2; j >= 0; j--)
            dp[n-1][j] = obstacleGrid[n-1][j] == 1 ? 0 : (obstacleGrid[n-1][j+1] == 1 ? 0 : dp[n-1][j+1]);
        int right,down;
        for(int i = n-2; i >= 0; i--){
            for(int j = m-2; j >= 0; j--){
                right = obstacleGrid[i][j+1] == 1 ? 0 : dp[i][j+1];
                down = obstacleGrid[i+1][j] == 1 ? 0 : dp[i+1][j];
                dp[i][j] = obstacleGrid[i][j] == 1 ? 0 : (right+down);
            }
        }
        return dp[0][0];
    }
```
优化版本: 
```
  
    public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int[][] dp = new int[obstacleGrid.length][obstacleGrid[0].length];
        int n = obstacleGrid.length;
        int m = obstacleGrid[0].length;
        dp[n-1][m-1] = obstacleGrid[n-1][m-1] == 1 ? 0 : 1;
        for(int i = n-2; i >= 0; i--) dp[i][m-1] = obstacleGrid[i][m-1] == 1 ? 0 : dp[i+1][m-1];
        for(int j = m-2; j >= 0; j--) dp[n-1][j] = obstacleGrid[n-1][j] == 1 ? 0 : dp[n-1][j+1];
        for(int i = n-2; i >= 0; i--){
            for(int j = m-2; j >= 0; j--){
                dp[i][j] = obstacleGrid[i][j] == 1 ? 0 : (dp[i][j+1]+dp[i+1][j]);
            }
        }
        return dp[0][0];
    }
```

***
###一维dp
也就是滚动优化: 
```
　　 public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int[] dp = new int[obstacleGrid[0].length];
        int n = obstacleGrid.length;
        int m = obstacleGrid[0].length;
        dp[m-1] = obstacleGrid[n-1][m-1] == 1 ? 0 : 1; 
        for(int j = m-2; j >= 0; j--)
            dp[j] = obstacleGrid[n-1][j] == 1 ? 0 : (obstacleGrid[n-1][j+1] == 1 ? 0 : dp[j+1]);
        
        int right,down;
        for(int i = n - 2; i >= 0; i--){
            dp[m-1] = obstacleGrid[i][m-1] == 1 ? 0 : (obstacleGrid[i+1][m-1] == 1 ? 0 : dp[m-1]);
            for(int j = m - 2; j >= 0; j--){
                right = obstacleGrid[i][j+1] == 1 ? 0 : dp[j+1];
                down = obstacleGrid[i+1][j] == 1 ? 0 : dp[j];
                dp[j] = obstacleGrid[i][j] == 1 ? 0 : (right + down);
            }
        }
        return dp[0];
    }
```

```
   public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        int[] dp = new int[obstacleGrid[0].length];
        int n = obstacleGrid.length;
        int m = obstacleGrid[0].length;
        dp[m-1] = obstacleGrid[n-1][m-1] == 1 ? 0 : 1; 
        for(int j = m-2; j >= 0; j--) dp[j] = obstacleGrid[n-1][j] == 1 ? 0 : dp[j+1];
        for(int i = n - 2; i >= 0; i--){
            dp[m-1] = obstacleGrid[i][m-1] == 1 ? 0 : dp[m-1];
            for(int j = m - 2; j >= 0; j--){
                dp[j] = obstacleGrid[i][j] == 1 ? 0 : (dp[j+1] + dp[j]);
            }
        }
        return dp[0];
    }
```

***