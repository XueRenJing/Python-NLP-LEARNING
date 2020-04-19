## Day3 拼写纠错
拼写纠错是搜索引擎的一个核心，拼写错误分两种情况：
拼写错误分两种情况：
> 1 错别字

> 2 不是错别字，但输入的字不符合语法或者语境 例如：I am go home

#### 怎么触发以上两种情况？
第一种情况，明显地，该字不在我们的词库里面就是错别字

第二种情况，我们能够通过语言模型计算，例如计算我们可能是错别字的该种情况，它发生的概率就比较低。

#### 当意识到用户已经输入错别字之后，怎么进行下一步的纠错呢？

一种最简单的方法就是计算编辑距离，通过计算用户的输入和候选单词之间的编辑距离，从候选单词中找到与用户输入编辑距离最短的。

![image](https://github.com/XueRenJing/Python-NLP-LEARNING/raw/master/edit_distance.png)

那编辑的操作有哪几种呢？
>1 insert 插入

>2 delete 删除

>3 replace 替换

通过以上的定义就可以计算两个单词之间的编辑距离，以"kitten" 和 "sitting" 这两个单词为例，把 "kitten" 转换为 "sitting" 需要的最少的编辑操作有：
>1.kitten → sitten (把"s"替换为"k")

>2.sitten → sittin (把"i"替换为"e")

>3.sittin → sitting (在末尾处插入"g")

所以，"kitten" 和 "sitting" 之间的编辑距离为 3 。

那么怎么实现计算编辑距离的算法呢？


以下通过创建矩阵的方式来说明，假设我们两个数组的长度分别为m，n，那么我们首先创建一个（m+1）*（n+1）维的空矩阵，首先给矩阵的
第一列和第一行赋值，值与字符所在位置有关系，如图一所示。
![image](https://github.com/XueRenJing/Python-NLP-LEARNING/raw/master/pictures/编辑距离一.png)

以此为基础并根据一些计算规则计算矩阵剩余的元素

我们的计算规则为,其中的d就是矩阵中的元素：
>d[i,j]=min(d[i-1,j]+1 、d[i,j-1]+1、d[i-1,j-1]+temp) 这三个当中的最小值。

>其中：str1[i] == str2[j]，用temp记录它，为0。否则temp记为1

>我们用d[i-1,j]+1表示增加操作

>d[i,j-1]+1 表示我们的删除操作

>d[i-1,j-1]+temp表示我们的替换操作

![image](https://github.com/XueRenJing/Python-NLP-LEARNING/raw/master/pictures/编辑距离二.png)

依次类推直到矩阵全部生成
根据以上的思路，能够通过代码实现我们的算法
直接的思路就是根据以上动态规划的思路，首先填补矩阵中第一行和第一列，根据这些首先填补的位置，通过上述的操作继续填补矩阵中的元素，代码实现如下：
```python
def dist_edit(str1,str2):
    '''
    功能：通过动态规划计算输入的两个字符串的编辑距离
    输入：字符串str1、字符串str2
    输出：两个字符串str1、字符串str2的编辑距离
    '''
    m=len(str1)+1
    n=len(str2)+1
    matrix=np.zeros((m,n))
    matrix[0][0]=0
    for i in range(1,m):
        matrix[i][0]=matrix[i-1][0]+1
        
    for j in range(1,n):
        matrix[0][j]=matrix[0][j-1]+1
        
        
    for i in range(1,m):
        for j in range(1,n):
            if str1[i-1]==str2[j-1]:
                cost=0
            else:
                cost=1
            matrix[i][j]=min(matrix[i-1][j]+1,matrix[i][j-1]+1,matrix[i-1][j-1]+cost)
        
    return(matrix[m-1][n-1])

dist_edit('apply','apple')
```

 #### 所以，计算单词apply与apple的编辑距离为：
 ```python
 1.0
 ```

另外通过上述的这个计算公式d[i,j]=min(d[i-1,j]+1 、d[i,j-1]+1、d[i-1,j-1]+temp)
中能够看出，这是一个重复使用编辑距离计算公式的过程，所以可以使用递归的方式计算：

```python
def dist_edit_recusion(str1,str2):
    '''
    功能：通过递归计算输入的两个字符串的编辑距离
    输入：字符串str1、字符串str2
    输出：两个字符串str1、字符串str2的编辑距离
    '''
    if len(str1)==0 and len(str2)==0:
        return 0
    elif len(str1)==0:
        return len(str2)
    elif len(str2)==0:
        return len(str1)
    
    if str1[len(str1)-1]==str2[len(str2)-1]:
        d = 0
    else:
        d = 1
    
    return min(dist_edit_recusion(str1,str2[:-1])+1,
               dist_edit_recusion(str1[:-1],str2)+1,
               dist_edit_recusion(str1[:-1],str2[:-1])+d
               )

```

 #### 所以同样，计算单词apply与apple的编辑距离为：
 ```python
 1.0
 ```

 #### 以上就是计算两个字符串之间编辑距离的计算方法以及代码。但是存在一些问题就是我们一开始提到，在我们的词典里面找到编辑距离最短的单词来返回。那么，就需要遍历整个词典来计算编辑距离，时间复杂度较高。所以就有了新的筛选方法。

