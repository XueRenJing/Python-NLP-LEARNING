在对文本拼写错误纠错之后，需要对文本进行清洗，清洗包括去除无用的标签、特殊符号
以及停用词。清洗之后才能得到一个比较好的模型。


## 什么是停用词？

*在任何自然语言中停用词是最常用的词。为了分析文本数据和构建NLP模型，这些停用词可能对构成文档的意义没有太多价值。*

> 通常，英语文本中的一些常用词是"the"，"is"，"in"，"for"，"where"，"when"，"to"，"at"等，而中文中的一些常用词是"了"，"呢"，"吧"。这是词的使用频率较高，但是没有较大的实际意义。可以作为停用词。

英文中的停用词举例：
>a about after all also always am an and any are at be been being but by came can cant come

#### 停用词存在的意义是什么？为什么删除停用词？什么场景应该删除停用词？

总的来说，删除停用词有以下几大好处：
>1.在删除停用词之后，训练样本减少，提升了训练的速度
2.删除了停用词，留下了一些更有意义的词能提升文本分类的准确率，类似于一般机器学习任务中的去噪声
3.删除了停用词后也能更快速的从数据库中检索数据

是否删除停用词取决于我们的具体场景，如果我们是更关注除了停用词之外的文本本身的含义，这一般是文本分类任务。举个例子，在句子“我们应该为了有更好的工作而努力学习吧”中“更好”、“工作”、“学习”等这些词就比“应该”、“吧”这些语气助词对句子本身的含义表达来说更加重要。
但是在机器翻译等一些场景里面，这些停用词的分析是有一定含义的，这里不继续展开。可以归纳如下：

#####我们可以在以下场景删除停用词：
+ 文本分类
+ 垃圾邮件过滤
+ 语言分类
+ 体裁(Genre)分类
+ 标题生成
+ 自动标记(Auto-Tag)生成
+ 避免删除停用词

#####删除停用词
+ 机器翻译
+ 语言建模
+ 文本摘要
+ 问答(QA)系统

##删除停用词的不同方法
中文的删除停用词的方法，我这里通过自定义函数的方式来实现。对我们已经通过分词手段把句子转化为词语的列表，再进行停用词删除的操作。

```python
def stopwordslist(filepath):
    stopwords = [line.strip() for line in open(filepath, 'r', encoding='utf-8').readlines()]
    return stopwords

def seg_sentence(sentence):
    stopwords = stopwordslist('/Users/betty/Downloads/自然语言处理/贪心学园课程练习/自建课程练习/中文停用词表.txt')  # 这里加载停用词的路径
    outstr = ''
    for word in sentence:
        if word not in stopwords:
            if word != '\t':
                outstr += word
                outstr += " "
    return outstr

seg_sentence(['你','是','科学家','了','吧'])
```
#####输出
```python
'科学家'
```

而英文的删除停用词的工具为nltk、gensim包，针对这些方法截取了网上的一些例子，首先是nltk的例子：

```python
# 导入包
import nltk
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize 
nltk.download('stopwords')
nltk.download('punkt')
set(stopwords.words('english'))

# 例句
text = """He determined to drop his litigation with the monastry, and relinguish his claims to the wood-cuting and 
fishery rihgts at once. He was the more ready to do this becuase the rights had become much less valuable, and he had 
indeed the vaguest idea where the wood and river in question were."""
# 停用词集合
stop_words = set(stopwords.words('english')) 

# 分词
word_tokens = word_tokenize(text) 

filtered_sentence = [] 

for w in word_tokens: 
    if w not in stop_words: 
        filtered_sentence.append(w) 



print("\n\nOriginal Sentence \n\n")
print(" ".join(word_tokens)) 

print("\n\nFiltered Sentence \n\n")
print(" ".join(filtered_sentence)) 
```

#####打印结果为：
```python
Original Sentence 


He determined to drop his litigation with the monastry , and relinguish his claims to the wood-cuting and fishery rihgts at once . He was the more ready to do this becuase the rights had become much less valuable , and he had indeed the vaguest idea where the wood and river in question were .


Filtered Sentence 


He determined drop litigation monastry , relinguish claims wood-cuting fishery rihgts . He ready becuase rights become much less valuable , indeed vaguest idea wood river question .
```

接着是Gensim包的例子,Gensim能够直接在原始文本上进行，无需事先分词：
```python
from gensim.parsing.preprocessing import remove_stopwords

result = remove_stopwords("""He determined to drop his litigation with the monastry, and relinguish his claims to the wood-cuting and fishery rihgts at once. He was the more ready to do this becuase the rights had become much less valuable, 
and he had indeed the vaguest idea where the wood and river in question were.""")

print('\n\n Filtered Sentence \n\n')
print(result)  
```

#####打印结果为：
```python
 Filtered Sentence 

He determined drop litigation monastry, relinguish claims wood-cuting fishery rihgts once. He ready becuase rights valuable, vaguest idea wood river question were.
```
所以以上就是python删除停用词的例子以及实施方式。






