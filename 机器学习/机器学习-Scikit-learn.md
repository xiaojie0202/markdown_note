# 机器学习概述
    机器学习是从数据中自动分析获得规律（模型），并利用规律对未知数据进行预测
## 机器学习-数据
    在机器学习中最首要的东西当然是数据了
1. 数据来源
>- 企业的日积月累的大数据， 政府掌握的各种数据，科研机构的实验数据
2. 数据类型
>- 离散型数据
>>- 由记录不同类别个体的数目所得到的数据，又称计数数据，所
	有这些数据全部都是整数，而且不能再细分，也不能进一步提高他
	们的精确度。

>- 连续型数据
>>- 变量可以在某个范围内取任一数，即变量的取值可以是连续
	的，如，长度、时间、质量值等，这类整数通常是非整数，含有小数
	部分。
>- 注：只要记住一点，离散型是区间内不可分，连续型是区间内可分
3. 可用数据集
>- scikit-learn
>>- 链接: http://scikit-learn.org/stable/datasets/index.html#datasets
>>- 数据量较小， 方便学习
>- UCI
>>- http://archive.ics.uci.edu/ml/
>>- 收录了360个数据集， 覆盖科学，生活，经济等领域， 数据量几十万
>- Kaggle特点
>- https://www.kaggle.com/datasets
>>- 大数据竞赛平台，80万科学家，真实数据， 数据量巨大
4. 常用数据的结构组成
>- 结构: 特征值+目标值
# 机器学习-scikit-learn数据集
    scikit-learn 内置了一些数据集，可以直接拿来用， 还有一些通过网络获取的数据集
## scikit-learn获取数据集-API
    sklearn.datasets
>- sklearn.datasets.load_*()
>>- 获取小规模数据集，数据包含在scikit-lenarn模块datasets目录里

>- datasets.fetch_*(data_home=None)
>>- 获取大规模数据集，需要从网络上下载，函数的第一个参数是data_home，表示数据集下载的目录,默认是 ~/scikit_learn_data/

## load*和fetch*返回的数据类型datasets.base.Bunch(字典格式)
>- data：特征数据数组，是 [n_samples * n_features] 的二维 numpy.ndarray 数组
>- target：标签数组，是 n_samples 的一维 numpy.ndarray 数组
>- DESCR：数据描述
>- feature_names：特征名,新闻数据，手写数字、回归数据集没有
>- target_names：标签名,回归数据集没有
### 实例
1. 爱丽丝(分类)
```python
from sklearn.datasets import load_iris
import pandas as pd

data = load_iris()
print(data.data)  # 150行，4列特征(花萼长度， 花萼宽度， 花瓣长度， 花瓣宽度)
print(data.feature_names)  # 4列特征(花萼长度， 花萼宽度， 花瓣长度， 花瓣宽度)
print(data.target_names)  # ['setosa' 'versicolor' 'virginica']， 花的名称
print(data.target)  # 标签名称， 目标值
# 构建数据集
dd = pd.DataFrame(data.data, index=data.target, columns=data.feature_names)
```

# 机器学习-scikit-learn数据集划分
    机器学习一般的数据集会划分为两个部分：
    训练数据：用于训练，构建模型
    测试数据：在模型检验时使用，用于评估模型是否有效
## scikit-learn数据集划分-API
    sklearn.model_selection.train_test_split
1. 参数和返回值
>- sklearn.model_selection.train_test_split(数据集， 标签名称， 测试集百分比)
>- 返回值: 训练集特征值，测试集特征值， 训练集目标值，测试集目标值
2. 实例
```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split

data = load_iris()

x_train, x_test, y_train, y_test = train_test_split(data.data, data.target, test_size=0.25)
'''
参数, 数据集， 标签名称， 测试集百分比
返回值：训练集特征值，测试集特征值， 训练集目标值，测试集目标值
'''
print('训练集特征值:%s, 训练集目标值:%s' % (x_train, y_train))
print('测试集特征值：%s, 测试集目标值:%s' % (x_train, x_test))
```
# 机器学习-数据的特征工程
    特征工程是将原始数据转换为更好地代表预测模型的潜在问题的特征的过程，从而提高了对未知数据的模型准确性
## 特征工程-特征抽取
    特征抽取就是把文本，字符串转换成机器机器认识的的array数组，我们使用sklean工具
### sklean特征抽取API： sklearn.feature_extraction
    将<特征-值>映射转化为向量。字典类型的对象转化为numpy.array或者scipy.sparse
    特征值为string类型时，向量表示为one-hot二元编码，出现的string为1，其余为0.
    特征值为int等数字类型时，对应的值为相应的数字。
### 字典特征抽取
    sklearn.feature_extraction.DictVectorizer(dtype=np.float64, separator="=", sparse=True, sort=True)
1. 参数
>- dtype: 特征值的类型。传递给Numpy / scipy数组。稀疏矩阵构造函数作为dtype参数。
>- separator: 在构造用于一次热编码的新特性时使用的分隔符字符串。
>- sparse: 布尔型， 是否转换位sparse矩阵
>- sort: 在匹配时是否应该对feature_names_和vocabulary ary_进行排序。默认为True。
2. 方法
>- DictVectorizer.fit_transform(X)
>>- X: 字典或者包含字典的迭代器, 返回sparse矩阵
>- DictVectorizer.inverse_transform(X)
>>- X:array数组或者sparse矩阵 返回值:转换之前数据格式
>- DictVectorizer.get_feature_names()
>>- 类别返回名称
>- DictVectorizer.fit(X)
>- X数据集
>- DictVectorizer.transform(X)
>>- 按照原先的标准转换
3. 实列
```python

from sklearn.feature_extraction import DictVectorizer

# 实例化自动特产抽取 
dict = DictVectorizer(sparse=False, separator='=')
# 三个样本， 城市名称， 空气质量
data = dict.fit_transform([{'city': '北京', 'temperature': 100},
                    {'city': '上海', 'temperature': 60},
                    {'city': '深圳', 'temperature': 30}]
                   )
print(dict.get_feature_names())  # ['city=上海', 'city=北京', 'city=深圳', 'temperature'] 中间‘=’号是 separator参数
print(data)
'''
['city=上海', 'city=北京', 'city=深圳', 'temperature']
[[  0.   1.   0. 100.]
 [  1.   0.   0.  60.]
 [  0.   0.   1.  30.]]
'''
print(dict.inverse_transform(data)) # [{'city=北京': 1.0, 'temperature': 100.0}, {'city=上海': 1.0, 'temperature': 60.0}, {'city=深圳': 1.0, 'temperature': 30.0}]
```
### 文本特征抽取-统计文本出现次数
    sklearn.feature_extraction.text.CountVectorizer(input='content', encoding='utf-8',decode_error='strict', strip_accents=None, lowercase=True, preprocessor=None, tokenizer=None, stop_words=None, token_pattern=r"(?u)\b\w\w+\b",ngram_range=(1, 1), analyzer='word',max_df=1.0, min_df=1, max_features=None,vocabulary=None, binary=False, dtype=np.int64)
1. 参数
>- input ：string {‘filename’, ‘file’, ‘content’}
>>- 如果是filename，那么传递到函数中的应该是一系列要分析的文件，通过读取文件的原始内容进行分析, 如果是file，那么file应该支持读，来获取文件的字节内容, 否则，输入的应该是可以直接分析的字符串或者字节项
>- encoding : string, ‘utf-8’ by default.
>>- 如果需要分析字节或者文件，这里的encoding是用来解码的
>- decode_error. {‘strict’, ‘ignore’, ‘replace’}
>>- 在给定的内容里遇到不能识别的字符时的处理方式，默认的是strict，意思是如果解码错误就汇报。其他的可选值为ignore’ 和‘replace’
>- strip_accents : {‘ascii’, ‘unicode’, None}
>>- 去掉口音（这里我觉得应该是‘特殊字符’），ascii是一个对字符进行快速映射的方法。unicode是一种较慢的方法，但是可以对任何字符使用
>- analyzer : string, {‘word’, ‘char’, ‘char_wb’} or callable
>>- 特征是否应该转化为单词或者n-grams（一种简化单词的方式，可以将注入book和books综合成一个单次），选项‘char_wb’仅将文档中的单词表示成为n-grams的形式, 如果一个用来提取特征的可调用函数，被传递进来，那么就按照被传递的函数进行处理
>- preprocessor : callable or None (default)
>>- 改写预测处理的函数，同时保留标记和n-grams的处理过程
>- tokenizer : callable or None (default)
>>- 改写字符串标记步骤，同时保留预处理和n-grams的步骤
>- ngram_range : tuple (min_n, max_n)
>>- 对于不同的n-grams的提取，n-value变化的上界和下界，所有的min_n <= n <= max_n 的n值都会被使用
>- stop_words : string {‘english’}, list, or None (default)
>>- 如果是字符串，那么它将被传递给_check_stop_list，并且返回停用列表，现在只支持英文的停用词列表, 如果是列表，那么这个列表将被认为包含了停用词，这些词将被从文档中的单词删除掉, 如果是none，那么就不使用停用词，max_df可以被设置成在[0.7，1.0]之间的值，来自动的检测和过滤掉基于其语料库的停用词
>- lowercase : boolean, default True
>>- 将所有的字符转为小写形式
>- token_pattern : string
>>- 构成token的正则表达式，它仅仅在tokenize == ‘word’的时候才使用。默认的正则表达式是选择两个或者两个以上的字符（标点符号是被忽略的，仅仅当作token的分割符）
>- max_df : float in range [0.0, 1.0] or int, optional, 1.0 by default
>>- 当构建字典的时候，忽略词频明显高于给的那个的阈值（语料库的停用词），如果是浮点数，那么参数代表了一个文档的比例，当是整数的时候，代表的是计数值。当字典不是none的时候，这个参数会被忽略
>- min_df : float in range [0.0, 1.0] or int, optional, 1 by default
>>- 当构建忽略词的字典的时候，这些忽略词要求要严格的低于给定的阈值。这个值在书中称之为截至值，如果是浮点数，那么代表的是文档中的比例，如果是整数那么就是绝对计数值。如果字典不是空的话，这个参数被忽略
>- max_features : optional, None by default
>>- 如果不是none，构建词典的时候仅仅考虑语料库里词频最高的那些特征，如果词典非空，那么这个参数被忽略
>- vocabulary : Mapping or iterable, optional
>>- 是以词为键，并且值可以在特征矩阵里可以索引的，或者是一个单词的迭代（译注：这里不是很清楚是什么意思），如果词典不是给定的，那么将会从输入的文档库中选择
>- binary : boolean, False by default.
>>- 如果是真，那么所有非零的计数将被设置成1。这对于离散的只针对二值事件而非整数计数的概率模型很有用
>- dtype : type, optional
>>- 通过fit_transform或者transform处理后返回的矩阵类型
2. 方法
>- CountVectorizer.fit_transform(X,y)
>>- X:文本或者包含文本字符串的可迭代对象, 返回sparse矩阵
>- CountVectorizer.inverse_transform(X)
>>- X:array数组或者sparse矩阵, 转换之前数据格式
>- CountVectorizer.get_feature_names()
>>- 返回值:单词列表
3. 实例
```python
from sklearn.feature_extraction.text import CountVectorizer

cv = CountVectorizer()
data = cv.fit_transform(["life is is short,i like python", "life is too long,i dislike python"])
'''
CountVectorizer执行流程
1. 统计所有文章的所有词汇，重复的只看作一次，单个字母不统计
2. 对每篇文章，在词的列表里进行统计每个词语出现次数
'''
print(cv.get_feature_names()) # ['dislike', 'is', 'life', 'like', 'long', 'python', 'short', 'too']
print(data.toarray())
'''
['dislike', 'is', 'life', 'like', 'long', 'python', 'short', 'too']
[[0 2 1 1 0 1 1 0] # 每个列表代表一篇文章
 [1 1 1 0 1 1 0 1]]
'''
```
4. 实例 - 处理中文文章(使用jieba分词)
```python
from sklearn.feature_extraction.text import CountVectorizer
import jieba
cv = CountVectorizer()
data = cv.fit_transform([' '.join(list(jieba.cut('人生苦短，我用pyton'))), ' '.join(list(jieba.cut('路漫漫其修远，我用pyton')))])
print(cv.get_feature_names()) # ['pyton', '人生', '其修远', '我用', '漫漫', '苦短']
print(data.toarray())
'''
[[1 1 0 1 0 1]
 [1 0 1 1 1 0]]
'''
``` 
### 文本特征抽取-统计词汇的重要性
    sklearn.feature_extraction.text.TfidfVectorizer(nput='content', encoding='utf-8',
                                         decode_error='strict', strip_accents=None, lowercase=True,
                                         preprocessor=None, tokenizer=None, analyzer='word',
                                         stop_words=None, token_pattern=r"(?u)\b\w\w+\b",
                                         ngram_range=(1, 1), max_df=1.0, min_df=1,
                                         max_features=None, vocabulary=None, binary=False,
                                         dtype=np.int64, norm='l2', use_idf=True, smooth_idf=True,
                                         sublinear_tf=False)
1. 算法分析
>- TF(t) = (词t在文档中出现的总次数) / (文档的词总数).
>- ID(t) = log_e(总文档数/词t出现的文档数)
>- TF-IDF = TF*IDF
2. 参数
>- input ：string {‘filename’, ‘file’, ‘content’}
>>- 如果是filename，那么传递到函数中的应该是一系列要分析的文件，通过读取文件的原始内容进行分析, 如果是file，那么file应该支持读，来获取文件的字节内容, 否则，输入的应该是可以直接分析的字符串或者字节项
>- encoding : string, ‘utf-8’ by default.
>>- 如果需要分析字节或者文件，这里的encoding是用来解码的
>- decode_error. {‘strict’, ‘ignore’, ‘replace’}
>>- 在给定的内容里遇到不能识别的字符时的处理方式，默认的是strict，意思是如果解码错误就汇报。其他的可选值为ignore’ 和‘replace’
>- strip_accents : {‘ascii’, ‘unicode’, None}
>>- 去掉口音（这里我觉得应该是‘特殊字符’），ascii是一个对字符进行快速映射的方法。unicode是一种较慢的方法，但是可以对任何字符使用
>- analyzer : string, {‘word’, ‘char’, ‘char_wb’} or callable
>>- 特征是否应该转化为单词或者n-grams（一种简化单词的方式，可以将注入book和books综合成一个单次），选项‘char_wb’仅将文档中的单词表示成为n-grams的形式, 如果一个用来提取特征的可调用函数，被传递进来，那么就按照被传递的函数进行处理
>- preprocessor : callable or None (default)
>>- 改写预测处理的函数，同时保留标记和n-grams的处理过程
>- tokenizer : callable or None (default)
>>- 改写字符串标记步骤，同时保留预处理和n-grams的步骤
>- ngram_range : tuple (min_n, max_n)
>>- 对于不同的n-grams的提取，n-value变化的上界和下界，所有的min_n <= n <= max_n 的n值都会被使用
>- stop_words : string {‘english’}, list, or None (default)
>>- 如果是字符串，那么它将被传递给_check_stop_list，并且返回停用列表，现在只支持英文的停用词列表, 如果是列表，那么这个列表将被认为包含了停用词，这些词将被从文档中的单词删除掉, 如果是none，那么就不使用停用词，max_df可以被设置成在[0.7，1.0]之间的值，来自动的检测和过滤掉基于其语料库的停用词
>- lowercase : boolean, default True
>>- 将所有的字符转为小写形式
>- token_pattern : string
>>- 构成token的正则表达式，它仅仅在tokenize == ‘word’的时候才使用。默认的正则表达式是选择两个或者两个以上的字符（标点符号是被忽略的，仅仅当作token的分割符）
>- max_df : float in range [0.0, 1.0] or int, optional, 1.0 by default
>>- 当构建字典的时候，忽略词频明显高于给的那个的阈值（语料库的停用词），如果是浮点数，那么参数代表了一个文档的比例，当是整数的时候，代表的是计数值。当字典不是none的时候，这个参数会被忽略
>- min_df : float in range [0.0, 1.0] or int, optional, 1 by default
>>- 当构建忽略词的字典的时候，这些忽略词要求要严格的低于给定的阈值。这个值在书中称之为截至值，如果是浮点数，那么代表的是文档中的比例，如果是整数那么就是绝对计数值。如果字典不是空的话，这个参数被忽略
>- max_features : optional, None by default
>>- 如果不是none，构建词典的时候仅仅考虑语料库里词频最高的那些特征，如果词典非空，那么这个参数被忽略
>- vocabulary : Mapping or iterable, optional
>>- 是以词为键，并且值可以在特征矩阵里可以索引的，或者是一个单词的迭代（译注：这里不是很清楚是什么意思），如果词典不是给定的，那么将会从输入的文档库中选择
>- binary : boolean, False by default.
>>- 如果是真，那么所有非零的计数将被设置成1。这对于离散的只针对二值事件而非整数计数的概率模型很有用
>- dtype : type, optional
>>- 通过fit_transform或者transform处理后返回的矩阵类型
>- norm : ‘l1’, ‘l2’ or None, optional
>>- 用于规范化术语向量的规范。None没有规范化。
>- use_idf : boolean, default=True
>>- 启用逆文档频率重新加权
>- smooth_idf : boolean, default=True
>>- 通过将文档频率添加一个平滑的idf权重，就好像一个额外的文档被看到包含一个集合中的每个术语一次。防止零分。
>- sublinear_tf : boolean, default=False
>>- 应用子线性tf缩放，即用1 + log（tf）替换tf。
3. 方法
>- build_analyzer（）	返回处理预处理和标记化的可调用
>- build_preprocessor（）	返回一个函数，以便在标记化之前对文本进行预处理
>- build_tokenizer（）	返回一个将字符串分成令牌序列的函数
>- decode（DOC）	将输入解码为一串Unicode码元
>- fit（raw_documents [，y]）	从训练集学习词汇和idf。
>- fit_transform（raw_documents [，y]）	学习词汇和idf，返回术语文档矩阵。
>- get_feature_names（）	从特征整数索引到特征名称的数组映射
>- get_params（[深]）	获取此估计器的参数。
>- get_stop_words（）	构建或获取有效的停止词列表
>- inverse_transform（X）	每个文档在X中返回非零条目的条款。
>- set_params（\ * \ * PARAMS）	设置该估计器的参数。
>- transform（raw_documents [，copy]）	将文档转换为文档术语矩阵。
4. 实列
```python
from sklearn.feature_extraction.text import TfidfVectorizer
import jieba
tf = TfidfVectorizer()
data = tf.fit_transform([' '.join(list(jieba.cut('人生苦短，我用python'))), ' '.join(list(jieba.cut('路漫漫其修远，我用python')))])
print(tf.get_feature_names()) # ['pyton', '人生', '其修远', '我用', '漫漫', '苦短']
print(data.toarray())
'''
[[0.40993715 0.57615236 0.         0.40993715 0.         0.57615236]
 [0.40993715 0.         0.57615236 0.40993715 0.57615236 0.        ]]
'''
```
## 特征工程-特征数据预处理
    通过特定的统计方法（数学方法）将数据转换成算法要求的数据
### sklearn特征数据处理API：sklearn.preprocessing
### 归一化
    sklearn.preprocessing.MinMaxScaler(feature_range=(0, 1), copy=True)
1. 参数
>- feature_range: tuple (min, max), default=(0, 1)
>>- 期望的转换数据范围。
>- copy : boolean, optional, default True
>>- 设置为False以执行inplace行规范化并避免复制(如果输入已经是numpy数组)。
2. 方法
>- MinMaxScalar.fit_transform(X)
>>- X:numpy array格式的数据[n_samples,n_features] 返回转换后的形状相同的array

3. 算法分析
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/5FC6B5E85E2A4620A489CC1F684511E1/7583)
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/0830149A835B4271AF7175375E441E52/7585)
4. 实例 
```python
from sklearn.preprocessing import MinMaxScaler

mc = MinMaxScaler()
data = mc.fit_transform([[90, 2, 10, 40], [60, 4, 15, 45], [75, 3, 13, 46]])
print(data)
'''
[[1.         0.         0.         0.        ]
 [0.         1.         1.         0.83333333]
 [0.5        0.5        0.6        1.        ]]
'''
```
5. 归一化算法总结

如果出现异常点，影响了最大值和最小值，那么结果显然会发生改变

最大值与最小值非常容易受异常点影响，所以归一化算法鲁棒性较差，只适合传统精确小数据场景。
### 标准化
    scikit-learn.preprocessing.StandardScaler(copy=True, with_mean=True, with_std=True)
1. 参数
>- copy boolean, optional, default True
>>- copy 如果为false,就会用归一化的值替代原来的值;如果被标准化的数据不是np.array或scipy.sparse CSR matrix, 原来的数据还是被copy而不是被替代
>- with_mean : boolean, True by default
>>- 在处理sparse CSR或者 CSC matrices 一定要设置False不然会超内存
>- with_std : boolean, True by default
>>-
2. 属性和方法
>- scale_： 缩放比例，同时也是标准差
>- mean_： 每个特征的平均值
>- var_:每个特征的方差
>- n_sample_seen_:样本数量，可以通过patial_fit 增加

>- fit(X,y=None):计算输入数据各特征的平均值，标准差已经之后的缩放系数 
>>- X 为array 或者 稀疏矩阵，[样本数量，样本特征数] 
>>- y: 传入为了使得和Pipeline兼容

>- fit_transform(X,y=None,**fit_params): 通过fit_params调整数据X,y得到一个调整后的X 
>>- X 为array：训练集 
>>- y 为标签 

>- get_params(deep=True): 返回StandardScaler对象的设置参数，接上文

>- inverse_transform(X,copy=None)：顾名思义，就是按照缩放规律反向还原当前数据 
>>- 默认copy 为None, 是否copy X 
>>- 这个copy暂时没有试出区别
>- partial_fit(X, y=None): 在线计算平均值和标准差。适合X作为连续的数据流输入。 
>>- 相关文献“Algorithms for computing the sample variance: Analysis and recommendations.” The American Statistician 37.3 (1983): 242-247:

>- transform(X, y=’deprecated’, copy=None):基于现有的对象规则，标准化新的参数
3. 算法分析

![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/E1B13AC29AFE4BE386B6E924D6EC0655/7587)
![](https://note.youdao.com/yws/public/resource/4d9f2b353221035aa2dc87736c84e628/xmlnote/4E92C0ECCD6B4F6E8B1D07BB6434D01C/7589)
4. 实例
```python
from sklearn.preprocessing import StandardScaler

a = StandardScaler()
data = a.fit_transform([[1, 0, 5], [-1, 5, 8], [2, 3, 6]])
print(data)
'''
[[ 0.26726124 -1.29777137 -1.06904497]
 [-1.33630621  1.13554995  1.33630621]
 [ 1.06904497  0.16222142 -0.26726124]]
'''

```
5. 总结
    
在已有样本足够多的情况下比较稳定，适合现代嘈杂大数据场景。
### 处理数据中的缺失值
       sklearn.preprocessing.Imputer(missing_values="NaN", strategy="mean", axis=0, verbose=0, copy=True)
1. 参数
>- missing_values
>>- 缺失值，可以为整数或NaN(缺失值numpy.nan用字符串‘NaN’表示)，默认为NaN
>- strategy
>>- 替换策略，字符串，默认用均值‘mean’替换
>>>- ①若为mean时，用特征列的均值替换
>>- ②若为median时，用特征列的中位数替换
>>>- ③若为most_frequent时，用特征列的众数替换
>- axis
>>- 指定轴数，默认axis=0代表列，axis=1代表行
>- copy
>>- 设置为True代表不在原数据集上修改，设置为False时，就地修改，存在如下情况时，即使设置为False时，也不会就地修改
>- fit_transform(X,y)       
2. 方法
>>- X:numpy array格式的数据[n_samples,n_features] 返回转换后的形状相同的array
3. 实例
```python
from sklearn.preprocessing import Imputer
import numpy as np

mi = Imputer(missing_values='NaN', strategy='mean', axis=0)

data = mi.fit_transform([[1,2,3],[np.nan, 5,6], [7,8,9]])
print(data)
'''
[[1. 2. 3.]
 [4. 5. 6.]
 [7. 8. 9.]]
'''
```
## 特征工程-特征选择
    特征选择就是单纯地从提取到的所有特征中选择部分特征作为训练集特征，
    特征在选择前和选择后可以改变值、也不改变值，但是选择后的特征维数肯
    定比选择前小，毕竟我们只选择了其中的一部分特征。
1. 主要方法（三大武器）：
Filter(方差过滤式):VarianceThreshold
Embedded(嵌入式)：正则化、决策树
Wrapper(包裹式)
### 特征选择-方差过滤式
    sklearn.feature_selection.VarianceThreshold(threshold=0.)
1. 参数
>- threshold : float, optional
>>- 方差, 会删除方差小于此参数的列
2. 方法
>- fit_transform(X,y)       
X:numpy array格式的数据[n_samples,n_features]
返回值：训练集差异低于threshold的特征将被删除。
默认值是保留所有非零方差特征，即删除所有样本
中具有相同值的特征。

3. 原理分析
这应该是最简单的特征选择方法了：假设某特征的特征值只有0和1，并且在所有输入样本中，95%的实例的该特征取值都是1，那就可以认为这个特征作用不大。如果100%都是1，那这个特征就没意义了。当特征值都是离散型变量的时候这种方法才能用，如果是连续型变量，就需要将连续变量离散化之后才能用，而且实际当中，一般不太会有95%以上都取某个值的特征存在，所以这种方法虽然简单但是不太好用。可以把它作为特征选择的预处理，先去掉那些取值变化小的特征，然后再从接下来提到的的特征选择方法中选择合适的进行进一步的特征选择。

4. 实例
```python
from sklearn.feature_selection import VarianceThreshold
var = VarianceThreshold(threshold=0)
data = var.fit_transform([[0, 2, 0, 3], [0, 1, 4, 3], [0, 1, 1, 3]])
print(data)
'''
[[2 0]
 [1 4]
 [1 1]]
'''
```
## 特征工程-数据降维
    sklearn. decomposition 
### 数据降维-PCA(主成分分析)
    sklearn. decomposition.PCA(n_components=None, copy=True, whiten=False,svd_solver='auto', tol=0.0, iterated_power='auto',random_state=None)
>- 本质：PCA是一种分析、简化数据集的技术
>- 目的：是数据维数压缩，尽可能降低原数据的维数（复杂度），损失少量信息。
>- 作用：可以削减回归分析或者聚类分析中特征的数量
1. 参数
>- n_components:  
>>- 意义：PCA算法中所要保留的主成分个数n，也即保留下来的特征个数n
>>- 类型：int 或者 string，缺省时默认为None，所有成分被保留。
          赋值为int，比如n_components=1，将把原始数据降到一个维度。
          赋值为string，比如n_components='mle'，将自动选取特征个数n，使得满足所要求的方差百分比。
>- copy:
>>- 类型：bool，True或者False，缺省时默认为True。
>>- 意义：表示是否在运行算法时，将原始训练数据复制一份。若为True，则运行PCA算法后，原始训练数据的值不            会有任何改变，因为是在原始数据的副本上进行运算；若为False，则运行PCA算法后，原始训练数据的              值会改，因为是在原始数据上进行降维计算。

>- whiten:
>>- 类型：bool，缺省时默认为False
>>- 意义：白化，使得每个特征具有相同的方差。关于“白化”，可参考：Ufldl教程

2. 属性和方法
>- components_ ：返回具有最大方差的成分。
>- explained_variance_ratio_：返回 所保留的n个成分各自的方差百分比。
>- n_components_：返回所保留的成分个数n。

>- fit(X,y=None)
>>- fit()可以说是scikit-learn中通用的方法，每个需要训练的算法都会有fit()方法，它其实就是算法中的“训练”这一步骤。因为PCA是无监督学习算法，此处y自然等于None。fit(X)，表示用数据X来训练PCA模型。
>>- 函数返回值：调用fit方法的对象本身。比如pca.fit(X)，表示用X对pca这个对象进行训练。

>- fit_transform(X)
>>- 用X来训练PCA模型，同时返回降维后的数据。 newX=pca.fit_transform(X)，newX就是降维后的数据。

>- inverse_transform()
>>- 将降维后的数据转换成原始数据，X=pca.inverse_transform(newX)

>- transform(X)
>>- 将数据X转换成降维后的数据。当模型训练好后，对于新输入的数据，都可以用transform方法来降维。
3. 算法分析

PCA主要是用来数据降维，将高纬度的特征映射到低维度的特征，加快机器学习的速度。比如在三维空间里面有一个薄饼状的三维物体，由于薄饼的厚度相对于薄饼的直径要小很多很多，故我们在平时的时候就说这个薄饼是圆的，而不说它的是圆柱的，这就是无意识中把描述薄饼的特征从三维降到二维了。
4. 实例
```python
from sklearn.decomposition import PCA
pca = PCA(n_components=0.95)
data = pca.fit_transform([[2, 8, 4, 5], [6, 3, 0, 8], [5, 4, 9, 1]])
print(data)
'''[[ 1.28620952e-15  3.82970843e+00]
 [ 5.74456265e+00 -1.91485422e+00]
 [-5.74456265e+00 -1.91485422e+00]]
'''
```
    
    