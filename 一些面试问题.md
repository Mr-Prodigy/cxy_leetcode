## 照片问题
你正在浏览照片，album中包含所有的照片。其中有一部分是你最喜欢的，为favourite图集。请你实现方法getPhoto和sortPhoto。sortPhoto返回一个列表，将album中favourite的照片放在前面，其余照片顺序不变。而调用getPhoto方法，会弹出来一张图片供欣赏，请先弹出favourite中的图片，再按序弹出album中不在favourite的图片。 当浏览完所有的图片后，再调用这个方法会弹出-1。 

You are browsing photos, and an album contains all of them. Some of them are your favorite. Please implement the methods getPhoto and sortPhoto. sortPhoto returns a list that puts the favored photo in  the front and the rest of the photos. in the same order to the original album. Calling getPhoto method, it will pop up a picture for enjoyment, please pop up the pictures in the favorite first, then pop up the pictures in the album which are not in the favorite in order. After browsing all the pictures, calling this method again will pop up -1.

Noted, favorite and album are sorted. We will call sortPhoto first before calling getPhoto.

Example:

album = [1, 2, 2, 3, 4, 5]
favourite = [1, 2, 2, 5]

Output after call sortPhoto: [1, 2, 2, 5, 3, 4]

call getPhoto 10 times: 1 2 2 5 3 4 -1 -1 -1



注意，favourite和album都是sorted的。
例子1：
album = [1, 2, 2, 3, 4, 5]
favourite = [1, 2, 2, 5]
调用sortPhoto后的输出： [1, 2, 2, 5, 3, 4]
调用getPhoto 10次后的输出为：1 2 2 5 3 4 -1 -1 -1



Followup:

This time you don't need implement sortPhoto. You only need to implement getPhoto.

call getPhoto n times(n = album.size()), the time complexity and space complexity should be O(n) and O(1) seperately.

follow up：
这次不实现sortPhoto方法，只实现getPhoto方法。要求时间复杂度O(n)，空间复杂度O(1)。
完整题目：
你正在浏览照片，album中包含所有的照片。其中有一部分是你最喜欢的，为favourite图集。请你实现一个方法getPhoto。每次调用这个方法，弹出来一张图片供欣赏，请先弹出favourite中的图片，再弹出album中不在favourite的图片。 当浏览完所有的图片后，你可以弹出-1。 
注意，favourite和album都是sorted的。

例子1：
album = [1, 2, 2, 3, 4, 5]
favourite = [1, 2, 2, 5]
调用getPhoto 10次后的输出为：1 2 2 5 3 4 -1 -1 -1

例子2：
album = []
favourite = []
调用getPhoto 3次后的输出为: -1 -1 -1

解答：
```python
class Solution:
    def __init__(self, favourite, album):
        self.count = -1
        self.favourite = favourite
        self.album = album
        self.count = -1
        self.count2 = -1
        self.inFavourite = True if favourite else False
    
    def getPhoto(self):
        # album 为空
        if not self.album:
            return -1
        # favourite为空
        if not self.favourite:
            if self.count2 < len(self.album) - 1:
                self.count2 += 1
                return album[self.count2]
            return -1
            
        # 先遍历favourite
        if self.inFavourite:
            self.count += 1
            if self.count == len(self.favourite) - 1:
                self.inFavourite = False
                self.count = 0
                return self.favourite[-1]
            return self.favourite[self.count]
        
        # self.count = 0, self.count2 = 0
        while self.count2 < len(self.album):
            self.count2 += 1
            while self.count2 < len(self.album)\
                and self.count < len(self.favourite)\
                and self.album[self.count2] > self.favourite[self.count]:
                    self.count += 1
            while self.count2 < len(self.album)\
                and self.count < len(self.favourite)\
                and self.album[self.count2] == self.favourite[self.count]:
                    self.count2 += 1
                    self.count += 1
            
            if self.count2 < len(self.album):
                return self.album[self.count2]
        return -1

album = [1, 2, 2, 3, 4, 5]
favourite = [1, 2, 2, 5]
test = Solution(favourite, album)
for _ in range(10):
    print(test.getPhoto())
// 2024.01.25
class albumHandler {
private:
    int idx = 0;
    int p1 = 0;
    int p2 = 0;
    vector<int> favourite;
    vector<int> album;
    bool inFavourite = true; // 代表现在还在弹出favourite相册的内容

public:
    albumHandler(vector<int> a, vector<int> b): album(a), favourite(b) {
        // album = a;
        // favourite = b;
        if (favourite.size() == 0) {
            inFavourite = false;
        }
    }

    int getPhoto() {
        if (inFavourite) {
            int res = favourite[idx++];
            if (idx >= favourite.size()) {
                inFavourite = false;
            }
            return res;
        }
        while (p1 < album.size()) {
            while (p2 < favourite.size() && favourite[p2] < album[p1]) {
                p2++;
            }
            if (p2 < favourite.size() && favourite[p2] == album[p1]) {
                p1++;
            } else {
                p1++;
                return album[p1-1];
            }
        }
        return -1;
    }
};

// album = [1, 2, 2, 3, 4, 5]
// favourite = [1, 2, 2, 5]
// 调用getPhoto 10次后的输出为：1 2 2 5 3 4 -1 -1 -1

int main() {
    vector<int> album = {1, 2, 2, 3, 4, 5};
    // vector<int> favourite = {1, 2, 2, 5};
     vector<int> favourite = {};
    albumHandler* handler = new albumHandler(album, favourite);
    for (int i = 0; i < 10; ++i) {
        cout << handler->getPhoto() << endl;
    }
}

```

## 学生问题
有几个学生被淘汰了？
给你一个二维数组，`a[i][0]`代表第i个学生的math成绩，`a[i][1]`代表第i个学生的English成绩。 如果存在学生b的两门成绩都比学生a高，a就被淘汰了。问一共有几个学生被淘汰了？

模仿[253.会议室2](https://leetcode.cn/problems/meeting-rooms-ii/solutions/2623924/san-chong-fang-shi-dui-you-xian-dui-lie-4kfnw/)的做法。
```C++
/// 2024.01.29
int countFailedStudents(vector<vector<int>>& students) {
    sort(students.begin(), students.end(), [](vector<int> a, vector<int> b) {
        return a[0] < b[0];
    });
    int ans = 0;
    priority_queue<int, vector<int>, greater<int>> survivor; // 代表未被淘汰的学生
    for (auto student:students) {
        int e = student[1];
        while (!survivor.empty() && survivor.top() < e) {
            survivor.pop();
        }
        survivor.push(e);        
    }
    return students.size() - survivor.size();
}
int main() {
    vector<vector<int>> students = {{66, 67}, {65, 80}, {71, 63}, {71, 56}};
    cout << countFailedStudents(students) << endl;
    return 3;
}
```

仔细想想，好像并不需要pq，维护一个单调递减的栈就好了。但没考虑数学成绩相同的做法，后续再补充。
```C++
/// 2024.01.29
int countFailedStudents(vector<vector<int>>& students) {
    sort(students.begin(), students.end(), [](vector<int> a, vector<int> b) {
        return a[0] < b[0];
    });
    int ans = 0;
    stack<int> survivor; // 代表未被淘汰的学生,单调递减的栈
    for (auto student:students) {
        int e = student[1];
        while (!survivor.empty() && survivor.top() < e) {
            survivor.pop();
        }
        survivor.push(e);        
    }
    return students.size() - survivor.size();
}
int main() {
    vector<vector<int>> students = {{66, 67}, {65, 80}, {71, 63}, {71, 56}};
    cout << countFailedStudents(students) << endl;
    return 3;
}
```

以上两种是错误做法，都没有考虑到数学成绩相同的情况，后续再补充。
下面介绍两种正确做法。
``` C++
/// 2024.01.30
int countFailedStudents(vector<vector<int>>& students) {
    if (students.empty()) return 0;
    int ans = 0;
    sort(students.begin(), students.end(), [](vector<int> a, vector<int> b) {
        if (a[0] != b[0]) return a[0] < b[0];
        return a[1] > b[1]; 
    }); // 按照数学成绩从小到大，数学相同的英语成绩从大到小
    // 注意corner case，两个人数学成绩相同
    int cur = -1; 
    for (int i = students.size() - 1; i >= 0; --i) {
        if (cur > students[i][1]) ans++;
        cur = max(cur, students[i][1]);
    }
    return ans;
}
int main() {
    vector<vector<int>> students = {{66, 67}, {65, 80}, {71, 63}, {71, 56}};
    cout << countFailedStudents(students) << endl;
    return 0;
}

/// 2024.01.25
int countFailedStudents(vector<vector<int>>& students) {
    if (students.empty()) return 0;
    int ans = 0;
    sort(students.begin(), students.end(), [](vector<int> a, vector<int> b) {
        return a[0] < b[0];
    });
    // 注意corner case，两个人数学成绩相同
    int pre = -1;    // 指排序后最后一个学生到cur组之前的最大值
    int cur = students.back()[1]; // 后一个学生及后面数学成绩相同的一组，他们英语成绩的最大值
    for (int i = students.size() - 2; i >= 0; --i) {
        if (students[i][0] == students[i+1][0]) { //当前学生和上一个相同，更新
            cur = max(cur, students[i][1]);
        } else { // 不同，更新
            pre = max(pre, cur);
            cur = students[i][1];
        }
        if (students[i][1] < pre) {
            ans++;
        }
    }
    return ans;
}
int main() {
    vector<vector<int>> students = {{66, 67}, {65, 80}, {71, 63}, {71, 56}};
    cout << countFailedStudents(students) << endl;
    return 3;
}
```



## 华容道问题

人生第一次英文面试，居然是Google，面着玩罢了。
面试官是个中国小姐姐
首先是自我介绍，为此提前背了稿子。。。

给出了一道算法题：
给一个m*n大小的矩阵表示地图，两辆车a，b，目的地分别是A，B，地图上用'.'表示道路，'#'表示墙。

一辆车会阻隔另一辆车的行进，要求判断两辆车能否都到达目的地。

觉得有些抽象，让她解释下，于是给了我几个样例
样例1：
a . A
\###
b . B
结果：true

样例2：
aBAb
结果：false

觉得不太有思路，就让她给点提示，于是给了我个简化的思路，先只考虑一辆车，于是写了一个dfs的解法
接着让我写个能测试输出的函数

本来以为要运行调试，结果面试官说他们是直接看代码

最后时间到了还是没把完整的方法给出来。 

作者：Buffer_专心找工版
链接：https://www.nowcoder.com/feed/main/detail/c11365cf22ab492e8df2129f9c968d51
来源：牛客网



```python
def canReach(grid):
    row, col = len(grid), len(grid[0])
    # 先找出i, j
    for i in range(row):
        for j in range(col):
            if grid[i][j] == 'a':
                a = (i, j)
                grid[i][j] = '.'
            elif grid[i][j] == 'b':
                b = (i, j)
                grid[i][j] = '.'
            elif grid[i][j] == 'A':
                A = (i, j)
            elif grid[i][j] == 'B':
                B = (i, j)
                
    # visited[ax][ay][bx][by] 代表a, b能达到的位置
    visited = [[[[False] * col for _ in range(row)] for __ in range(col)] for _ in range(row)]
    visited[a[0]][a[1]][b[0]][b[1]] = True
    queue = collections.deque([(a[0], a[1], b[0], b[1])])
    while queue:
        axc, ayc, bxc, byc = queue.popleft()
        # a先走
        for axn, ayn in ((axc+1, ayc), (axc-1, ayc), (axc, ayc+1), (axc, ayc-1)):
            if axn < 0 or axn >= row or ayn < 0 or ayn >= col or \
            grid[axn][ayn] == '#' or (axn == bxc and ayn == byc) or \
            visited[axn][ayn][bxc][byc]:
                continue
            if (axn, ayn) == A and (bxc, byc) == B:
                return True
            visited[axn][ayn][bxc][byc] = True
            queue.append((axn, ayn, bxc, byc))
        
        # b再走
        for bxn, byn in ((bxc+1, byc), (bxc-1, byc), (bxc, byc+1), (bxc, byc-1)):
            if bxn < 0 or bxn >= row or byn < 0 or byn >= col or \
            grid[bxn][byn] == '#' or (bxn == axc and byn == ayc) or \
            visited[axc][ayc][bxn][byn]:
                continue
            if (axc, ayc) == A and (bxn, byn) == B:
                return True
            visited[axc][ayc][bxn][byn] = True
            queue.append((axc, ayc, bxn, byn))
    
    return False
test_list = []
test1 = [['a', '.', 'A'], ['#', '#', '#'], ['b', '.', 'B']]
test2 = [['a', 'B', 'A', 'b']]
test3 = [['#', '#', '.', '#'], ['a', 'B', 'A', 'b']]
test_list.extend([test1, test2, test3])


for test in test_list:
    print(canReach(test))
```



## 最长半递减数组

[2863. 最长半递减数组](https://leetcode.cn/problems/maximum-length-of-semi-decreasing-subarrays/)

```python
class Solution:
    def maxSubarrayLength(self, nums: List[int]) -> int:
        s = sorted(zip(nums,range(len(nums))),reverse = True)
        smallest = len(nums)
        ans = 0
        for n,i in s:
            ans = max(i-smallest+1,ans)
            smallest = min(smallest,i)
        return ans
```

