# 数位dp

> leetcode上对这类题普遍标困难，难度在1950到2200之间。但这类题有模板，掌握模版后不难。
>
> 需要注意的是，is_num选项可能会不需要。is_num不需要的情况是：此位不填可以算成此位填0（例如，9是否可看成009）。



## 1. 最大和为N的数字组合

[902. 最大为 N 的数字组合](https://leetcode.cn/problems/numbers-at-most-n-given-digit-set/)

这题需要is_num，因为此位不填不可以算成此位填0。

```python
class Solution:
    def atMostNGivenDigitSet(self, digits: List[str], n: int) -> int:x
        digits = [int(d) for d in digits]
        s = str(n)
    
        @cache
        def dfs(i, is_limit, is_num):
            if i == len(s):
                return 1
            res = 0
            if not is_num:
                res += dfs(i+1, False, False)
            up = 9 if not is_limit else int(s[i])
            low = 0 if is_num else 1
            for d in digits:
                if d > up: break
                if d < low: continue
                res += dfs(i+1, is_limit and d == up, True)
            return res
        
        return dfs(0, True, False) - 1 # 去掉0
```

C++记忆化搜索的原理见[灵神解答](https://leetcode.cn/problems/numbers-at-most-n-given-digit-set/solutions/1900101/shu-wei-dp-tong-yong-mo-ban-xiang-xi-zhu-e5dg/)

```C++
class Solution {
public:
    int atMostNGivenDigitSet(vector<string>& digits, int n) {

        auto s = to_string(n);
        int m = s.size(), dp[m];
        memset(dp, -1, sizeof(dp));
        function<int(int, bool, bool)> f = [&](int i, bool isLimit, bool isNum) -> int {
            if (i == m) return 1;
            if (!isLimit && isNum && dp[i] >= 0) return dp[i];
            int res = 0;
            if (!isNum) res += f(i+1, false, false);
            int up = isLimit ? s[i] - '0' : 9;
            int low = isNum? 0 : 1;
            for (auto d_char: digits) {
                int d = d_char[0] - '0';
                if (d > up) break;
                if (d < low) continue;
                res += f(i+1, isLimit and d == up, true);
            }
            if (!isLimit && isNum) dp[i] = res;
            return res;
        };
        return f(0, true, false) - 1;
    }
};
```



## 2. 数字 1 的个数

[233. 数字 1 的个数](https://leetcode.cn/problems/number-of-digit-one/)，相同的题目还有 [面试题 17.06. 2出现的次数](https://leetcode.cn/problems/number-of-2s-in-range-lcci/)

这题无需is_num。 但需要加个参数count，记录此种情况中1的个数。

```python
class Solution:
    def countDigitOne(self, n: int) -> int:

        s = str(n)

        # mask 代表其中1的个数
        @cache
        def f(i, count, isLimit):
            if i == len(s):
                return count
            res = 0
            up = 9 if not isLimit else int(s[i])
            for d in range(up + 1):
                if d == 1:
                    res += f(i+1, count+1, isLimit and d == up)
                else:
                    res += f(i+1, count, isLimit and d == up)
            return res
        
        return f(0, 0, True)
```



```c++
class Solution {
public:
    int countDigitOne(int n) {
        auto s = to_string(n);
        int m = s.length(), dp[m][m];
        memset(dp, -1, sizeof(dp));
        function<int(int, int, bool)> f = [&](int i, int count, bool isLimit) -> int{
            if (i == m) return count;
            if (!isLimit && dp[i][count] >= 0) return dp[i][count];
            int res = 0;
            int up = isLimit ? s[i] - '0' : 9;
            for (auto d = 0; d <= up; ++d) {
                if (d == 1)
                    res += f(i+1, count+1, isLimit and d == up);
                else
                    res += f(i+1, count, isLimit and d == up);
            }
            if (!isLimit) dp[i][count] = res;
            return res;
        };
        return f(0, 0, true);
    }
};
```



## 3. 范围内的数字计数

[1067. 范围内的数字计数](https://leetcode.cn/problems/digit-count-in-range/)

d为1～9时，都可以用第三题的方法计算。

d为0时，需要is_num来特殊判断。

所以这题用了一种更通用的方法来计算。 同理，上面那题也可以用这种方式计算。

```python
class Solution:
    def digitsCount(self, d: int, low: int, high: int) -> int:
        # 为什么这题有is_num这个参数
        # 因为当d为0时，前导0会影响结果
        # dfs(i+1, 0, False, False, target)这种情况无法写在下面的循环里
        @cache
        def dfs(i, count, is_num, is_limit, target):
            s = str(target)
            if i == len(s): return count
            up = 9 if not is_limit else int(s[i])
            res = 0
            if not is_num:
                res += dfs(i+1, 0, False, False, target)
            low = 1 if not is_num else 0
            for t in range(low, up+1):
                if t == d:
                    res += dfs(i+1, count+1, True, is_limit and t == up, target)
                else:
                    res += dfs(i+1, count, True, is_limit and t == up, target)
            return res
        
        return dfs(0, 0, False, True, high) - dfs(0, 0, False, True, low - 1)
```



## 4. 不含连续1的非负整数

[600. 不含连续1的非负整数](https://leetcode.cn/problems/non-negative-integers-without-consecutive-ones/)

这题全转成2进制来算，不需要is_num，但加上了is_one这个参数来记录上位是否是1。

```python
class Solution:
    def findIntegers(self, n: int) -> int:
        s = bin(n)[2:]
        
        @cache
        def dfs(i, is_one, is_limit):
            if i == len(s): return 1
            up = 1 if not is_limit else int(s[i])
            res = 0
            for d in range(up+1):
                if is_one and d == 1:
                    continue
                if d == 1:
                    res += dfs(i+1, True, is_limit and d == up)
                else:
                    res += dfs(i+1, False, is_limit and d == up)
            return res

        return dfs(0, False, True)
```



## 5. 统计特殊整数

[2376. 统计特殊整数](https://leetcode.cn/problems/count-special-integers/)

这题需要is_num。

```python
class Solution:
    def countSpecialNumbers(self, n: int) -> int:
        # https://leetcode.cn/problems/count-special-integers/solutions/1746956/shu-wei-dp-mo-ban-by-endlesscheng-xtgx/
        s = str(n)

        @cache
        def f(i, mask, is_limit, is_num):
            if i == len(s):
                return int(is_num)
            res = 0
            if not is_num: # 跳过当前数位
            # 当前位跳过了，证明前面也都跳过了
                res += f(i + 1, 0, False, False)
            # 如果前面没有填数字，必须从 1 开始（因为不能有前导零）
            low = 0 if is_num else 1
            # 如果前面填的数字都和 n 的一样，那么这一位至多填 s[i]（否则就超过 n 啦）
            up = int(s[i]) if is_limit else 9
            for d in range(low, up + 1):
                if (mask >> d & 1 == 0):
                    # d 不在 mask 中
                    res += f(i + 1, mask | (1 << d), is_limit and d == up, True)
            return res
          
        return f(0, 0, True, False)
```

实际上，如果mask为0，不就代表前面数字都没填，不就是is_num为True的情况。 因此可改写为下面：

```python
class Solution:
    def countSpecialNumbers(self, n: int) -> int:
        # https://leetcode.cn/problems/count-special-integers/solutions/1746956/shu-wei-dp-mo-ban-by-endlesscheng-xtgx/
        s = str(n)

        @cache
        def f(i, mask, is_limit):
            if i == len(s):
                return bool(mask)
            res = 0
            up = int(s[i]) if is_limit else 9
            for d in range(up + 1):
                if not mask and d == 0:
                  # 前面未填，并且此位也不填
                    res += f(i+1, 0, False)
                    continue
                if (mask >> d & 1 == 0):
                    # d 不在 mask 中
                    res += f(i + 1, mask | (1 << d), is_limit and d == up)
            return res
        return f(0, 0, True)
```



## 6. 统计强大整数的数目

[2999. 统计强大整数的数目](https://leetcode.cn/problems/count-the-number-of-powerful-integers/)

这题有点难度，周赛时没想出来

```python
class Solution:
    def numberOfPowerfulInt(self, start: int, finish: int, limit: int, s: str) -> int:
        @cache
        def dfs(i, is_limit, target):
            if target < int(s):
                return 0
            if i == len(str(target)):
                return 1
            t = str(target)
            diff = len(t) - len(s)  # 前面可以不同的位数
            ceil = int(t[i]) if is_limit else 9
            res = 0
            # 如果还没到后缀，可以随意填
            if i < diff:
                for d in range(min(ceil, limit) + 1):
                    res += dfs(i+1, is_limit and d == ceil, target)
            else: # 已经到了后缀，此位只能填对应的后缀位
                x = int(s[i - diff])
                if x <= min(limit, ceil):
                    res = dfs(i+1, is_limit and x == ceil, target)
            return res
        
        return dfs(0, True, finish) - dfs(0, True, start - 1)
```

