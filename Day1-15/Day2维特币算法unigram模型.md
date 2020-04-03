## Day2维特币算法以Uni-gram语言模型为基础

在Day1中提到最大匹配算法里面只利用了词典，并没有考虑到语义的情况，那么如果考虑语义的话，应该怎么做呢？

我们可以分为两步：

    第一步，产生所有分词的情况

    第二步，在所有分词的情况里面选最好的

在第二步中我们就需要考虑到语义，考虑到语义，就需要考虑语言模型。语言模型是什么呢？就是考虑这种分词的概率，例如把句子“我是好人”分词成（我，是，好人），像（我，是，好人）这其中一种分词发生的概率就可以用语言模型衡量。

为了简化问题，这里的语言模型考虑nigram模型，nigram模型指的是句子每个词发生的概率与周围词发生的概率无关，所以刚才的例子中的概率 P（我，是，好人）=P（我）* P（是）* P（好人）

因为刚才分成两步的话，效率较低，那么维特币这个人物就提出了维特币算法，维特币算法是语音识别、HMM的铺垫。

#### 给定一个例子，sentence为句子，wordlist为词典:
```python
'''
数据准备
例子：我想成为数据科学家
词典：['我','我想','想','成','成为','数','数据',‘科学’,'科学家','家']
概率x：[0.1,0.05,0.1,0.1,0.2,0.2,0.05,0.05,0.05,0.1]
'''
import numpy as np
from nltk.util import bigrams, trigrams
from nltk.text import Text
from nltk import FreqDist
from functools import reduce
import numpy as np

dictionary = ['我','我想','想','成','成为','数','数据','科学','科学家','家']
sentence = '我想成为数据科学家'
probability = [0.1,0.05,0.1,0.1,0.2,0.2,0.05,0.05,0.05,0.1]
```

接下来我们为了实现维特币算法，需要做一个转换，概率先取对数，再对对数化之后的结果取负数，取负数的作用是能让我们求最小值，对数化的作用是把乘法的运算转换为加法运输。

#### 转化成加法运算之后，刚才提到的例子-log(P（我，是，好人))=(-log(P(我)))+(-log(P(是)))+(-log(P(好人))),通过加法的方式就能够实现维特币算法中的最短路径思想了:

```python
logx = -np.round(np.log(probability),2)
```
#### logx结果为：
```python
array([2.3 , 3.  , 2.3 , 2.3 , 1.61, 1.61, 3.  , 3.  , 3.  , 2.3 ])
```

#### 结合上面的分词方式以及每个词的概率进行-log(x)的转换后，可以通过下图的形式表达，例如路径上面词“我想”用点1到点3的路径表达，词“成为”用点3到点5的路径表达，词“我”用点1到点2的路径表达，如此类推。而上面的数字就是每个词概率的-log(x)转换
 #### 一开始我们求各自分词的最大的概率，假设其中一种分词（‘我想’，‘成为’，‘数据’，‘科学’，‘家’）的概率最大，那么：
 #### P（‘我想’，‘成为’，‘数据’，‘科学’，‘家’）=P（‘我想’）* P（‘成为’）* P（‘数据’）* P（‘科学’）* P（‘家’）就是最大
 #### 而通过-log(x)的转换之后，-log(P(‘我想’，‘成为’，‘数据’，‘科学’，‘家’))的值就是最小的，反映到下图中，就是一条最短路径，所以求最大的分词可能性就是一个求最短路径的过程

 ![image](https://github.com/XueRenJing/Python-NLP-LEARNING/raw/master/viterbi.png)

 #### 为方便运输，把上图的过程转化为状态转移矩阵，首先第一步转为数组：
 ```python
 '''
 生成状态转移数组
 '''
 def transformlist(sentence,dictionary,logx):
     word_pro = dict(zip(dictionary,logx))
     word_noin_sentence=list((set(sentence)-set([i for i in word_pro.keys() if len(i)==1])))
     pro_no=[20]*len(list(set(sentence)-set([i for i in word_pro.keys() if len(i)==1])))
     word_pro_not=dict(zip(word_noin_sentence,pro_no))
     transform_dict = dict(word_pro_not,**word_pro)
     transformlist=[]
     for i,j in transform_dict.items():
         transformlist.append(((sentence.index(i[0])+1,sentence.index(i[-1])+2),j))
     return(transformlist)

 transformlist = transformlist(sentence,dictionary,logx) 
 ```
 #### 所以把路径中跳转的过程transformlist结果如下，对应图中的路径和数字，例如，从8到9是20：
 ```python
 [((8, 9), 20),
  ((4, 5), 20),
  ((6, 7), 20),
  ((7, 8), 20),
  ((1, 2), 2.3),
  ((1, 3), 3.0),
  ((2, 3), 2.3),
  ((3, 4), 2.3),
  ((3, 5), 1.61),
  ((5, 6), 1.61),
  ((5, 7), 3.0),
  ((7, 9), 3.0),
  ((7, 10), 3.0),
  ((9, 10), 2.3)]
 ```


 #### 进一步，生成状态转移矩阵：
 ```python
 '''
 生成状态转移矩阵
 '''    
 def matrix_transform(sentence,transformlist):
     matrix = np.zeros((len(sentence)+2,len(sentence)+2))
     for i in range(len(transformlist)):
         matrix[transformlist[i][0][0]][transformlist[i][0][1]] = transformlist[i][1]
     return(matrix)
     
 matrix = matrix_transform(sentence,transformlist)  
 ```

 #### 所以，状态转移矩阵matrix结果为：
 ```python
 array([[ 0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,
          0.  ,  0.  ],
        [ 0.  ,  0.  ,  2.3 ,  3.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,
          0.  ,  0.  ],
        [ 0.  ,  0.  ,  0.  ,  2.3 ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,
          0.  ,  0.  ],
        [ 0.  ,  0.  ,  0.  ,  0.  ,  2.3 ,  1.61,  0.  ,  0.  ,  0.  ,
          0.  ,  0.  ],
        [ 0.  ,  0.  ,  0.  ,  0.  ,  0.  , 20.  ,  0.  ,  0.  ,  0.  ,
          0.  ,  0.  ],
        [ 0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  1.61,  3.  ,  0.  ,
          0.  ,  0.  ],
        [ 0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  , 20.  ,  0.  ,
          0.  ,  0.  ],
        [ 0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  , 20.  ,
          3.  ,  3.  ],
        [ 0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,
         20.  ,  0.  ],
        [ 0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,
          0.  ,  2.3 ],
        [ 0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,  0.  ,
          0.  ,  0.  ]])
 ```

 #### 根据矩阵输出最短路径：
 ```python
 '''
 根据矩阵输出最短路径
 '''
 def shortestlink(sentence,matrix):
     test=[0]
     pointer=[1]    
     for i in range(2,len(sentence)+2,1):
         test2=[]
         incominglink=[]
         for j in range(1,i,1):
             if matrix[j][i]>0:
                 print(test[j-1]+matrix[j][i])
                 test2.append(test[j-1]+matrix[j][i])
                 incominglink.append(j)
         link_min = dict(zip(test2,incominglink))    
         tt=min(test2)
         ttt=link_min[tt]
         test.append(tt)
         pointer.append(ttt)
     pointer.append(len(sentence)+1)
     shortestlink=list(set(pointer))
     return(shortestlink)
     
 shortestlink=shortestlink(sentence,matrix)
 ```
 #### 所以，最短路径shortestlink结果为：
 ```python
 [1, 3, 5, 7, 10]
 ```

 #### 把最短路径转化为分词结果：
 ```python
 '''
 根据最短路径输出分词结果
 '''
 def word_cut(shortestlink，sentence):
     word_cut=[]
     for i in range(len(shortestlink)-1):
         print(sentence[shortestlink[i]-1:shortestlink[i+1]-1])
         word_cut.append(sentence[shortestlink[i]-1:shortestlink[i+1]-1])
         if i==len(shortestlink):
             break
     return(word_cut)
     
 word_cut=word_cut(shortestlink,sentence)
 ```

 #### 所以最后分词结果为：
 ```python
 ['我想', '成为', '数据', '科学家']
 ```
