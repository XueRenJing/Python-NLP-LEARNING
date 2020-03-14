# Python-NLP-100
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
            '''
            寻找需要匹配的词
            '''
            wordmatch1 = sentence[0:n]
            i=0
    #        循环匹配
            while (i<len(wordlist)):
                if wordmatch1 == wordlist[i]:
                    wordlist_1th = wordmatch1
                    break
                else:
                    wordlist_1th=''
                i+=1
            if len(wordlist_1th)>0:
                break
#        word_cut.append(wordlist_1th)
    #        j等于wordlist长度表示找到最后都找不到，继续找
        if len(wordlist_1th)>0:
            word_cut.append(wordlist_1th)
            sentence=sentence[len(wordlist_1th):]
    return(word_cut)    
```
