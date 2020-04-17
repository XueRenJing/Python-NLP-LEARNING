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

#### 生成这些词之后，接下来就是过滤的过程，这里就需要考虑到语言模型，在针对我们输入错误的词，推断出最有可能正确的词。其实就是最大化以下的公式，c为正确的词，w为错误的词：
> P(c|w) 
#### 根据贝叶斯定理：
> P(c|w) = P(w|c) * P(c) / P(w)
#### 对于所有生成的编辑距离不超过2备选的正确的词c来说，对应的都是同一个错误的词w，所以它们的 P(w) 是相同的，因此其实可以化简为求：
> P(w|c) * P(c) 

#### 以上的P(c）就是正确的候选词出现的概率，用频率近似，等于词出现的次数除以所有词的总次数。因为所有词的总次数是固定的，所以可以以词的频次来代替。
#### P(w|c)指的是试图找到正确的词确出现错误的词的概率，需要统计数据支持，为了化简问题我们选出编辑距离最近的词，这些错误的词的概率最高。例如，相差一个字母的拼法就比相差两个字母的拼法发生的概率高，例如我想拼写something,直观上来说，那么错误拼写成somethinn的可能性就比somethiee的可能性更高。
#### 所以，要实现P(w|c) * P(c) ，我们找到与输入单词越相近的词，并找到其中出现频率最高的词。

而再回顾上面生成编辑距离不超过2的词，有些词并不一定是拼写正确的词，为了提高效率，先过滤其中不正确的词，所以把create_dist12_word函数改为know_create_dist1_word
create_dist12_word改为know_create_dist1_word

```python
'''    
生成编辑距离为2的单词可以在编辑距离为1的单词基础上生成但要在词典中
'''
def know_create_dist1_word(str,NWORDS):
    
    '''
    功能：对输入的单词产生跟它编辑距离为1的单词
    输入：字符串str、拼写正确的词的词典
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

    dist_word1 = [w for w in dist_word1 if w in NWORDS]
    return(dist_word1)

def know_create_dist2_word(str,NWORDS):
    '''
    功能：对输入的单词产生跟它编辑距离为2的单词
    输入：字符串str、拼写正确的词的词典
    输出：输出编辑距离为1的单词列表
    '''
    dist_word2 = [w2 for w1 in create_dist12_word(str) for w2 in create_dist12_word(w1) if e2 in NWORDS]
    
    return(dist_word2)
```

#### 首先，构建词典，返回词典中每个词对应的词频

```python
import re, collections

text_t = open('big.txt').read()

def words(text): 
    '''
    功能：对文档中的词进行抽取
    输入：文档
    输出：文档中的词
    '''
    return(re.findall('[a-z]+', text.lower()))

words(text_t)

def train(features):
    '''
    计算每个词的出现概率p
    '''
    model = collections.defaultdict(lambda: 1)
    for f in features:
        model[f] += 1
    return model

NWORDS = train(words(text_t))
```

#### 最后find_best()函数，用来从所有备选的词中，根据词频选出用户最可能想要拼写的词。

```python
def find_best(word,NWORDS):
    '''
    功能：对拼错的词根据词典返回正确的词
    输入：拼错的词以及需要的词典
    输出：返回拼写正确的词
    '''
    words = [w for w in [word] if w in NWORDS]
    word_list = words or know_create_dist1_word(word,NWORDS) or know_create_dist2_word(word,NWORDS)
    return(max(word_list,key = NWORDS.get))
```   

#### 以纠正单词sothing为例，运行
```python
find_best('sothing',NWORDS)
```   

#### 结果为nothing，拼写正确：
```python
'nothing'
```   
