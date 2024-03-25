**艾氏筛, 时间复杂度为O(nloglogn)**

```python
MX = 10 ** 5 + 1

is_prime = [True] * MX

is_prime[1] = False

for i in range(2, int(MX ** 0.5) + 1):

​    if is_prime[i]:

​        \# 为什么从i*i开始？ 因为小于i*i的合数一定是某个小于i的数的倍数，被提前淘汰了。

​        for j in range(i**2, MX, i):

​            is_prime[j] = False
```



