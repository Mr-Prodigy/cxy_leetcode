# 区间dp

> 见[灵神视频](https://www.bilibili.com/video/BV1Gs4y1E7EU/?spm_id_from=333.999.top_right_bar_window_history.content.click&vd_source=217bacbee37820b5bf3ed2f4fb8f6c94%E4%BD%9C%E8%80%85%EF%BC%9ALucky_boy%E9%93%BE%E6%8E%A5%EF%BC%9Ahttps%3A%2F%2Fleetcode.cn%2Fproblems%2Fminimum-cost-to-cut-a-stick%2F%E6%9D%A5%E6%BA%90%EF%BC%9A%E5%8A%9B%E6%89%A3%EF%BC%88LeetCode%EF%BC%89%E8%91%97%E4%BD%9C%E6%9D%83%E5%BD%92%E4%BD%9C%E8%80%85%E6%89%80%E6%9C%89%E3%80%82%E5%95%86%E4%B8%9A%E8%BD%AC%E8%BD%BD%E8%AF%B7%E8%81%94%E7%B3%BB%E4%BD%9C%E8%80%85%E8%8E%B7%E5%BE%97%E6%8E%88%E6%9D%83%EF%BC%8C%E9%9D%9E%E5%95%86%E4%B8%9A%E8%BD%AC%E8%BD%BD%E8%AF%B7%E6%B3%A8%E6%98%8E%E5%87%BA%E5%A4%84%E3%80%82)、[oi-wiki 区间dp](https://oi-wiki.org/dp/interval/)



[toc]

## 1. 最长回文子序列

[516. 最长回文子序列](https://leetcode.cn/problems/longest-palindromic-subsequence/description/)

DFS + 记忆化搜索

```c++
class Solution {
    vector<vector<int>> memo;
    int helper(string &s, int i, int j) {
        if (i > j) return 0;
        if (i == j) return 1;
        if (memo[i][j] != -1) return memo[i][j];
        if (s[i] == s[j]) 
            memo[i][j] = helper(s, i+1, j-1) + 2;
        else 
            memo[i][j] = max(helper(s, i+1, j), helper(s, i, j-1));
       
        return memo[i][j];
    }
public:
    int longestPalindromeSubseq(string s) {

        int n = s.size();
        memo = vector<vector<int>> (n, vector<int>(n, -1));
        return helper(s, 0, n-1);
    }
};

//————————————————————————————————————灵神写法学习————————————————————————————————————————————————————
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        int n = s.length(), memo[n][n];
        memset(memo, -1, sizeof(memo)); // -1 表示还没有计算过
        function<int(int, int)> dfs = [&](int i, int j) -> int {
            if (i > j) return 0; // 空串
            if (i == j) return 1; // 只有一个字母
            int &res = memo[i][j];
            if (res != -1) return res;
            if (s[i] == s[j]) return res = dfs(i + 1, j - 1) + 2; // 都选
            return res = max(dfs(i + 1, j), dfs(i, j - 1)); // 枚举哪个不选
        };
        return dfs(0, n - 1);
    }
};
```

区间dp

```c++
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        // dp[i][j]代表s[i:j+1]的最长回文子串
        int n = s.size();
        vector<vector<int>> dp(n, vector<int>(n, 0));
        for (auto i = n-1; i >= 0; --i) {
            dp[i][i] = 1;
            for (auto j = i+1; j < n; ++j) {
                if (s[i] == s[j]) 
                    dp[i][j] = dp[i+1][j-1] + 2;
                else 
                    dp[i][j] = max(dp[i+1][j], dp[i][j-1]);
            }
        }
        return dp[0][n-1];
    }
};
```



## 2. 切棍子的最小成本

[1547. 切棍子的最小成本](https://leetcode.cn/problems/minimum-cost-to-cut-a-stick/description/)

这题有点难理解，以后继续学习

```c++
class Solution {
public:
    int minCost(int n, vector<int>& cuts) {
        // 区间dp
        // dp[i][j]表示当前待切割的木棍左端点为cuts[i-1], cuts[j+1]
        // dp[i][j] = max(dp[i][j], dp[i][k] + dp[k][j] + cost)
        cuts.push_back(0);
        cuts.push_back(n);
        sort(cuts.begin(), cuts.end());
        int m = cuts.size();
        vector<vector<int>> dp(m, vector<int>(m, INT_MAX));
        
        for (auto i = m-1; i >= 0; --i) {
            dp[i][i] = 0;
            for (auto j = i+1; j < m; ++j) {
                if (j == i+1) {
                    dp[i][j] = 0;
                    continue;
                }
                for (auto k = i+1; k < j; ++k) {
                    dp[i][j] = min(dp[i][j], dp[i][k] + dp[k][j]);
                }
                dp[i][j] += cuts[j] - cuts[i];
            }
        }
        return dp[0][m-1];
    }
};
```



## 3. 多边形三角剖分的最低得分

[1039. 多边形三角剖分的最低得分](https://leetcode.cn/problems/minimum-score-triangulation-of-polygon/description/)

> 解答见[灵神视频](https://www.bilibili.com/video/BV1Gs4y1E7EU/?spm_id_from=333.999.top_right_bar_window_history.content.click&vd_source=217bacbee37820b5bf3ed2f4fb8f6c94%E4%BD%9C%E8%80%85%EF%BC%9ALucky_boy%E9%93%BE%E6%8E%A5%EF%BC%9Ahttps%3A%2F%2Fleetcode.cn%2Fproblems%2Fminimum-cost-to-cut-a-stick%2F%E6%9D%A5%E6%BA%90%EF%BC%9A%E5%8A%9B%E6%89%A3%EF%BC%88LeetCode%EF%BC%89%E8%91%97%E4%BD%9C%E6%9D%83%E5%BD%92%E4%BD%9C%E8%80%85%E6%89%80%E6%9C%89%E3%80%82%E5%95%86%E4%B8%9A%E8%BD%AC%E8%BD%BD%E8%AF%B7%E8%81%94%E7%B3%BB%E4%BD%9C%E8%80%85%E8%8E%B7%E5%BE%97%E6%8E%88%E6%9D%83%EF%BC%8C%E9%9D%9E%E5%95%86%E4%B8%9A%E8%BD%AC%E8%BD%BD%E8%AF%B7%E6%B3%A8%E6%98%8E%E5%87%BA%E5%A4%84%E3%80%82)

这题难点在于如何处理这个实际问题并思考出dp方程。

```C++
class Solution {
public:
    int minScoreTriangulation(vector<int>& values) {

        int n = values.size();
        int dp[n][n]; // 不赋值会怎么样
        // dp[i][j]表示从i到j的最小值，包括i，j，那么此题答案为dp[0][n-1]
        // dp[i][j] = min(dp[i][j], dp[i][k]+dp[k][j])
        
        for (auto i = n-1; i >= 0; i--) {
            for (auto j = i+1; j < n; j++) {
                dp[i][j] = j == i+1 ? 0 : INT_MAX;
                for (auto k = i+1; k < j; k++) {
                    dp[i][j] = min(dp[i][j], dp[i][k]+dp[k][j]+values[i]*values[j]*values[k]);
                }
            }
        }
        return dp[0][n-1];
    }
};
```



## 删除回文子数组

[1246. 删除回文子数组](https://leetcode.cn/problems/palindrome-removal/)

```python
class Solution:
    def minimumMoves(self, arr: List[int]) -> int:
        # https://leetcode.cn/problems/palindrome-removal/solutions/1155079/biao-zhun-qu-jian-dpyu-chu-li-shi-shi-ji-9ey0/
        n = len(arr)
        dp = [[n] * n for _ in range(n)]
        g = [None] * n 
        for i, x in enumerate(arr):
            dp[i][i] = 1
            # 预处理找到 [i, n) 范围内所有与 a[i] 相等的值的下标，减少无效遍历
            g[i] = [j for j in range(i, n) if x == arr[j]]

        for i in range(n - 2, -1, -1):
            for j in range(i+1, n):  s
                if i == j - 1:
                    dp[i][j] = int(arr[i] != arr[j]) + 1
                else:
                    if arr[i] == arr[j]:
                        dp[i][j] = dp[i+1][j-1] # 无论怎么取，都能把这个带进去
                    for k in g[i]:
                        if k >= j:
                            break
                        dp[i][j] = min(dp[i][j], dp[i][k] + dp[k+1][j])
        
        return dp[0][n-1]
```

