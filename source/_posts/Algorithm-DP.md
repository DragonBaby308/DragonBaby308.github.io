---
title: 算法专题——动态规划
author: DragonBaby308
avatar: 'https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.2/img/header/avatar.jpg'
authorLink: www.dragonbaby308.com
authorAbout:
authorDesc:
categories: 算法
comments: true
date: 2019-09-03 22:40:22
tags:
- 算法
- 背包问题
- 0-1背包
- 完全背包
- DP
- 递归
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/DragonBaby308/cdn@1.0/img/posts/DP.jpg
---

#  算法专题——动态规划

> 参考自[《动态规划套路详解》](https://leetcode-cn.com/problems/coin-change/solution/dong-tai-gui-hua-tao-lu-xiang-jie-by-wei-lai-bu-ke/)，写得真好。

动态规划（Dynamic Programming, `DP`）是一种**将原有问题分解为相对简单的子问题**，来求解复杂问题的方法，常常适用于有**重叠子问题**和**最优子结构**性质的问题。
> 最优子结构：**原问题的解由子问题的最优解构成，并且子问题必须相互独立、互不干扰**

* 原理：**将重复计算子问题转化为查表，保证每个子问题只计算一次** —— 一旦某个给定子问题的解已经算出，则将其记忆化存储，以便下次需要同一个子问题解之时直接**查表**。
* 用途：`DP`常用于**优化递归**问题，使用递归求解时如果需要计算很多次重复子问题，那么利用`DP`可以减少计算量，具有天然剪枝的功能。
* 解`DP`问题的关键：找到**状态转移方程**
> 找到状态转移方程，也就意味着找到了一种暴力破解的方法。

---

##  （〇）解`DP`的惯用三步骤

###  1.递归

* **自顶向下，画递归树，找到递归解法，发现重复子问题**。
> ![](https://dragonbaby308.oss-cn-hangzhou.aliyuncs.com/algorithm/DP/s-rock-e.jpg)  
可以看到存在很多重复计算。

```c
int countPaths(boolean[][] grid, int row, int col)  {
  if (!validSquare(grid, row, col)) {
    return 0;
  }
  if (isAtEnd(grid, row, col)) {
    return 1;
  }
  return countPaths(grid, row + 1, col) + countPaths(grid, row, col + 1);
}
```

###  2.递归 + 记忆化

* **将重复子问题进行记忆化存储**
> 将每次计算所得结果存入一个数组中，每次不是重新求，而是查数组。

###  3.定义状态数组，递推状态转移方程

1. **定义状态数组**（即`dp[n]`数组）
2. **自底向上，递推出状态转移方程，解决**`DP`

```c
//边界条件：最下面一行和最右边一列，opt[i, j] = 0，只有一种走法
if (a[i, j] = '空地') {
  opt[i, j] = opt[i - 1, j] + opt[i, j - 1];
}else { //石头
  opt[i, j] = 0;
}
```

---

##  （一）背包问题

将一系列具有重量w的物品（可能还有价值v），放入一个总容量为C的背包中，满足某种条件（比如放入的物品重量和恰好为C/重量和最大/价值和最大）。

###  （1）0-1背包

0-1背包隐含的信息是：
* **对于每种物品只有两种选择，即“完全装入背包”/“不装入背包”。**
* **不能将同一个物品装入背包多次，也不能只装一个物品的一部分。**

####  ①物品无价值：【重量和恰好为C/重量和最大】

假设有一个总容量为C的背包和n件重量分别为w1,w2,..,wn的物品，能否从n件物品中挑选若干件正好装满背包。
* 当C=10，6件物品种类分别为{1,8,4,3,5,2}，可以找到以下4组解：(1,4,3,2)、(1,4,5)、(8,2)、(3,5,2)
* **状态转移方程：`maxValue[i][j] = Math.max(maxValue[i -1][j], maxValue[i - 1][j - w[i]] + w[i]);`**，其中**0 <= i < n - 1，0 <= j <= C**

> [无价值的0-1背包问题（重量和最大）](http://www.dragonbaby308.com/artsk20190903/)

####  ②物品有价值：【价值和最大】

假设有一个总容量为C的背包，n种物品满足第i种物品重量为wi、价值为vi，应该如何选择装入背包的物品，使得装入背包中的物品价值和最大。

* 用一个二维数组`maxValue[i][C]`来表示第i种物品装入容量为C的背包时的最大价值
* 第i种物品装入背包时最大价值有两种情况：
1. **装入第i种物品时，容量超过C：此时最大价值就是不装入第i种物品时的最大价值 —— `maxValue[i-1][C]`**
2. **装入第i种物品时，容量不超过C：此时最大价值需要在前i-1种物品最大价值上加上第i种物品的价值，同时前i-1种物品的容量需要减去第i种物品的容量 —— `vi + maxValue[i - 1][C - wi]`**
* 状态转移方程：`maxValue[i][C] = Math.max(maxValue[i - 1][C], vi + maxValue[i - 1][C - wi]);`

```java
//@params: int[] w  重量
//@params: int[] v  价值
//@params: int C    背包容量
public static int knapsack(int[] w, int[] v, int C) {
    int size = w.length();
    if(size <= 0) return 0;
    //最大价值数组
    int[][] maxValue = new int[size][C + 1];
    //边界值——只填入物品0时的最大价值
    for(int i = 0; i <= C; i++) {
        //判断容量是否超过
        maxValue[0][i] = w[0] > C ? 0 : v[0];
    }
    //状态转移方程
    for(int j = 1; j < size; j++){
        for(int k = 0; k <= C; k++){
            //这一步是为了过滤掉k - w[j] < 0的情况，防止数组越界
            maxValue[j][k] = maxValue[j - 1][k];
            if(w[j] <= k)
                maxValue[j][k] = Math.max(maxValue[j][k]
                    , maxValue[j - 1][k - w[j]] + v[j]);
        }
    }
    //总共size种，从0开始
    return maxValue[size - 1][C];
}
```

> 以上DP使用了二维数组，空间复杂度是`O(n * C)`。可以优化为一维数组：

```java
//...
int[] maxValue = new int[C + 1];
//边界值——只填入第0种物品
for(int i = 0; i <= C; i++){
    maxValue[i] = w[i] < i ? v[i] : 0;
}
//状态转移方程
for(int j = 1; j < size; j++){
    //从后往前遍历，防止计算结果被覆盖
    for(int k = C; k >= w[j]; k--){
        maxValue[k] = Math.max(maxValue[k], v[j] + maxValue[k - w[j]]);
    }
}
return maxValue[C];
```

---

###  （2）完全背包

* 与“0-1背包”的区别在于**每种物品可以放无限次**。  

* 假设有一个总容量为C的背包，n种物品满足第i种物品重量为wi、价值为vi，**每种物品可以放入无限多次**，应该如何选择装入背包的物品，使得装入背包中的物品价值和最大。

---

###  （3）多重背包

与“0-1背包”的区别在于**每种物品有一个固定的次数上限**。

---

##  （二）Leetcode easy

###  1.最大子序和

> [https://leetcode-cn.com/problems/maximum-subarray/](https://leetcode-cn.com/problems/maximum-subarray/)

给定一个整数数组`nums`，找到一个具有最大和的**连续子数组**（子数组**最少包含一个**元素），返回其最大和。

```text
输入: [-2,1,-3,4,-1,2,1,-5,4]
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6
```

####  扫描法

* 思路：加上一个正数和会增加，加上一个负数和会减少；由于子数组是**连续**的，所以对于当前元素来说，如果加入之前子数组和中得到了一个负数，那么就**将累加结果丢弃并清零，子数组从当前元素重新开始遍历**即可。
> 看到网上有人将其类比成了“赌徒理论” —— 永远只记住自己赢钱的时候，如果哪一天查看总资产发现自己亏本了，就将这一天之前的那些天都忘记，从0开始赌。

* 时间复杂度：`O(N)`

```go
//8 ms
//3.3  MB
func scanMaxSubArray(nums []int) int {
    //最大子数组和，由于子数组必然连续且非空，所以初始值即第一个元素
    max := nums[0]
    //当前子数组和
    sum := 0
    //遍历数组，将当前元素加入子数组（并不需要真正的加入，因为我们关心的只是加入后的数组和）
    for i := 0; i < len(nums); i++ {
        sum += nums[i]
        //如果加入的是正数，更新最大值
        if sum > max {
            max = sum
        }
        //如果和为负数，将和丢弃并清空，即子数组开始元素变更为当前元素
        if sum < 0 {
            sum = 0
        }
    }
    return max
}
```

####  `DP`

* 状态转移方程：`dp[i] = max(dp[i - 1] + nums[i], nums[i])`
* 时间复杂度：`O(N)`

```go
//8 ms
//3.5  MB
func dpMaxSubArray(nums []int) int {
    max := nums[0]
    length := len(nums)
    dp := make([]int, length)
    //dp[i]代表的是数组下标为i的元素加入后，子数组中最大值
    //所以可以知道dp[0] = nums[0]，即第一个元素
    dp[0] = nums[0]
    for i := 1; i < length; i++ {
        dp[i] = nums[i]
        if sum := dp[i -1] + nums[i]; sum > max {
            dp[i] = sum
        }
        //记录最大值
        if sum > max {
            max = sum
        }
    }
    return max
}
```

---

###  2.买卖股票的最佳时机

给定一个数组，它的第`i`个元素是一支给定股票第` i `天的价格。  
如果你最多只允许完成一笔交易（即买入和卖出一支股票），设计一个算法来计算你所能获取的最大利润。  
注意你不能在买入股票前卖出股票。

```text
输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。

输入: [7,6,4,3,1]
输出: 0
解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
```

####  `O(N)`解法

这种思路是很容易想到的，记录最小元素`min`和最大差值`max`，遍历数组，将当前位置元素和`min`做减法，更新`max`和`min`：

```go
// 12 ms
// 3.1 MB
func maxProfit(prices []int) int {
    l := len(prices)
    if l <= 1 {
        return 0
    }
    max := 0           //最大差值
    min := prices[0]   //股票买入最低价
    for i := 1; i < l; i++ {
        tmp := prices[i] - min    //此时卖出赚到的钱
        if tmp > max {
            max = tmp
        }
    //如果赚到的钱是负数，更新最小值
        if tmp < 0 {
            min = prices[i]
        }
    }
    return max
}
```

####  `DP`

根据惯有套路，我们要先**定义状态数组，然后递推出`DP`的状态转移方程**。  
那么状态数组如何定义呢？
1. 首先最容易想到的是一维状态数组：`mp[i]`代表的是到第`i`天的最大利润。在第`i`天的时候可以选择买入股票或者卖出股票，尝试来写状态转移方程：`mp[i] = (price[i] >= 0) ? (mp[i - 1] - price[i]) : (mp[i - 1] + price[i])`。但是这样写是**不对**的！我们还有其他的限制条件：**买入股票时手里不能持有股票**、**卖出股票时手里必须有股票**，所以只用一个一维数组来定义状态是不够的。
2. 我们增加一个维度：`mp[i][has]`代表的是到第`i`天的最大利润，`has ∈ {0, 1}`，`0`表示**手中未持有股票**，`1`表示**手中持有股票**。在第`i - 1`天如果手中没有股票，我们可以第`i`天保持原状或者选择买入，选择两者中的最大值；如果第`i - 1`天手中持有股票，我们可以在第`i`天保持原状或者卖出，选择两者中的最大值。状态转移方程变成了：

```go
mp[i][0] = max(mp[i - 1][0], mp[i - 1][1] + price[i]) //卖出股票，利润加上价格
mp[i][1] = max(mp[i - 1][1], mp[i - 1][0] - price[i]) //买入股票，利润减去价格
```

3. 那么使用二维数组就够了吗？答案是还是不够，我们还有一个限制条件：**最多只能买卖`k`次股票**（本题中`k = 1`）。所以我们要再增加一个维度：`mp[i][cnt][has]`代表的是到第`i`天的最大利润，`cnt`代表**第`i`天之前交易股票的次数**，`has ∈ {0, 1}`（`0`表示手中未持有股票，`1`表示手中持有股票）。状态转移方程变成了：

```go
mp[i][k][0] = max(mp[i - 1][k][0], mp[i - 1][k - 1][1] + price[i])    //卖出股票，利润加上价格
mp[i][k][1] = max(mp[i - 1][k][1], mp[i - 1][k - 1][0] - price[i])    //买入股票，利润减去价格
```

到这一步，最大利润就变成了`max( mp[n - 1][cnt][0] ), cnt在[0, k]范围内循环`

> 那么如果题目改为**手中最多持有`X`股票**呢？  
`has`在`[0, X]`范围内循环即可。  
状态转移方程：`mp[i][k][j] = max(mp[i - 1][k][j], mp[i - 1][k - 1][j + 1] + price[i], mp[i - 1][k - 1][j - 1] - price[i])`
