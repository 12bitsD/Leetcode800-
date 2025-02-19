# day2C++

---

[toc]



## [209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

给定一个含有 `n` 个正整数的数组和一个正整数 `target` **。**找出该数组中满足其总和大于等于 `target` 的长度最小的 **子数组**`[numsl, numsl+1, ..., numsr-1, numsr]` ，并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。



解题思路：

1.穷举

两层循环

外层循环固定子数组起点，内层循环逐个累加元素。一旦和≥target，立刻记录当前子数组长度并跳出内循环，避免无效计算。由于每个起点都要遍历剩余所有元素，时间复杂度为O(n²)

```C++

    int minSubArrayLen(int target, vector<int>& nums) {
        int result = nums.size()+1;
        int sum;
        for(int i=0;i<nums.size();i++){
            sum =0;
            for(int j=i;j<nums.size();j++){
                sum +=nums[j];
                if(sum>=target){
                    int minLen = j-i+1;
                    result = result<minLen? result:minLen;
                    break;
                }
            }
            
        }
        return result==nums.size()+1?0:result;

    }
```



2.滑动窗口

用**快慢指针**动态维护窗口。快指针向右扩展窗口，累加元素和；当和≥target时，慢指针向右收缩窗口，同时更新最小长度。每个元素最多被访问两次，时间复杂度降为O(n)，效率更高

```C++
int minSubArrayLen(int target, vector<int>& nums) {        
        int fast=0,slow=0,sum=0,ans=nums.size()+1;
        for(fast;fast<nums.size();fast++){
            sum+=nums[fast];
            while(sum>=target){
                ans=min(ans,fast-slow+1);
                sum-=nums[slow++];
            }
        }
        return (ans==nums.size()+1)?0:ans;


    }
```

## [59. 螺旋矩阵 II](https://leetcode.cn/problems/spiral-matrix-ii/)

题目：

 给你一个正整数 `n` ，生成一个包含 `1` 到 `n2` 所有元素，且元素按顺时针顺序螺旋排列的 `n x n` 正方形矩阵 `matrix` 



### 解题思路V1：

​	首先 n*n的螺旋排列矩阵，可以将其拆解成一层一层的结构。观察规律：

​		用一个变量：num 判断该放入的数字是多少，每次放入后num++

​		每一层按照方向可被分解成4个，顶侧–>右侧—>下侧–>上侧。

​	为了方便理解，简单说明一下：每层的最后一个=n-i-1（无论哪个方向。）

>  每一层每个方向的最后一个一定是 n -该层数-1(我们这里是**n-i-1**需要记住这个数);

> 接下来的循环里将用i表示层数的循环，用j表示方向的第几个，如 i=3 ， j=2 就是第三层某个方向的第2个。

​	**每一层按顺序填入：**

​		顶：填入路径为：[ **i** ]\[ **j++** ] `（0，1）->(0,2)` 

​		右：填入路径为:[ **j++** ] [ **n-i-1** ] `(0,3)–>(1,3)`;  

​		下：填入路径为[**n-i-1**] \[ **n-(j++)-1** ] `(3,3)->(3,2)`  看到n-j-1 可能会有些疑惑，没事我简单讲一下你就明白了，用例子解释一下，数组的垂直方向 []\[第二个column]， 一开始应该是该层的最底部（n-j-1) ,然后逐步往左挪（j++）；

​		上： 填入路径为：[ **n-(j++)-1** ]\[ **i (第几层) **] `(3,0)->（2,0）`

​	**处理奇数中间点**：若 `n` 为奇数，矩阵中心单独填充最后一个数

---

### 解题思路V2：

​	**核心思路**：像剥洋葱一样**逐层填充**，每一层按顺时针走四条边，用变量 `num` 记录当前要填的数字。

​	**具体步骤**：

​	分层处理

​	 矩阵共有 `n/2` 层（比如 `n=4` 有2层，`n=3` 有1层+中心点）

- ​    **每层填四条边**（假设当前层为 `i`）：

  - **上边**：行固定为 `i`，列从左到右填满（列范围：`i` → `n-i-2`）

  - **右边**：列固定为 `n-i-1`，行从上到下填满（行范围：`i` → `n-i-2`）

  - **下边**：行固定为 `n-i-1`，列从右到左填满（列范围：`n-i-1` → `i+1`）

  - **左边**：列固定为 `i`，行从下到上填满（行范围：`n-i-1` → `i+1`）


​	举个栗子

​	（n=3）

-    **第0层**（即最外层）：

  - 上边：填 `(0,0) → (0,1)`

  - 右边：填 `(0,2) → (1,2)`

  - 下边：填 `(2,2) → (2,1)`

  - 左边：填


   

  ```
  (2,0) → (1,0)
  ```

  - ​	**中心点**：单独填 `(1,1)` 为最后一个数9


​	奇数特殊处理

- ​	当 `n` 为奇数时（如3），矩阵正中心单独填入最后一个数。

----



```C++
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> res(n, vector<int>(n,0));
        int num = 1;
        for(int i=0;i<n/2;i++){
            for(int j=i;j<n-i-1;j++) res[i][j] = num++;
            for(int j=i;j<n-i-1;j++) res[j][n-i-1] = num++;
            for(int j=i;j<n-i-1;j++) res[n-i-1][n-j-1] = num++;
            for(int j=i;j<n-i-1;j++)res[n-j-1][i] = num++;
        }
        if(n%2==1)
            res[n/2][n/2] = num;
        return res;
    }
};
```



 