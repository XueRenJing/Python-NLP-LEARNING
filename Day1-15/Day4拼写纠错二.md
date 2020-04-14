上节提到，遍历整个词典来计算编辑距离，时间复杂度较高。所以就有了新的筛选方法。这里提供一种方法，看下图

![image](https://github.com/XueRenJing/Python-NLP-LEARNING/raw/master/pictures/编辑距离不超过2.png)

针对图中所说生成编辑距离不超过2的词，我们可以先构造其中编辑距离为1的词。怎么生成编辑距离为1的词，最简单的思路就是
在输入的词基础上简单地通过一步的替换、插入、删除所有可能的字母或者汉字的操作。根据这个思路，下面提供英文的生成编辑距离为1
的词的例子。

```python
def create_dist12_word(str):
    '''
    功能：对输入的单词产生跟它编辑距离为1的单词
    输入：字符串str
    输出：输出编辑距离为1的单词列表
    '''
    letters = 'abcdefghijklmnopqrstuvwxyz'
    splits = [(str[:i],str[i:]) for i in range (len(str) + 1)] 
    [L+letter+R for L,R in splits for letter in letters]
    
    #插入操作
    inserts = [L + c + R for L ,R in splits for c in letters]  
    
    #删除操作
    deletes = [L + R[1:] for L,R in splits if R]  
    
    #替换操作
    replaces = [L + c + R[1:] for L ,R in splits for c in letters] 
    
    dist_word1= list(set(inserts+deletes+replaces))
    return(dist1_word)
```

如果需要生成编辑距离为2的单词，可以在编辑距离为1的单词基础上生成

```python
def create_dist2_word(str):
    '''
    功能：对输入的单词产生跟它编辑距离为1的单词
    输入：字符串str
    输出：输出编辑距离为1的单词列表
    '''
    dist_word2 = [w2 for w1 in create_dist12_word(str) for w2 in create_dist12_word(w1)]
    
    return(dist_word2)
```




