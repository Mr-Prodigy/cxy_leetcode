# 字典树

> 模板

## 实现 Trie (前缀树)

[208. 实现 Trie (前缀树)](https://leetcode.cn/problems/implement-trie-prefix-tree/)

```python
class Trie:

    def __init__(self):
        self.node = [None] * 26
        self.isEnd = False


    def insert(self, word: str) -> None:
        cur = self
        for char in word:
            i = ord(char) - ord('a')
            if not cur.node[i]:
                cur.node[i] = Trie()
            cur = cur.node[i]
        cur.isEnd = True

    def search(self, word: str) -> bool:
        cur = self
        for char in word:
            i = ord(char) - ord('a')
            if not cur.node[i]:
                return False
            cur = cur.node[i]
        return cur.isEnd


    def startsWith(self, prefix: str) -> bool:
        cur = self
        for char in prefix:
            i = ord(char) - ord('a')
            if not cur.node[i]:
                return False
            cur = cur.node[i]
        return True



# Your Trie object will be instantiated and called as such:
# obj = Trie()
# obj.insert(word)
# param_2 = obj.search(word)
# param_3 = obj.startsWith(prefix)
```



## 实现 Trie （前缀树） II

[1804. 实现 Trie （前缀树） II](https://leetcode.cn/problems/implement-trie-ii-prefix-tree/)

```python
class Trie:
	def __init__(self):
		self.children = [None] * 26
		self.end_num = 0
		self.num = 0
	
	def insert(self, word):
		cur = self
		for ch in word:
			ch = ord(ch) - ord('a')
			if not cur.children[ch]:
				cur.children[ch] = Trie()
			cur = cur.children[ch]
			cur.num += 1
		cur.end_num += 1

	def countWordsEqualTo(self, word):
		cur = self
		for ch in word:
			ch = ord(ch) - ord('a')
			if not cur.children[ch]:
				return 0
			cur = cur.children[ch]
		return cur.end_num

	def countWordsStartingWith(self, prefix):
		cur = self
		for ch in prefix:
			ch = ord(ch) - ord('a')
			if not cur.children[ch]:
				return 0
			cur = cur.children[ch]
		return cur.num

	def erase(self, word):
		cur = self
		for ch in prefix:
			ch = ord(ch) - ord('a')
			if not cur.children[ch]:
				return
			cur = cur.children[ch]
			cur.num -= 1
		cur.end_num -= 1

class Trie:
	def __init__(self):
		self.children = [None] * 26
		self.end_num = 0
		self.num = 0
	
	def insert(self, word):
		cur = self
		for ch in word:
			ch = ord(ch) - ord('a')
			if not cur.children[ch]:
				cur.children[ch] = Trie()
			cur = cur.children[ch]
			cur.num += 1
		cur.end_num += 1

	def countWordsEqualTo(self, word):
		cur = self
		for ch in word:
			ch = ord(ch) - ord('a')
			if not cur.children[ch]:
				return 0
			cur = cur.children[ch]
		return cur.end_num

	def countWordsStartingWith(self, prefix):
		cur = self
		for ch in prefix:
			ch = ord(ch) - ord('a')
			if not cur.children[ch]:
				return 0
			cur = cur.children[ch]
		return cur.num

	def erase(self, word):
		cur = self
		for ch in word:
			ch = ord(ch) - ord('a')
			if not cur.children[ch]:
				return
			cur = cur.children[ch]
			cur.num -= 1
		cur.end_num -= 1





# Your Trie object will be instantiated and called as such:
# obj = Trie()
# obj.insert(word)
# param_2 = obj.countWordsEqualTo(word)
# param_3 = obj.countWordsStartingWith(prefix)
# obj.erase(word)
```



## 最长公共后缀查询

[100268. 最长公共后缀查询](https://leetcode.cn/problems/longest-common-suffix-queries/)

```python
class Trie:
    def __init__(self):
        self.node = [None] * 26
        self.idx = -1
        self.length = inf
    
    def insert(self, i, word):
        cur = self
        for char in word:
            t = ord(char) - ord('a')
            if not cur.node[t]:
                cur.node[t] = Trie()
            cur = cur.node[t]
            if cur.idx == -1 or len(word) < cur.length or (len(word) == cur.length and i < cur.idx):
                cur.idx = i
                cur.length = len(word)
    
    def search(self, word):
        cur = self
        for char in word:
            t = ord(char) - ord('a')
            if not cur.node[t]:
                return cur.idx
            cur = cur.node[t]
        
        return cur.idx


class Solution:
    def stringIndices(self, wordsContainer: List[str], wordsQuery: List[str]) -> List[int]:
        # 字典树
        trie = Trie()
        ans = []
        min_length = min(len(w) for w in wordsContainer)
        min_idx = [len(w) for w in wordsContainer].index(min_length)
        # print(min_idx)
        for i, word in enumerate(wordsContainer):
            trie.insert(i, word[::-1])
        
        for word in wordsQuery:
            idx = trie.search(word[::-1])
            if idx == -1:
                ans.append(min_idx)
            else:
                ans.append(idx)
        
        return ans
```

