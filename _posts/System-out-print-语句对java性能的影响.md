title: System.out.print 语句对java性能的影响
date: 2015-12-24 12:00:07
tags: [Tips,leetcode]
---
做这个题目的时候发现的：https://leetcode.com/problems/container-with-most-water/

Given n non-negative integers a1, a2, ..., an, where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

Note: You may not slant the container.

我的思想是首尾两个指针扫描，感觉有点快速排序的那个意思。计算当前的面积，与最大面积进行比较，如果当前大，那么最大面积=当前面积。然后比较首尾元素，让更小的那一端移动。直到首尾相差为1。

线性的时间复杂度。但是提交之后却报错time out，百思不得其解..后来我把输出语句给注释掉之后竟然通过了！！原来print语句这么耗费时间的吗？？

长度为5000的数组进行本地测试的结果（注释掉print前后）：
前：137毫秒
后：0毫秒

{% img /images/printAndSpeed.png print对性能的影响 %}


``` bash
public int maxArea(int[] height) {
		if (height.length < 2)
			return 0;
		else {
			int low = 0, high = height.length - 1;
			int max = Math.min(height[low], height[high]) * (high - low);
			while (low < high) {
				if (height[low] <= height[high]) {
					low++;
				} else
					high--;

				System.out.println(low + ", " + high);
				max = Math.max(max, Math.min(height[low], height[high]) * (high - low));
				System.out.println(max);
			}
			System.out.println(max);
			return max;
		}
	}
```

2015年12月24日12:08:07