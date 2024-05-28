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
![Screenshot 2024-05-22 at 08.09.07](media/17160319560093/Screenshot%202024-05-22%20at%2008.09.07.png)



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

## 堆/优先队列

### 215. 数组中的第K个最大元素

题目

[215](https://leetcode.cn/problems/kth-largest-element-in-an-array/description/?envType=study-plan-v2&envId=leetcode-75)

```
给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。

请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

你必须设计并实现时间复杂度为 O(n) 的算法解决此问题。

 

示例 1:

输入: [3,2,1,5,6,4], k = 2
输出: 5
示例 2:

输入: [3,2,3,1,2,4,5,5,6], k = 4
输出: 4

```

解体思路：
>        1. 建立大小为k的小顶堆
>        2. 往堆里继续添加数字， 
>         - a. 如果该数字小于堆顶， 不要管它
>         - b. 如果该数字大于堆顶， 把它压入堆, 把堆顶元素弹出
>         - c. 则堆里的数字中， 堆顶以下的元素都比堆顶大， 堆顶就是第k个最大的元素


代码：

```
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        //建立大小为k的小顶堆
        //往堆里继续添加数字， 
        // - 1. 如果该数字小于堆顶， 不要管它
        // - 2. 如果该数字大于堆顶， 把它压入堆, 把堆顶元素弹出
        // - 3. 则堆里的数字中， 堆顶一下的元素都比堆顶大， 堆顶就是第k个最大的元素
        std::priority_queue<int, vector<int>, std::greater<int> > smallQueue;
        for (int i = 0; i < k; ++i) {
            smallQueue.push(nums[i]);
        }
        for (int j = k; j < nums.size(); ++j) {
            if (nums[j] > smallQueue.top()) {
                smallQueue.pop(); 
                smallQueue.push(nums[j]);
            }
        }

        return smallQueue.top();
        
    }
};
```


### 2542. 最大子序列的分数

题目
[2542](https://leetcode.cn/problems/maximum-subsequence-score/description/?envType=study-plan-v2&envId=leetcode-75)


```
给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，两者长度都是 n ，再给你一个正整数 k 。你必须从 nums1 中选一个长度为 k 的 子序列 对应的下标。

对于选择的下标 i0 ，i1 ，...， ik - 1 ，你的 分数 定义如下：

nums1 中下标对应元素求和，乘以 nums2 中下标对应元素的 最小值 。
用公式表示： (nums1[i0] + nums1[i1] +...+ nums1[ik - 1]) * min(nums2[i0] , nums2[i1], ... ,nums2[ik - 1]) 。
请你返回 最大 可能的分数。

一个数组的 子序列 下标是集合 {0, 1, ..., n-1} 中删除若干元素得到的剩余集合，也可以不删除任何元素。

 

示例 1：

输入：nums1 = [1,3,3,2], nums2 = [2,1,3,4], k = 3
输出：12
解释：
四个可能的子序列分数为：
- 选择下标 0 ，1 和 2 ，得到分数 (1+3+3) * min(2,1,3) = 7 。
- 选择下标 0 ，1 和 3 ，得到分数 (1+3+2) * min(2,1,4) = 6 。
- 选择下标 0 ，2 和 3 ，得到分数 (1+3+2) * min(2,3,4) = 12 。
- 选择下标 1 ，2 和 3 ，得到分数 (3+3+2) * min(1,3,4) = 8 。
所以最大分数为 12 。
示例 2：

输入：nums1 = [4,2,3,1,1], nums2 = [7,5,10,9,6], k = 1
输出：30
解释：
选择下标 2 最优：nums1[2] * nums2[2] = 3 * 10 = 30 是最大可能分数。

```

解体思路

> 1. num2 从大到小排序
> 2. num1维护一个size为k的最小堆
> 3. 当最小堆size > k时，把堆顶（最小元素）弹出
> 4. 当最小堆size = k时，计算sum1 * num2的值，此时的sum1是k个元素的最大值的和（这一点有点费解）
> 5. 更新最大分数，并与最大分数比较
> 
代码

```cpp
class Solution {
public:
    long long maxScore(vector<int>& nums1, vector<int>& nums2, int k) {
           int n = nums1.size();
            // 将索引按照 nums2 的值从大到小排序
            vector<int> indices(n);
            iota(indices.begin(), indices.end(), 0);
            sort(indices.begin(), indices.end(), [&](int a, int b) {
                return nums2[a] > nums2[b];
            });

            // 优先队列（最小堆）
            priority_queue<int, vector<int>, greater<int>> min_heap;
            long long sum_nums1 = 0;
            long long max_score = 0;

            // 遍历排序后的索引
            for (int i : indices) {
                int min_value = nums2[i];
                min_heap.push(nums1[i]);
                sum_nums1 += nums1[i];

                // 保持堆的大小为 k
                if (min_heap.size() > k) {
                    sum_nums1 -= min_heap.top();
                    min_heap.pop();
                }

                // 当堆的大小为 k 时，计算分数
                if (min_heap.size() == k) {
                    max_score = max(max_score, sum_nums1 * min_value);
                }
            }

            return max_score;
            }
};
```

### 2462. 雇佣 K 位工人的总代价

题目：
[2462](https://leetcode.cn/problems/total-cost-to-hire-k-workers/description/?envType=study-plan-v2&envId=leetcode-75)

```
给你一个下标从 0 开始的整数数组 costs ，其中 costs[i] 是雇佣第 i 位工人的代价。

同时给你两个整数 k 和 candidates 。我们想根据以下规则恰好雇佣 k 位工人：

总共进行 k 轮雇佣，且每一轮恰好雇佣一位工人。
在每一轮雇佣中，从最前面 candidates 和最后面 candidates 人中选出代价最小的一位工人，如果有多位代价相同且最小的工人，选择下标更小的一位工人。
比方说，costs = [3,2,7,7,1,2] 且 candidates = 2 ，第一轮雇佣中，我们选择第 4 位工人，因为他的代价最小 [3,2,7,7,1,2] 。
第二轮雇佣，我们选择第 1 位工人，因为他们的代价与第 4 位工人一样都是最小代价，而且下标更小，[3,2,7,7,2] 。注意每一轮雇佣后，剩余工人的下标可能会发生变化。
如果剩余员工数目不足 candidates 人，那么下一轮雇佣他们中代价最小的一人，如果有多位代价相同且最小的工人，选择下标更小的一位工人。
一位工人只能被选择一次。
返回雇佣恰好 k 位工人的总代价。

 

示例 1：

输入：costs = [17,12,10,2,7,2,11,20,8], k = 3, candidates = 4
输出：11
解释：我们总共雇佣 3 位工人。总代价一开始为 0 。
- 第一轮雇佣，我们从 [17,12,10,2,7,2,11,20,8] 中选择。最小代价是 2 ，有两位工人，我们选择下标更小的一位工人，即第 3 位工人。总代价是 0 + 2 = 2 。
- 第二轮雇佣，我们从 [17,12,10,7,2,11,20,8] 中选择。最小代价是 2 ，下标为 4 ，总代价是 2 + 2 = 4 。
- 第三轮雇佣，我们从 [17,12,10,7,11,20,8] 中选择，最小代价是 7 ，下标为 3 ，总代价是 4 + 7 = 11 。注意下标为 3 的工人同时在最前面和最后面 4 位工人中。
总雇佣代价是 11 。


示例 2：

输入：costs = [1,2,4,1], k = 3, candidates = 3
输出：4
解释：我们总共雇佣 3 位工人。总代价一开始为 0 。
- 第一轮雇佣，我们从 [1,2,4,1] 中选择。最小代价为 1 ，有两位工人，我们选择下标更小的一位工人，即第 0 位工人，总代价是 0 + 1 = 1 。注意，下标为 1 和 2 的工人同时在最前面和最后面 3 位工人中。
- 第二轮雇佣，我们从 [2,4,1] 中选择。最小代价为 1 ，下标为 2 ，总代价是 1 + 1 = 2 。
- 第三轮雇佣，少于 3 位工人，我们从剩余工人 [2,4] 中选择。最小代价是 2 ，下标为 0 。总代价为 2 + 2 = 4 。
总雇佣代价是 4 。
```

解体思路：

> 参考官网解体思路
> 链接：https://leetcode.cn/problems/total-cost-to-hire-k-workers/solutions/2758993/gu-yong-k-wei-gong-ren-de-zong-dai-jie-b-y4ju/
> 其实主要是两个步骤
> 1. 建立小顶堆： 
> - a. 如果left+1 > right, 说明前后的cadidates有交叉， 有重叠， 则所有的元素都入堆
> - b. 否则， 分别将前后cadidates入堆
> 2. k轮循环时， 更新小顶堆，更新left, right值，并及时计算cost值
> - a.  如果left+1 > right, 说明所有元素都在cadidates中， 只要从堆中顺序选出堆顶元素即可
> - b. 如果left+1 <= right, 则判断堆顶元素的index, 如果index <= left, 则更新left++, 并将left++对应的元素送入堆以便下轮循环进行选择，  如果index >=right, 则更新right--
> 
代码：
```
class Solution {
public:
    long long totalCost(vector<int>& costs, int k, int candidates) {
        int n = costs.size();
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> q;
        int left = candidates - 1, right = n - candidates;
        if (left + 1 < right) {
            for (int i = 0; i <= left; ++i) {
                q.emplace(costs[i], i);
            }
            for (int i = right; i < n; ++i) {
                q.emplace(costs[i], i);
            }
        }
        else {
            for (int i = 0; i < n; ++i) {
                q.emplace(costs[i], i);
            }
        }
        long long ans = 0;
        for (int _ = 0; _ < k; ++_) {
            auto [cost, id] = q.top();
            q.pop();
            ans += cost;
            if (left + 1 < right) {
                if (id <= left) {
                    ++left;
                    q.emplace(costs[left], left);
                }
                else {
                    --right;
                    q.emplace(costs[right], right);
                }
            }
        }
        return ans;
    }
};

```

## 单调栈

### 739. 每日温度
题目：
[739](https://leetcode.cn/problems/daily-temperatures/?envType=study-plan-v2&envId=leetcode-75)

```
给定一个整数数组 temperatures ，表示每天的温度，返回一个数组 answer ，其中 answer[i] 是指对于第 i 天，下一个更高温度出现在几天后。如果气温在这之后都不会升高，请在该位置用 0 来代替。

示例 1:

输入: temperatures = [73,74,75,71,69,72,76,73]
输出: [1,1,4,2,1,1,0,0]
示例 2:

输入: temperatures = [30,40,50,60]
输出: [1,1,1,0]
示例 3:

输入: temperatures = [30,60,90]
输出: [1,1,0]

```

解体思路：
>  单调栈： 维持栈内元素自底向顶部增长时为递减顺序， 一旦元素大于栈顶元素，就将栈破坏掉，一一将栈内元素弹出， 最后更新入新的元素
> - 遍历每天的温度： 
>        a. 如果栈不为空， 或者当前温度>栈顶温度： 
>            - 更新top_idx对应的值， 其等于当前温度的idx - top_idx
>            - 弹出栈顶元素
>        b. 否则： 栈内压入当前温度对应的idx
> 
pseudo code:
```
         for  idx, t  in  enumerate(temperatures):
             while stk and t > stk.top()
                  top_idx = stk.top()
                  ans[top_idx] = idx - top_idx 
             stk.push(t) // stack is empty or t < stack top, push stack
```

code:
```
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {  
        //stepbystep: 
        //step1:  idx: 0  stk is empty,            stk.push: [0]
        //step2:  idx: 1  74 > 73(stk.top),        ans[0] = 1 - 0 = 1, stk.pop -> empty  stk.push: [1]
        //step3:  idx: 2  75 > 74(stk.top),        ans[1] = 2 - 1 = 1, stk.pop -> empty  stk.push: [2]
        //step4:  idx: 3  71 < 75(stk.top),        stk.push(i): [2(75), 3(71)]
        //step5:  idx: 4  69 < 71(stk.top),        stk.push(i): [2(75), 3(71), 4(69)]
        //step6:  idx: 5. 72 > 69(stk.top),        ans[4] = 5 - 4 = 1, stk.pop -> [2(75), 3(71)]
        //                                         ans[3] = 5 - 3 = 2, stk.pop -> [2(75)]
        //                                         ans[2] = 5 - 2 = 3, stk.pop -> empty. stk.push: [5]
        //step7:  idx: 6. 76 > 72(stk.top),        ans[5] = 6 - 5 = 1, stk.pop -> empty. stk.push: [6(76]
        //step8:  idx: 7. 73 < 76(stk.top),        stk.push(7).  stk: [6(76), 7(73)]
        int n = temperatures.size(); 
        vector<int> ans(n, 0); 
        stack<int> stk;
        int stk_topidx;  
        for(int i = 0; i < n; ++i) {
            while(!stk.empty() && temperatures[i] > temperatures[stk.top()]) {
                stk_topidx = stk.top(); 
                ans[stk_topidx] = i - stk_topidx; 
                stk.pop();
            }
            stk.emplace(i); 
        }
        return ans;
    }
};
```
