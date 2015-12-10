title: jave中的break语句跳出的范围？
date: 2015-12-10 15:49:37
tags: Tips
---
如果是多层的for语句，那么break只是跳出当前的循环。
如果是for语句内有while语句，break在while语句中，此时也会跳出for循环。

``` bash
for (int i = 0; i < 30; i++) {
			System.out.println("第 "+i+"次循环");
			while (i < 100) {
				i++;
				if (i == 50) {
					System.out.println("jump out");
					break;
				}
			}
		}
```

``` bash
第 0次循环
jump out
```

2015年12月10日15:51:10