在对文本进行清洗过后，还需要对文本进行一些文本标准化的操作，在很多自然语言中，统一的意思有多种表达方式。参考网上的一篇文章，看看以下的例子：

+ Lisa ate the food and washed the dishes.

+ They were eating noodles at a cafe.

+ Don’t you want to eat before we leave?

+ We have just eaten our breakfast.

+ It also eats fruit and vegetables.

以上的英文表达的句子包含eat的多种形态‘ate’，‘eating’，‘eaten’，‘eats’，其实都是表达同个意思，但是机器无法识别这4个词的含义是一致的？造成信息冗余。所以需要对这些单词进行标准化为根词，在这些英文句子的例子中，就标准化为eat。因为中文不涉及到时态，在这里就不提供标准化的方法。一般文本标准化的方法通过两种方法来实现，为词干化(stemming)和词形还原(lemmatization)。

## 什么是词干化和词性还原？
>词干化和词形还原只是单词的标准化，这意味着将单词缩减为它的根形式。

词干化算法通过从词中剪切后缀或前缀来工作来获取。词形还原是一种更强大的操作，因为它还考虑了词的形态分析。

#### 一般的中文标准化不涉及到词干化，所以词干化的应用场景较少，这里只简单举使用NLTK进行词干化的例子
```python
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize 
from nltk.stem import PorterStemmer

Stem_words = []
ps =PorterStemmer()
for w in filtered_sentence:
    rootWord=ps.stem(w)
    Stem_words.append(rootWord)
print(filtered_sentence)
print(Stem_words)
```
#####词干化前打印结果为：
```python
['He', 'determined', 'drop', 'litigation', 'monastry', ',', 'relinguish', 'claims', 'wood-cuting', 'fishery', 'rihgts', '.', 'He', 'ready', 'becuase', 'rights', 'become', 'much', 'less', 'valuable', ',', 'indeed', 'vaguest', 'idea', 'wood', 'river', 'question', '.']
```

#####词干化后打印结果为：
```python
['He', 'determin', 'drop', 'litig', 'monastri', ',', 'relinguish', 'claim', 'wood-cut', 'fisheri', 'rihgt', '.', 'He', 'readi', 'becuas', 'right', 'becom', 'much', 'less', 'valuabl', ',', 'inde', 'vaguest', 'idea', 'wood', 'river', 'question', '.']
```


#### NLTK词形还原的例子

```python
lemma_word = []
import nltk
from nltk.stem import WordNetLemmatizer
wordnet_lemmatizer = WordNetLemmatizer()
for w in filtered_sentence:
    word1 = wordnet_lemmatizer.lemmatize(w, pos = "n")
    word2 = wordnet_lemmatizer.lemmatize(word1, pos = "v")
    word3 = wordnet_lemmatizer.lemmatize(word2, pos = ("a"))
    lemma_word.append(word3)
print(filtered_sentence)
print(lemma_word)
```

##### 词形还原后打印结果为：
```python
['He', 'determine', 'drop', 'litigation', 'monastry', ',', 'relinguish', 'claim', 'wood-cuting', 'fishery', 'rihgts', '.', 'He', 'ready', 'becuase', 'right', 'become', 'much', 'le', 'valuable', ',', 'indeed', 'vague', 'idea', 'wood', 'river', 'question', '.']
