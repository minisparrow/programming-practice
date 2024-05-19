# 编程修炼

## 双指针

### 11. 盛水最多的容器

题目： 

[leetcode11](https://leetcode.cn/problems/container-with-most-water/description/?envType=study-plan-v2&envId=leetcode-75)

```
给定一个长度为 n 的整数数组 height 。有 n 条垂线，第 i 条线的两个端点是 (i, 0) 和 (i, height[i]) 。

找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

返回容器可以储存的最大水量。

说明：你不能倾斜容器。

```
![Screenshot 2024-05-18 at 19.35.41](media/17160319560093/Screenshot%202024-05-18%20at%2019.35.41.png)

解题思路：

> 使用双指针， 关键是左右指针如何移动
> 如果左边高度小于最右高度， 则左指针向右移动（去找更大的边）；
> 否则右指针向左移动
```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int i = 0; 
        int j = height.size() - 1; 
        int area_max = 0; 
        int cur_area;
        //双指针方法
        while(i < j) {
            cur_area = min(height[i], height[j]) * (j - i);
            if (cur_area > area_max) {
                area_max = cur_area;
            }
            
            // this is the key point, how to move the two pointers:
            // move the shorter pointer in order to find big area
            if (height[i] < height[j]) i++; 
            else j--;
        }

        return area_max; 

    }
};
```

### 1679. k和数对的最大数目

题目：
[1679](https://leetcode.cn/problems/max-number-of-k-sum-pairs/description/?envType=study-plan-v2&envId=leetcode-75)

```
给你一个整数数组 nums 和一个整数 k 。

每一步操作中，你需要从数组中选出和为 k 的两个整数，并将它们移出数组。

返回你可以对数组执行的最大操作数。

示例 1：

输入：nums = [1,2,3,4], k = 5
输出：2
解释：开始时 nums = [1,2,3,4]：
- 移出 1 和 4 ，之后 nums = [2,3]
- 移出 2 和 3 ，之后 nums = []
不再有和为 5 的数对，因此最多执行 2 次操作。
示例 2：

输入：nums = [3,1,3,4,3], k = 6
输出：1
解释：开始时 nums = [3,1,3,4,3]：
- 移出前两个 3 ，之后nums = [1,4,3]
不再有和为 6 的数对，因此最多执行 1 次操作。

```

解体思路：
> 1. 先排序 sort(nums.begin(), nums.end())
> 2. 双指针初始化， 一个指向最小， 一个指向最大值
> 3. 如何移动指针， 如果两指针指向的值的和比k小， 左指针向右移动
>    如果和比k大， 右指针向左移动；
> 如果等于k, 次数加1， 两个指针均向靠拢一步

```
class Solution {
public:
    int maxOperations(vector<int>& nums, int k) {
       int left = 0, right = nums.size() - 1; 
       sort(nums.begin(), nums.end()); // 排序o(n * logn)
       int sum, counter = 0;
       while(left < right) {
            if(nums[left] + nums[right] == k) {
                left++;
                right--;
                counter++;
            } 
            else if(nums[left] + nums[right] < k){
                left++;
            }
            else {
                right--;
            }
       }
       return counter;
    }
};
```
## 图

### 1926. 迷宫中离入口最近的出口

题目

[1926](https://leetcode.cn/problems/nearest-exit-from-entrance-in-maze/description/?envType=study-plan-v2&envId=leetcode-75)
```
给你一个 m x n 的迷宫矩阵 maze （下标从 0 开始），矩阵中有空格子（用 '.' 表示）和墙（用 '+' 表示）。同时给你迷宫的入口 entrance ，用 entrance = [entrancerow, entrancecol] 表示你一开始所在格子的行和列。

每一步操作，你可以往 上，下，左 或者 右 移动一个格子。你不能进入墙所在的格子，你也不能离开迷宫。你的目标是找到离 entrance 最近 的出口。出口 的含义是 maze 边界 上的 空格子。entrance 格子 不算 出口。

请你返回从 entrance 到最近出口的最短路径的 步数 ，如果不存在这样的路径，请你返回 -1 。


```
![Screenshot 2024-05-18 at 20.22.26](media/17160319560093/Screenshot%202024-05-18%20at%2020.22.26.png)

解体思路：
> 广度优先搜索
> 当前位置在哪里 -> 当前位置入队 （创建队列）
> 以当前位置为起点， 上下左右一圈向外移动 （上下左右通过两个数组dx, dy来规定它的方向）
> 如果没有访问过， 且不是边界/出口，当前位置入队， 并标记当前位置为已经访问过(visited)
> 如果访问过，则跳过
> 
```
class Solution {
public:
    int nearestExit(vector<vector<char>>& maze, vector<int>& entrance) {
        //3:33 
        int dx[4] = {0,  0, -1, 1}; 
        int dy[4] = {-1, 1, 0,  0};  // up, down, left, right

        //get maze size
        int height = maze.size(); 
        int width = maze[0].size();

        //result 
        int step = 0; 
        //queue 
        queue<pair<int, int>> curPosition;


        //initial
        curPosition.push({entrance[0], entrance[1]});

        bool visited[height][width];
        memset(visited, 0, sizeof(visited));
        visited[entrance[0]][entrance[1]] = true;

        while(!curPosition.empty()) 
        {
             step++; 

             int sz = curPosition.size(); 
             for (int j = 0; j < sz; ++j)
             {
                auto [y, x] = curPosition.front(); 
                curPosition.pop(); 
                for (int i = 0; i < 4; ++i) 
                {
                    //new position
                    int nx = x + dx[i];
                    int ny = y + dy[i];
                    if (nx >=0 && nx < width && ny >=0 && ny < height && !visited[ny][nx] && maze[ny][nx] == '.')  
                    {
                        if (nx == 0 || nx == width - 1 || ny == 0 || ny == height - 1) 
                        return step;
                        curPosition.push({ny, nx}); // for next step tranverse
                        visited[ny][nx] = true;
                    }
                }

             }
             
        }

        return -1;    
    }
};
```


### 994. 腐烂的橘子

题目：
```
在给定的 m x n 网格 grid 中，每个单元格可以有以下三个值之一：

值 0 代表空单元格；
值 1 代表新鲜橘子；
值 2 代表腐烂的橘子。
每分钟，腐烂的橘子 周围 4 个方向上相邻 的新鲜橘子都会腐烂。

返回 直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 -1 。

```
![Screenshot 2024-05-18 at 20.38.39](media/17160319560093/Screenshot%202024-05-18%20at%2020.38.39.png)

解体思路：
> 广度优先搜索

代码：
```
class Solution {
//#define debug
public:
    int orangesRotting(vector<vector<int>>& grid) {
        //record fresh cnt 
        //record rotted coord
        //record result 
        int fresh_cnt = 0; //fresh_cnt >0  return -1 else return minute 
        int minute = 0; 
        int res; 
        int height = grid.size();
        int width = grid[0].size();
        
        queue<pair<int, int>> rot_queue;
        bool visited[height][width];
        memset(visited, false, sizeof(visited)); 

        //record fresh_cnt and initial rot_queue
        //记录新鲜水果的数量， 把腐烂的水果入队
        for (int row = 0; row < height; ++row) {
            for (int col = 0; col < width; ++col) {
                if(grid[row][col] == 2) {
                    rot_queue.push({row, col});
                    visited[row][col] = true;
                }
                if(grid[row][col] == 1) {
                    fresh_cnt += 1; 
                }
            }
        }
        if (fresh_cnt == 0) return 0;

        int dx[4] = {0, 0, -1, 1};
        int dy[4] = {-1, 1, 0, 0}; 
        while(!rot_queue.empty()) {
            minute += 1;
            int level_size = rot_queue.size(); 
            #ifdef debug
            cout << "time: " << minute << " rot queue size: "<< level_size << endl;
            #endif
            for (int j = 0; j < level_size; j++) {
                auto [row, col] = rot_queue.front();
                rot_queue.pop();

                for (int i = 0; i < 4; i++) {
                    int nx = col + dx[i]; 
                    int ny = row + dy[i]; 
                    if(nx >=0 && nx < width && ny >=0 && ny < height && grid[ny][nx] != 0)
                    {
                        if (visited[ny][nx] == true) continue;
                        rot_queue.push({ny, nx});
                        visited[ny][nx] = true;
                        fresh_cnt -= 1;
                        #ifdef debug
                        cout << "current rot pos: " << ny <<"," << nx << endl;
                        cout << "curent fresh cnt: " <<fresh_cnt <<endl;  
                        #endif
                        if (fresh_cnt == 0) return minute;
                    }
                }

            }
            
        }
        res = (fresh_cnt != 0) ? -1 : minute;
        return res; 
    }
};
```
