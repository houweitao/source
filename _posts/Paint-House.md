title: Paint House
date: 2015-12-11 11:19:13
tags: [算法,leetcode]
---
There are a row of n houses, each house can be painted with one of the three colors: red, blue or green. The cost of painting each house with a certain color is different. You have to paint all the houses such that no two adjacent houses have the same color.
 
The cost of painting each house with a certain color is represented by a n x 3 cost matrix. For example, costs[0][0] is the cost of painting house 0 with color red; costs[1][2] is the cost of painting house 1 with color green, and so on... Find the minimum cost to paint all houses.
 
Note:
All costs are positive integers.

使用了动态规划..

自顶向下的方法
``` bash
	int paint(int n, int cost[][]) {
		return help(n, cost, 3);
	}

	//思想：动态规划。用forbidden这个参数来实现相邻房子不同颜色。
	//自顶向下
	//如果自底向上的话可以用一个跟cost相同大小的数组，来保存最后一个房子选择这个颜色时的最小花费
	private int help(int n, int cost[][], int forbiden) {
		// TODO Auto-generated method stub
		int min = Integer.MAX_VALUE;
		if (n == 1) {
			for (int i = 0; i < 3; i++) {
				if (i != forbiden) {
					min = Math.min(min, cost[0][i]);
				}
			}
			return min;
		} else {
			for (int i = 0; i < 3; i++) {
				if (i != forbiden)
					min = Math.min(min, cost[n - 1][i] + help(n - 1, cost, i));
			}
			return min;
		}
	}
```

自底向上：
``` bash
	//n:要粉刷的总共n个房子。数目
	//用某种颜色粉刷某个房子的花费。
	//复杂度应该是 n*k*k k为颜色个数，n为房子个数
	private int helpB2U(int n, int cost[][]) {
		// TODO Auto-generated method stub
		int[][] minCost = new int[cost.length][cost[0].length];

		for (int i = 0; i < n; i++) {//i代表粉刷第I+1个房子
			if (i == 0) {
				minCost[0][0] = cost[0][0];
				minCost[0][1] = cost[0][1];
				minCost[0][2] = cost[0][2];
			} else {
				for (int k = 0; k < 3; k++) {//第K个房子，选择第K种颜色的情况
					int min = Integer.MAX_VALUE;
					for (int j = 0; j < 3; j++) {
						if (j != k)
							min = Math.min(min, minCost[i - 1][j]);
					}

					minCost[i][k] = cost[i][k] + min;
				}
			}
		}

		return Math.min(minCost[n - 1][0], Math.min(minCost[n - 1][1], minCost[n - 1][2]));
	}
```

2015年12月11日11:22:36