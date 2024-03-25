# KMP



板子

[28. 找出字符串中第一个匹配项的下标](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/)

```Python
class Solution:
    def strStr(self, haystack: str, needle: str) -> int:
        n, m = len(haystack), len(needle)

        pi = [0] * m
        j = 0
        for i in range(1, m):
            while j > 0 and needle[j] != needle[i]:
                j = pi[j - 1]
            if needle[i] == needle[j]:
                j += 1
            pi[i] = j
        
        j = 0
        for i in range(n):
            while (j > 0) and (needle[j] != haystack[i]):
                j = pi[j - 1]
            if needle[j] == haystack[i]:
                j += 1
            if j == m:
              	# j = pi[j - 1]
                return i - m + 1
            
        return -1
```

