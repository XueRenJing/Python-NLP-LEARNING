


#### 例子
```python
sentence = "我想成为一名数据科学家"
wordlist = ["我", "想", "成为", "一名", "数据", "科学家"]    
```

*根据我们的例子进行前向最大匹配算法的说明*

> 一、句子：我想成为一名数据科学家
1 我想成为一 False

2 我想成为 False

3 我想成 False

4 我想 False

5 我 True

>二、句子：想成为一名数据科学家
1 想成为一名 False
2 想成为一 False
3 想成为 False
4 想成 False
5 想 True

>三、句子：成为一名数据科学家
1 成为一名数 False
2 成为一名 False
3 成为一 False
4 成为 True

>四、句子：一名数据科学家
1 一名数据科 False
2 一名数据 False
3 一名数 False
4 一名 False

>五、句子：数据科学家
1 数据科学家 False
2 数据科学 False
3 数据科 False
4 数据 True

>六、句子：科学家
1 科学家 True



#### 所以前向最大匹配分词结果为
```python
["我", "想", "成为", "一名", "数据", "科学家"]    
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
