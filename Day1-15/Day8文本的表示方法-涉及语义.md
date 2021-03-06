上节提到，one-hot、词袋模型、TF-IDF算法等文本表示方法都存在共同的缺点为无法理解语言。通过这些方法表达出的词向量在计算出相似度之后，意思相关相似的词无法表现出更高。
#### 主要有两个缺点：

>1.没有语义相似度

>2.sparsity

为克服以上的缺点，提出了词向量的分布式表示。
### Distributed representation词向量表示

分布式表示（distributed representation），描述的是把文本分散嵌入到另一个空间，一般从是从高维空间嵌入到低维空间。如何在低维空间表达一个词呢？目前流行的是通过矩阵降维或神经网络降维将语义分散存储到向量的各个维度中，这两类方法得到的向量空间是低维的一般都可以称作分布式表示，又称为词嵌入（word embedding）或词向量）。
这种方法为深度学习在NLP领域的一个应用。

#### 基本思路

分布式表示的方法基本假设为：上下文相似的词，其语义也相似。而基本思路是通过训练将每个词映射成一个固定长度的短向量，所有这些向量就构成一个词向量空间，每一个向量可视为该空间上的一个点。此时向量长度可以自由选择，与词典规模无关。所以方法的核心是上下文的表示以及上下文与目标词之间的关系的建立。

知乎上有比较通俗的理解，对“宝马”、“奔驰”两种车进行向量表达，引入其中一种表示<汽车，奢侈品，动物，动作，美食>。这个表示怎么得到？可以通过根据相似的上下文（word2vec，即NN）。例如宝马和奔驰的上下文都经常出现汽车，奢侈品，动物，动作，美食这几个词。那么就可以以这几个词作为向量表示。

#### Word2Vec

针对分布式表示的方法distributed representation的一种实现模型为Word2Vec

### python实现
#### 模型训练
```python
common_texts = [['今天', '明天', '上班'],
 ['上班', '注意', '疫情', '防控'],
 ['下班', '注意', '疫情', '防控'],
 ['今天', '明天', '下班'],
 ['下班', '需要', '注意', '疫情'],
 ['疫情', '注意', '卫生'],
 ['卫生','疫情'],
 ['疫情', '卫生'],
 ['今天', '明天', '卫生'],
 ['今天', '明天', '疫情']]

from gensim.models import Word2Vec
train_model = Word2Vec(common_texts, size=100, window=5, min_count=1, workers=4)
train_model.save('./MyModel')
```
#### 模型
```python
model = Word2Vec.load('./MyModel')
# 对于训练好的模型，我们可以通过下面这前三行代码来查看词表中的词，频度，以及索引位置， 
# 最关键的是可以通过第四行代码判断模型中是否存在这个词
for key in model.wv.vocab:
    print(key)
    print(model.wv.vocab[key])
print('human' in model.wv.vocab)
print(len(model.wv.vocab)) #获取词表中的总词数
```


#### 获取对应的词向量及维度
```python
for key in model.wv.vocab:
    print(key)
    print(model[key])
```

#### 计算两个词的相似度（余弦距离）
```python
model = Word2Vec.load('./MyModel')
print('今天明天的形似度',model.wv.similarity('今天', '明天'))
print('疫情明天的形似度',model.wv.similarity('疫情', '明天'))
```

#### 最后输出，说明今天与明天的相似度更高

>今天与明天的形似度 0.51175606

>疫情与明天的形似度 -0.006288953

在代码中调用word2vec模型时，所设参数size代表的为词向量的维度。

