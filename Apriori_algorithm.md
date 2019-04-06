
# 11 Apriori 算法（关联分析）
关联分析的目标包括两项：发现频繁项集和发现关联规则

优点：易于实现
缺点：大数据下运行较慢
适用的数据类型：数值型或标称型数据
项集支持度 ( support ) 被定义为数据集中包含该项集的记录所占的比例
可信度或置信度 ( confidence ) 定义是 大项集支持度/小项集支持度 

首先，需要产生候选项集


```python
# 数据载入函数
def loadDataSet():
    return [[1,3,4],[2,3,5],[1,2,3,5],[2,5]]

# 产生项集 C1
def createC1(dataSet):
    C1=[]
    for data in dataSet:
        for item in data:
            if not [item] in C1:
                C1.append([item])
    # C1 进行排序
    C1.sort()
    # 冻结
    return list(map(frozenset,C1))

# createC1(loadDataSet())

# 计算所有项集的支持度
def scanD(D,Ck,minSupport):
    # D  是数据集
    # Ck 是项集
    # minSupport 是最小支持度
    ssCnt = {}
    for tid in D:
        for can in Ck:
            if can.issubset(tid):
            # 若项集 can 在数据集子集中出现 
            # 则字典 ssCnt[can] 加一
                if not can in ssCnt:
                    ssCnt[can] = 1
                else:
                    ssCnt[can] += 1
    numItems = float(len(D))
    retList = []
    supportData = {}
    for key in ssCnt:
        support = ssCnt[key]/numItems
        if support >= minSupport:
            retList.insert(0,key)
        supportData[key] = support
    return retList,supportData

D = loadDataSet()
Ck = createC1(D)
retList,supportData = scanD(D,Ck,minSupport = 0.5)
print(retList)
print(supportData)
```

    [frozenset({5}), frozenset({2}), frozenset({3}), frozenset({1})]
    {frozenset({1}): 0.5, frozenset({3}): 0.75, frozenset({4}): 0.25, frozenset({2}): 0.75, frozenset({5}): 0.75}
    

# 组织完整的 Apriori 算法


```python
# 整个 Apriori 算法的伪代码如下:

# 当集合中项的个数大于 0 时
#     构建一个 k 个项组成的候选项集的列表
#     检查数据以确定每个项集都是频繁的
#     保留频繁项集并构建 k + 1 项组成的候选项集的列表
```


```python
def aprioriGen( Lk , k ):
    retList = []
    LenLk = len(Lk)
    for i in range(LenLk):
        for j in range(i+1,LenLk):
            L1 = list(Lk[i])[:k-2]
            L2 = list(Lk[j])[:k-2]
            L1.sort()
            L2.sort()
            if L1==L2:
                retList.append(Lk[i]|Lk[j])
    return retList

def apriori( dataSet , minSupport = 0.5 ):
    C1 = createC1(dataSet)
    D  = list(map( set , dataSet ))
    L1 , supportData = scanD(D,C1,minSupport)
    L = [L1]
    k = 2
    while(len(L[k-2])>0):
        Ck = aprioriGen( L[k-2] , k )
        Lk , supK = scanD(D,Ck,minSupport)
        supportData.update(supK)
        L.append(Lk)
        k += 1
    return L, supportData

L, supportData = apriori( D , minSupport = 0.75 )
print(L)
print(supportData)
```

    [[frozenset({5}), frozenset({2}), frozenset({3})], [frozenset({2, 5})], []]
    {frozenset({1}): 0.5, frozenset({3}): 0.75, frozenset({4}): 0.25, frozenset({2}): 0.75, frozenset({5}): 0.75, frozenset({2, 5}): 0.75, frozenset({3, 5}): 0.5, frozenset({2, 3}): 0.5}
    
