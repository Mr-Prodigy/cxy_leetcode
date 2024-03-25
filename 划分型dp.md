# 划分型dp



> 这类题较难，模版见分割数组的最大值。需要仔细思考转移方程，下标也很容易搞错

[toc]

## 分割数组的最大值

[410. 分割数组的最大值](https://leetcode.cn/problems/split-array-largest-sum/)

这个做法可以看成模版

```python
class Solution:
    def splitArray(self, nums: List[int], k: int) -> int:
        # prefix?
        n = len(nums)
        prefix = [0] * (n+1)
        # prefix[i] - prefix[j] 是nums[j] + ... + nums[i-1]
        for i, num in enumerate(nums):
            prefix[i+1] = prefix[i] + num

        # dp[i][j]代表把前j个元素(nums[0]到nums[j-1])分成i个
        dp = [[inf] * (n + 1) for _ in range(1 + k)]
        dp[0][0] = 0
        for i in range(1, k+1):
            # 前面有i-1个划分，至少要i-1个元素
            # 后面有k-i个划分，至少要k-i个元素
            for j in range(i, n - k + i + 1):
                for t in range(i-1, j):
                    dp[i][j] = min(dp[i][j], max(dp[i-1][t], prefix[j] - prefix[t]))
        
        return dp[-1][-1]
```

最小最大值问题也要想到二分答案：

```Python
class Solution:
    def splitArray(self, nums: List[int], k: int) -> int:
        n = len(nums)
        prefix = [0] * (n+1)
        # prefix[i] - prefix[j] 是nums[j] + ... + nums[i-1]
        # prefix[k] - prefix[i] 是nums[i] + ... + nums[k-1]
        for i, num in enumerate(nums):
            prefix[i+1] = prefix[i] + num
         
        def check(limit):
            count = 0
            idx = 0
            while idx < n:
                # 每一组的和都要小于limit
                start = idx
                idx += 1
                while idx < n and prefix[idx+1] - prefix[start] <= limit:
                    idx += 1
                count += 1
                if count > k:
                    return False    
            return True
        
        left, right = max(nums), sum(nums)
        while left <= right:
            mid = (left + right) >> 1
            if check(mid):
                right = mid - 1
            else:
                left = mid + 1
        return right + 1

```





## 最大平均值和的分组

[813. 最大平均值和的分组](https://leetcode.cn/problems/largest-sum-of-averages/)

```Python
class Solution:
    def largestSumOfAverages(self, nums: List[int], k: int) -> float:
        n = len(nums)
        prefix = [0] * (n+1)
        for i, num in enumerate(nums):
            prefix[i+1] = prefix[i] + num
        # dp[i][j]代表前j个分为i个
        dp = [[-inf] * (n+1) for _ in range(k+1)]
        dp[0][0] = 0
        for i in range(1, k+1):
            # dp[i][i] = prefix[i]
            # 前面有i-1个，后面有k-i个，要给前面和后面留足空间
            for j in range(i, n - k + i + 1):
                for t in range(i-1, j):
                    dp[i][j] = max(dp[i][j], dp[i-1][t] + (prefix[j] - prefix[t]) / (j-t))
        return dp[-1][-1]

```





## 工作计划的最低难度

[1335. 工作计划的最低难度](https://leetcode.cn/problems/minimum-difficulty-of-a-job-schedule/)

这题难点在难以想到划分型dp

```C++
class Solution {
public:
    int minDifficulty(vector<int>& jobDifficulty, int d) {
        int n = jobDifficulty.size();
        if (n < d) return -1;
        
        long long dp[d+1][n+1];
        memset(dp, 0x3F, sizeof(dp));
        dp[0][0] = 0;
        for (auto i = 1; i < d+1; ++i) {
            // 前面有i-1个，后面有d-i个，留位置
            for (auto j = i; j < n - d + i + 1; ++j) {
                int mx = jobDifficulty[j-1];
                for (auto k = j - 1; k >= i - 1; --k) {
                    mx = max(mx, jobDifficulty[k]);
                    dp[i][j] = min(dp[i][j], dp[i-1][k] + mx);
                }
            }
        }
        return dp[d][n];
    }
};
```

