title: Google变种面试题：离所有健身器材最近的位置
date: 2015-12-10 10:52:55
tags: 面试
---
之前做的那个是哪个点到所有的器材最近。现在则是改成，获得某个点到某个健身器材最近的距离。有意思~这个题目其实就是我做的上一个题目的bug啊。

上个题目：用N个二维的点阵来计算每个点到每一个器材的距离加和。
这个题目：用一个二维点阵来储存每一个点到最贱器材的距离。

更新的方式是一样的bfs，如果相邻的点比当前点的值大1以上，更新相邻点为当前点的值+1。

代码奉上，只是一点点修改。

``` bash
public class MinDistanceToGymChange {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		MinDistanceToGymChange md=new MinDistanceToGymChange();
		
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
		
		md.getBestPos(gym);
	}

	int[][] getBestPos(char[][] gym) {
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
		for (int i = 0; i < equipment.size(); i++) {
			dealGymPoint(map, equipment.get(i).x, equipment.get(i).y);
		}

		printMap(map);

		return map;
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

```

2015年12月10日10:58:06