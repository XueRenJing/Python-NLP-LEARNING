## Day1 最大匹配算法是中文分词中的一类贪心算法

最大匹配算法算法是用于中文分词的算法，分为前向最大匹配和后向最大匹配，实现算法需要一个词典以及需要最大匹配的长度，给个实现例子，sentence为句子，wordlist为词典


```python
sentence = "我想成为一名数据科学家"
wordlist = ["我", "想", "成为", "一名", "数据", "科学家"]    
```

*根据我们的例子进行前向最大匹配算法流程的说明，选需匹配词的最大长度为5，True为匹配中词典wordlist中的词：*

> 第一步：

> 句子：我想成为一名数据科学家

>1 我想成为一 False

>2 我想成为 False

>3 我想成 False

>4 我想 False

>5 我 True

> 第二步：

> 句子变成：想成为一名数据科学家

>1 想成为一名 False

>2 想成为一 False

>3 想成为 False

>4 想成 False

>5 想 True

> 第三步：

> 句子变成：成为一名数据科学家

>1 成为一名数 False

>2 成为一名 False

>3 成为一 False

>4 成为 True

> 第四步：

> 句子变成：一名数据科学家

>1 一名数据科 False

>2 一名数据 False

>3 一名数 False

>4 一名 False

> 第五步：

> 句子变成：数据科学家

>1 数据科学家 False

>2 数据科学 False

>3 数据科 False

>4 数据 True

> 第六步：

> 句子变成：科学家

>1 科学家 True



#### 所以前向最大匹配分词结果为
```python
["我", "想", "成为", "一名", "数据", "科学家"]    
```

*根据我们的例子进行后向最大匹配算法流程的说明，与前向最大匹配类似，但词的减少从左边开始：*

> 第一步：

> 句子：我想成为一名数据科学家

>1 数据科学家 False

>2 据科学家 False

>3 科学家 True

> 第二步：

> 句子变成：我想成为一名数据

>1 为一名数据 False

>2 一名数据 False

>3 名数据 False

>4 数据 True

> 第三步：

> 句子变成：我想成为一名

>1 想成为一名 False

>2 成为一名 False

>3 为一名 False

>4 一名 True

> 第四步：

>句子变成：我想成为

>1 我想成为 False

>2 想成为 False

>3 成为 True

>第五步：

> 句子变成：我想

>1 我想 False

>2 想 True

>第六步：

> 句子变成：我

>1 我 True


#### 所以后向最大匹配分词结果为
```python
['科学家', '数据', '一名', '成为', '想', '我']   
```







#### python代码
前向最大匹配：
```python
def forward_max_match(sentence,wordlist,m):
    '''
    功能：通过前向最大匹配的算法对句子进行分词
    输入：sentence需要分词的句子、wordlist需要参考的词典、m最大长度
    输出：分词结果
    '''
    word_cut=[]
    for k in range(len(wordlist)):
        for n in range(m,0,-1):
            wordmatch1 = sentence[0:n]
            i=0
            while (i<len(wordlist)):
                if wordmatch1 == wordlist[i]:
                    wordlist_1th = wordmatch1
                    break
                else:
                    wordlist_1th=''
                i+=1
            if len(wordlist_1th)>0:
                break
        if len(wordlist_1th)>0:
            word_cut.append(wordlist_1th)
            sentence=sentence[len(wordlist_1th):]
    return(word_cut)    
```

分词结果：
```python
['我', '想', '成为', '一名', '数据', '科学家']
```


后向最大匹配：
```python
def backward_max_match(sentence,wordlist,m):
    '''
    功能：通过后向最大匹配的算法对句子进行分词
    输入：sentence需要分词的句子、wordlist需要参考的词典、m最大长度
    输出：分词结果
    '''
    word_cut=[]
    for k in range(len(wordlist)):
        for n in range(m,0,-1):
            wordmatch1 = sentence[-n:]
            i=0
            while (i<len(wordlist)):
                if wordmatch1 == wordlist[i]:
                    wordlist_1th = wordmatch1
                    break
                else:
                    wordlist_1th=''
                i+=1
            if len(wordlist_1th)>0:
                break
        if len(wordlist_1th)>0:
            word_cut.append(wordlist_1th)
            sentence=sentence[:-len(wordlist_1th)]
    return(word_cut)    
```

分词结果：
```python
['科学家', '数据', '一名', '成为', '想', '我']
```

#### 参考贪心学院的课程，最大匹配算法存在一些缺点：

- 细分（有可能更好，如果我们词典中用到词“科学”，是无法细分到的，因为匹配到科学家就停止了）

- 局部最优，因为这是一种贪心算法

- 效率较低，用了多层循环

- 不考虑语义，词与词之间没有关系

#### 所以需要进一步优化算法，考虑语义需要语言模型

