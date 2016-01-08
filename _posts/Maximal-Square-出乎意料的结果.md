title: Maximal Square. 出乎意料的结果..
date: 2016-01-03 02:23:31
tags: [leetcode]
---

真的是不知道不明了呀..

有的时候吧，以为自己做的不错，结果却是超时；有的时候吧，以为自己用的是笨方法，结果却是相当不错..这次就算这样子的，用了个笨方法，却超过了88%的方案..

题目如下：https://leetcode.com/problems/maximal-square

就算扫描每个节点，假设以当前节点当做结果正方形的左上角的顶点，求出当前节点下的最大面积。

<!--more-->
感觉这个方案是K*N*N的复杂度吧...

{% img /images/unexpected.png 坑爹的leetcode %}

##code

``` bash
public int maximalSquare(char[][] matrix) {
		if (matrix.length == 0)
			return 0;

		int max = 0;
		int chang = matrix.length;
		int kuan = matrix[0].length;
		for (int i = 0; i < chang; i++) {
			for (int j = 0; j < kuan; j++) {
				if (matrix[i][j] == '1') {
					max = Math.max(max, getMax(matrix, i, j));
				}
			}
		}
		return max;
	}

	private int getMax(char[][] matrix, int x, int y) {
		int chang = matrix.length;
		int kuan = matrix[0].length;
		int bianchang = 1;
		boolean bad = false;
		for (int i = 1; i < Integer.MAX_VALUE; i++) {
			if (x + i < chang && y + i < kuan && !bad) {
				for (int j = 0; j < i; j++) {
					if (matrix[x + i][y + j] != '1' || matrix[x + j][y + i] != '1' || matrix[x + i][y + i] != '1') {
						bad = true;
						break;
					}
				}
				if (!bad)
					bianchang++;
			} else {
				break;
			}
		}
```

2016年1月3日02:30:24