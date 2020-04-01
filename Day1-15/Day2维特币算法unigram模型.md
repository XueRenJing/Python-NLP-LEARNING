![avatar](/Users/betty/Downloads/自然语言处理/贪心学园课程练习/自建课程练习/02维特币算法-改.png)



#### 给个实现例子，sentence为句子，wordlist为词典，logx为概率对数:
```python
'''
数据准备
例子：我想成为数据科学家
词典：['我','我想','想','成','成为','数','数据',‘科学’,'科学家','家']
概率x：[0.1,0.05,0.1,0.1,0.2,0.2,0.05,0.05,0.05,0.1]
log(x)：[2.3 , 3.  , 2.3 , 2.3 , 1.61, 1.61, 3.  , 3.  , 3.  , 2.3 ]   
'''
import numpy as np
#matrix=np.zeros((9,9))
#matrix[0][8]
from nltk.util import bigrams, trigrams
from nltk.text import Text
from nltk import FreqDist
from functools import reduce
import numpy as np

dictionary = ['我','我想','想','成','成为','数','数据','科学','科学家','家']
sentence = '我想成为数据科学家'
probability = [0.1,0.05,0.1,0.1,0.2,0.2,0.05,0.05,0.05,0.1]
logx = -np.round(np.log(probability),2)
```

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
