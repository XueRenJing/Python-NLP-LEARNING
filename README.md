


#### 例子
```python
sentence = "我想成为一名数据科学家"
wordlist = ["我", "想", "成为", "一名", "数据", "科学家"]    
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
