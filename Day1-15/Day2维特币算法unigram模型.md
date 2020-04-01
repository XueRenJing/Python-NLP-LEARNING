![avatar](/Users/betty/Downloads/自然语言处理/贪心学园课程练习/自建课程练习/02维特币算法-改.png)



#### 给个实现例子，sentence为句子，wordlist为词典，logx为概率对数:
```python
'''
例子：我想成为数据科学家
词典：['我','我想','想','成','成为','数','数据',‘科学’,'科学家','家']
概率x：[0.1,0.05,0.1,0.1,0.2,0.2,0.05,0.05,0.05,0.1]
log(x)：[2.3 , 3.  , 2.3 , 2.3 , 1.61, 1.61, 3.  , 3.  , 3.  , 2.3 ]   
'''
sentence = '我想成为数据科学家'
wordlist = ['我','我想','想','成','成为','数','数据','科学','科学家','家']
logx = [2.3 , 3.  , 2.3 , 2.3 , 1.61, 1.61, 3.  , 3.  , 3.  , 2.3 ]

'''
生成状态转移矩阵
'''
matrix = np.zeros((len(sentence)+2,len(sentence)+2))

for i in range(len(transformlist)):
    print(transformlist[i][0])
    print(matrix[transformlist[i][0][0]][transformlist[i][0][1]])
    matrix[transformlist[i][0][0]][transformlist[i][0][1]] = transformlist[i][1]
```
