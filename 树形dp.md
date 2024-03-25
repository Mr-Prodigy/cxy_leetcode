
[TOC]

> 感悟：利用递归进行，最重要的是理清关系。换根需要两次dfs，相对复杂
>
> 参考 [oi-wiki 树形dp](https://oi-wiki.org/dp/tree/)

# 题目：
## 不换根

### 1. 平衡二叉树
[110平衡二叉树](https://leetcode.cn/problems/balanced-binary-tree/description/)

思路：新建一个helper函数，该函数返回当前子树的高度。通过递归，比较左右节点的高度差

```python3
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isBalanced(self, root: Optional[TreeNode]) -> bool:

        isValid = True
        
        def helper(root):
            nonlocal isValid
            if not root:
                return 0
            if isValid:
                left = helper(root.left)
            if isValid:
                right = helper(root.right)
            if isValid and abs(left - right) > 1:
                isValid = False
            if isValid:
                return max(left, right) + 1
        
        helper(root)
        return isValid
```

### 2. 二叉树的直径
[543. 二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/description/)

思路：
求 任意两个节点之间的距离的最大值
即求 通过任意节点的长度的最大值
即求 某节点的左右子树的长度和的最大值

```python3
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def diameterOfBinaryTree(self, root: Optional[TreeNode]) -> int:
        ans = 0
        def dfs(node):
            nonlocal ans
            if not node:
                return 0
            l = dfs(node.left)
            r = dfs(node.right)
            ans = max(ans, l + r)
            return max(l + 1, r + 1)
        dfs(root)
        return ans
```

### 3. 树的直径
[1245. 树的直径](https://leetcode.cn/problems/tree-diameter/description/)  
> 这题同1522. N叉树的直径 https://leetcode.cn/problems/diameter-of-n-ary-tree/description/

题目和上题不同之处在于是N叉树，而非二叉树
思路相同，对于任意节点，找到其最长子边和第二长子边，相加即可

``` python3
class Solution:
    def treeDiameter(self, edges: List[List[int]]) -> int:
        graph = defaultdict(list)
        for a, b in edges:
            graph[a].append(b)
            graph[b].append(a)
        # 一个节点的最长边和第二长边
        
        ans = 0
        def dfs(node, fa):
            nonlocal ans
            top1 = top2 = 0
            for son in graph[node]:
                if son != fa:
                    dis = 1 + dfs(son, node)
                    _, top2, top1 = sorted((dis, top1, top2))
                    ans = max(ans, top1 + top2)
            return top1
        
        dfs(0, -1)
        return ans
```

### 4. 在树上执行操作以后得到的最大分数
[2925. 在树上执行操作以后得到的最大分数](https://leetcode.cn/problems/maximum-score-after-applying-operations-on-a-tree/description/)

思路：要求每条路径和都不为0，那么越靠近根的节点可替换的节点越多
也就是比较 $value[node]$ 和 $∑value[son]$ 
注意新建函数return的值，是子树已经被替换掉的最小值
实现 可以用所有节点和减去不变为0的节点

```python3
class Solution:
    def maximumScoreAfterOperations(self, edges: List[List[int]], values: List[int]) -> int:
        
        n = len(values)
        graph = [[] for _ in range(n)]

        for a, b in edges:
            graph[a].append(b)
            graph[b].append(a)
        graph[0].append(-1)
        
        def dfs(node, fa):
            if len(graph[node]) <= 1:
                return values[node]
            
            temp = 0
            for son in graph[node]:
                if son != fa:
                    temp += dfs(son, node)
            return min(temp, values[node])
        
        return sum(values) - dfs(0, -1)
```



### 5. 感染二叉树需要的总时间

[2385. 感染二叉树需要的总时间](https://leetcode.cn/problems/amount-of-time-for-binary-tree-to-be-infected/description/)

> 这题只有1711分，是因为很容易想到建图+BFS方法

思路：独特的返回值

```python3
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def amountOfTime(self, root: Optional[TreeNode], start: int) -> int:
        # 返回值是node节点的高度（node为0往下数），感染节点是否在下面
        def dfs(node): 
            nonlocal ans
            if not node:
                return -1, False
            l, lflag = dfs(node.left)
            r, rflag = dfs(node.right)
            if node.val == start:
                ans = max(l, r) + 1
                return 0, True  # 注意，这儿返回的是0，因为node节点下面的节点已经没有用了
            if lflag or rflag:  # 感染节点在下面
                ans = max(ans, l+r+2)
                curr = l+1 if lflag else r+1
                return curr, True
            return max(l, r)+1, False   #感染节点不在下面，返回
            
        ans = 0
        dfs(root)
        return ans
```



## 换根dp

换根dp比较难，


### 1. 树中距离之和
[834.树中距离之和](https://leetcode.cn/problems/sum-of-distances-in-tree/description/)

这题最重要的是找到 不同根之间的关系，如示例1以0为根和以2为根，到其余哪些节点的距离增加了，哪些节点的距离减少了？ 详见[灵神解答.](https://leetcode.cn/problems/sum-of-distances-in-tree/solutions/2345592/tu-jie-yi-zhang-tu-miao-dong-huan-gen-dp-6bgb/)

``` python3
class Solution:
    def sumOfDistancesInTree(self, n: int, edges: List[List[int]]) -> List[int]:
        graph = [[] for _ in range(n)]
        for a, b in edges:
            graph[a].append(b)
            graph[b].append(a)
        # graph[0].append(-1)
        ans = [0] * n
        size = [1] * n

        def dfs(node, fa, depth):
            ans[0] += depth
            for son in graph[node]:
                if son != fa:
                    dfs(son, node, depth+1)
                    size[node] += size[son]
        
        dfs(0, -1, 0)

        def helper(node, fa):
            if node != 0:
                ans[node] = ans[fa] + n - 2 * size[node]
            for son in graph[node]:
                if son != fa:
                    helper(son, node)
        
        helper(0, -1)

        return ans
```

### 2. 最小高度树
[310.最小高度树]( https://leetcode.cn/problems/minimum-height-trees/description/)

这题暂时只会拓扑排序做法，还不会换根dp做法

### 3. 统计可能的树根数目
[2581. 统计可能的树根数目](https://leetcode.cn/problems/count-number-of-possible-root-nodes/description/)

这道题需要找到换根之间的关系：如果节点 x 和 y 之间有边，那么从「以 x 为根的树」变成「以 y 为根的树」，就只有 [x,y] 和 [y,x] 这两个猜测的正确性变了，其余猜测的正确性不变。
找到关系后，再用两次DFS。（这个过程也不是很简单）

```python3
class Solution:
    def rootCount(self, edges: List[List[int]], guesses: List[List[int]], k: int) -> int:
        # 暴力解法：枚举每个根，得到father列表
        # 方法2: 求得一个二维数组dis[i][j]代表i到j的最近距离 --> dijkstra: O(n*mlogm), 然后判断： O(n*k)
        # 方法3:
        # 注意到，如果节点 x 和 y 之间有边，那么从「以 x 为根的树」变成「以 y 为根的树」，就只有 [x,y] 和 [y,x] 这两个猜测的正确性变了，其余猜测的正确性不变。
        # 先以0为根遍历一遍，得到father列表
        # 再遍历一遍，每次遍历的时候换根
        graph = collections.defaultdict(list)
        for a, b in edges:
            graph[a].append(b)
            graph[b].append(a)
            
        guss_fa = collections.defaultdict(list)
        for fa, son in guesses:
            guss_fa[son].append(fa)
        # print(guss_fa)
        # father列表记录以0为根的真值
        father = dict()
        count = 0
        def dfs(node, fa):
            nonlocal count
            father[node] = fa
            count += guss_fa[node].count(fa)
            for son in graph[node]:
                if son != fa:
                    dfs(son, node)
        dfs(0, -1)
        # print(count)
        ans = 0
        def helper(node, count):
            nonlocal ans
            if count >= k:
                ans += 1
            for son in graph[node]:
                if son != father[node]:
                    # son 和 node调换位置
                    temp = count
                    father[node] = son
                    temp += guss_fa[node].count(son)
                    temp -= guss_fa[son].count(node)
                    helper(son, temp) 
                    father[son] = node
        helper(0, count)
        return ans
```