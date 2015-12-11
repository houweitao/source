title: Google面试题：离所有健身器材最近的位置
date: 2015-12-09 17:44:25
tags: [面试]
---

input是一个二维char数组代表一个gym，@是wall，不能通过，*是健身器材，其他都是空格，要求找一个空格到所有*的距离最短

思路：
建立N个二维数组（N=健身器材的数目），分别保存每一个点到某一个器材位置的大小。最后把他们加起来。取最小的点即为所求。

思路很简单，但是编程实现还真有点麻烦呢。我这里就遇到了个问题是，数组的clone函数。一开始用它，结果总是不对，后来自己挨着复制才解决..貌似clone出来的数组跟原来的数组是等价的，即内存地址相同。

求每个点到某一个器材的位置大小的方法可以是深度优先也可以说是广度优先。直观上广度优先要更合理一些。这里就使用了广度优先的方法。

``` bash
public class MinDistanceToGym2 {
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		MinDistanceToGym2 mg = new MinDistanceToGym2();
		int[][] map = new int[10][10];
		for (int i = 0; i < map.length; i++)
			for (int j = 0; j < map[0].length; j++)
				map[i][j] = Integer.MAX_VALUE;
		map[5][5] = 0;
		map[1][1] = 0;
		map[8][1] = 0;
		map[5][4] = -1;
//		mg.printMap(map);
//		mg.updateMap(map, 5, 5);
//		mg.printMap(map);
		mg.dealGymPoint(map, 5, 5);
		mg.printMap(map);
		// #:空位置; @:墙壁； *：健身器材
		char[][] gym = { { '#', '#', '*', '#', '#', '#', '#', '#', '#', '*' },
				{ '#', '#', '#', '#', '#', '#', '#', '#', '#', '#' },
				{ '#', '#', '#', '#', '#', '#', '#', '#', '#', '#' },
				{ '#', '@', '@', '@', '#', '#', '#', '#', '#', '#' },
				{ '#', '#', '#', '@', '#', '#', '#', '*', '#', '#' },
				{ '#', '#', '#', '#', '#', '#', '@', '#', '#', '#' },
				{ '#', '#', '#', '#', '#', '#', '@', '#', '#', '#' },
				{ '#', '#', '@', '@', '#', '@', '@', '#', '*', '#' },
				{ '*', '#', '#', '#', '#', '#', '#', '#', '#', '#' },
				{ '#', '#', '#', '#', '#', '#', '#', '#', '#', '*' }, };
		Dot bestPlace = mg.getBestPos(gym);
		System.out.println(bestPlace.x + "," + bestPlace.y + " : " + bestPlace.val);
	}
	Dot getBestPos(char[][] gym) {
		ArrayList<Dot> equipment = new ArrayList<>();
		ArrayList<Dot> wall = new ArrayList<>();
		int[][] map = new int[gym.length][gym[0].length];
		getEquipPosition(gym, equipment, wall);
		for (int i = 0; i < equipment.size(); i++) {
			map[equipment.get(i).x][equipment.get(i).y] = -2;
		}
		for (int i = 0; i < wall.size(); i++) {
			map[wall.get(i).x][wall.get(i).y] = -1;
		}
		for (int i = 0; i < map.length; i++) {
			for (int j = 0; j < map[0].length; j++)
				if (map[i][j] == 0)
					map[i][j] = Integer.MAX_VALUE;
				else if (map[i][j] == -2)
					map[i][j] = 0;
		}
		printMap(map);
		int[][][] allMap = new int[equipment.size()][gym.length][gym[0].length];
		for (int i = 0; i < allMap.length; i++) {
//			allMap[i] = map.clone(); 克隆不靠谱啊！！
			for (int k = 0; k < map.length; k++)
				for (int j = 0; j < map[0].length; j++)
					allMap[i][k][j] = map[k][j];
			
			dealGymPoint(allMap[i], equipment.get(i).x, equipment.get(i).y);
		}
		int[][] result = map.clone();
		int minPlace = Integer.MAX_VALUE;
		Dot dotReturn = new Dot(-1, -1, -1);
		for (int i = 0; i < result.length; i++) {
			for (int j = 0; j < result.length; j++) {
				if (result[i][j] > 0) {
					result[i][j] = 0;
					for (int k = 0; k < allMap.length; k++) {
						result[i][j] = result[i][j] + allMap[k][i][j];
					}
					if (minPlace > result[i][j]) {
						minPlace = result[i][j];
						dotReturn = new Dot(i, j, result[i][j]);
					}
				}
			}
		}
		return dotReturn;
	}
	void getEquipPosition(char[][] gym, ArrayList<Dot> g, ArrayList<Dot> wall) {
		for (int i = 0; i < gym.length; i++) {
			for (int j = 0; j < gym[0].length; j++) {
				if (gym[i][j] == '@')
					wall.add(new Dot(i, j, -1));
				else if (gym[i][j] == '*')
					g.add(new Dot(i, j, 0));
			}
		}
	}
	void dealGymPoint(int[][] map, int i, int j) {
		System.out.println(i + "," + j);
		Queue<Dot> queue = new LinkedList<Dot>();
		queue.offer(new Dot(i, j, 0));
		while (!queue.isEmpty()) {
			Dot dot = queue.poll();
			updateMapByQueue(map, dot.x, dot.y, queue);
		}
//		updateMap(map, i, j);
		printMap(map);
	}
	//用队列实现。广度优先
	void updateMapByQueue(int[][] map, int i, int j, Queue<Dot> queue) {
		if (inMap(map, i, j)) {
			if (inMap(map, i + 1, j) && map[i + 1][j] > map[i][j] + 1) {
				map[i + 1][j] = map[i][j] + 1;
				queue.offer(new Dot(i + 1, j, map[i + 1][j]));
			}
			if (inMap(map, i, j - 1) && map[i][j - 1] > map[i][j] + 1) {
				map[i][j - 1] = map[i][j] + 1;
				queue.offer(new Dot(i, j - 1, map[i][j - 1]));
			}
			if (inMap(map, i - 1, j) && map[i - 1][j] > map[i][j] + 1) {
				map[i - 1][j] = map[i][j] + 1;
				queue.offer(new Dot(i - 1, j, map[i - 1][j]));
			}
			if (inMap(map, i, j + 1) && map[i][j + 1] > map[i][j] + 1) {
				map[i][j + 1] = map[i][j] + 1;
				queue.offer(new Dot(i, j + 1, map[i][j + 1]));
			}
		}
	}
	//深度优先
	void updateMap(int[][] map, int i, int j) {
		if (inMap(map, i, j)) {
			if (inMap(map, i + 1, j) && map[i + 1][j] > map[i][j] + 1) {
				map[i + 1][j] = map[i][j] + 1;
				updateMap(map, i + 1, j);
			}
			if (inMap(map, i, j - 1) && map[i][j - 1] > map[i][j] + 1) {
				map[i][j - 1] = map[i][j] + 1;
				updateMap(map, i, j - 1);
			}
			if (inMap(map, i - 1, j) && map[i - 1][j] > map[i][j] + 1) {
				map[i - 1][j] = map[i][j] + 1;
				updateMap(map, i - 1, j);
			}
			if (inMap(map, i, j + 1) && map[i][j + 1] > map[i][j] + 1) {
				map[i][j + 1] = map[i][j] + 1;
				updateMap(map, i, j + 1);
			}
		}
	}
	//判断给出的点是否在图中。是否越界
	private boolean inMap(int[][] map, int i, int j) {
		// TODO Auto-generated method stub
		if (i >= 0 && i < map.length && j >= 0 && j < map[0].length)
			return true;
		else
			return false;
	}
	void printMap(int[][] map) {
		for (int i = 0; i < map.length; i++) {
			for (int j = 0; j < map[0].length; j++)
				System.out.print(map[i][j] + " ");
			System.out.println();
		}
		System.out.println();
	}
}
class Dot {
	int x;
	int y;
	int val;
	public Dot(int x, int y, int val) {
		// TODO Auto-generated constructor stub
		this.x = x;
		this.y = y;
		this.val = val;
	}
}
```

2015年12月9日17:48:24
