title: kaggle经验
date: 2017-06-24 14:24:29
categories: kaggle
tags: kaggle
---

最近参加了kaggle的一个比赛，看了好多关于数据处理，特征分析以及集成学习的一些内容，这些都是数据挖掘大神总结出来的经验，在这里总结一下我的收获，以及谈一下我自己的一些想法。

参加数据挖掘比赛一般要从以下几个方面着手：
### 数据探索与预处理
所谓数据探索就是利用各种图表直观地观察各种分布情况，从而为接下来的数据预处理提供一个预处理的方向。
1. 首先分析数据，统计各个类型特征的个数，以及统计缺失值情况
    ```python
    import pandas as pd
    df_train = pd.read_csv('../input/train.csv')
    # distinguish numerical attributes and object attributes and target column
    dtype_df = df_train.dtypes.reset_index()
    dtype_df.columns = ["Count", "Column Type"]
    summary_dtypes = dtype_df.groupby("Column Type").aggregate('count').reset_index()
    print(summary_dtypes)
    
    #样例输出
    #  Column Type  Count
    #0       int64    369
    #1     float64      1
    #2      object      8
    
    target = ['y']
    columns = df_train.columns
    num_feats = df_train.dtypes.index[df_train.dtypes!='object']
    cate_feats = df_train.dtypes.index[df_train.dtypes=='object']
    num_feats = list(set(num_feats)-set(target))
    #输出所有数值型特征和离散型特征
    print('numerical features:')
    print(num_feats)
    print('categorical features:')
    print(cate_feats)
    print('target column:')
    print(target)
    
    #统计缺失值
    # check if there are any missing values
    print('the following numerical features contains null value')
    for column in num_feats:
        if(np.sum(df_train[column].isnull())!=0):
            print(column)
    print('the following categorical features contains null value')
    for column in cate_feats:
        if(np.sum(df_train[column].isnull()!=0)):
            print(column)
    # fill the missing values with mean or median , all depends on the distribution of the corresponding variable
    missing_df = df_train.isnull().sum(axis=0).reset_index()
    missing_df.columns = ['column_name', 'missing_count']
    missing_df = missing_df.ix[missing_df['missing_count']>0]
    missing_df = missing_df.sort_values(by='missing_count')
    missing_df
    ```
2.  目标变量的分布情况
    ```python
    import seaborn as sns
    plt.figure(figsize = (8,6))
    tar_val = np.sort(df_train[target].values)
    sns.distplot(tar_val,bins=50,kde=False)
    plt.xlabel('target value',fontsize=12)
    ```
    如果目标变量为连续值且其值域范围较大，可以考虑对其做对数变换，并用变换后的值建模。
    如果目标变量为离散值且正负样本不均衡，可以考虑下采样或这上采样，可以采用对不平衡数据友好的auc等指标指导模型选择，而且再划分训练集和验证集的使用要注意各个类的样本再训练集或验证集中要分布均匀，可以使用sklearn中的StratifiedKFold函数
    ```python
    from sklearn.model_selection import StratifiedKFold
    kf = StratifiedKFold(n_splits = 3)
    for train_index,test_index in kf.split(train_X):
        pass
    ```
3.  各个特征变量的分布
    
       特征变量为连续值：如果为长尾分布并且考虑使用线性模型，需要对变量进行对数变换
    
       特征变量为离散值：观察每个离散值的频率分布，对出现频率较低的变量统一归为“其他类”，之后进行one-hot 编码或采用数值编码
       数值编码可以采用LabelEncoder，one-hot encoder也有对应的函数
    
       如何处理缺失值？
       特征为连续值：如果分布为偏正态分布，可以用均值填充，如果为长尾分布，一般用中值，避免受outlier的影响。
       特征为离散值：用众数填充。
    
       ```python
       from sklearn.preprocessing import LabelEncoder
       lbl_enc = LabelEncoder()
       lbl_enc.fit(list(df_train[column_name].values))
       df_train[column_name] = lbl_enc.transform(list(df_train[column_name].values))
       # 貌似如果要使用OneHotEncoder函数需要保证column的值是数值，所以需要先用labelencoder预处理一下
       from sklearn.preprocessing import OneHotEncoder
       ohe = OneHotEncoder()
       ohe.fit(df_train[column_name])
       ohe_features = ohe.transform(df_train[column_name])
    
       ```
       我一般使用pandas内置的函数做one-hot编码：
    
       ```python
        # ...one hot encoding of categorical variables
        categorical =  ["X0", "X1", "X2", "X3", "X4", "X5", "X6", "X8"]
        for f in categorical:
            dummies = pd.get_dummies(all_df[f], prefix = f, prefix_sep = '_')
            all_df = pd.concat([all_df, dummies], axis = 1)
       ```
4.  各个特征与标号地分布情况
    对于categorical features 一般采用box-plot分析，对于数值型特征一般采用scatter或者直接plot
    box-plot 代码如下：
    
    ```python
    var_name = "X6"
    col_order = np.sort(df_train[var_name].unique()).tolist()
    plt.figure(figsize=(12,8))
    sns.boxplot(x=var_name, y='y', data=df_train, order=col_order)
    plt.xlabel(var_name, fontsize=12)
    plt.ylabel('y', fontsize=12)
    plt.title("Distribution of y variable with "+var_name, fontsize=15)
    plt.show()
    ```
5.  特征两两之间的分布与相关度
可以有助于发现高相关和共线性的特征

### 数据清洗
1. 合并文件
2. 缺失值处理
3. 离群点处理
4. 特征编码

这个部分其实基本上在第一部分数据探索与预处理中已经提到了，主要是因为数据探索主要也是探索需要处理的特征，所以数据清洗与数据探索与预处理是有很大一部分耦合的。

### 特征工程
坊间传言，特征决定了效果的上限，而算法只是再不断地逼近这个上限，这充分说明了特征在数据挖掘比赛中的重要性。
特征工程按照我自己的理解主要是与领域知识相关的，我觉得可以把它分为三个部分：
1. 特征构建
   特征构建就是根据原始的数据构建出可以用来训练模型的数据，这主要与参赛者的领域知识密切相关，比如在自然语言处理中常用的tf-idf特征，参赛者对相关领域的了解程度直接决定了构建的特征好与坏。
2. 特征抽取
   一般是指用PCA,LDA,ICA,SVD等方法自动抽取出有物理意义的特征，这些抽取出的特征一般都是直接与原始特征stacking到一起，而不是要舍弃原始特征。
```python
from sklearn.decomposition import PCA
pca = PCA(n_component = 12)
pca.fit(df_train)
pca_comp = pca.transform(df_train)
```
3. 特征选择
Lasso，ElasticNet等模型能起到特征选择的作用，只保留重要的特征，而基于决策树的一些方法如RandomForest,xgboost等也能够根据构建决策树的过程中各个特征的贡献度给出各个特征的打分，可以根据打分判断各个特征的重要性。当然，也可以使用卡方检验来对特征的重要度排序。根据特征的重要程度选择相对有用的特征用于建模。



### 模型选择

对于稀疏型数据（如文本特征，one-hot的ID特征），一般采用线性模型。 Random Forest 和 GBDT等树模型不太适用于稀疏的特征，但可以先对特征降维(PCA,LDA,SVD/LSA)，再使用这些特征。稀疏数据直接输入DNN会导致网络weight过多，不利于优化， 也可以考虑先降维，或者对ID类特征只用embedding的方式。

对于稠密型特征，一般使用xgboost建模

数据中既有稀疏特征，又有稠密特征，可以考虑使用线性模型对稀疏数据进行建模，将其输出与稠密特征一起再输入到XGboost等模型中。

如果要使用线性模型，一般要先标准化输入特征，因为各个特征的尺度可能不一样，标准化能归一化到同一尺度，从而避免某个特征占主导地位，而且对于采用梯度下降优化算法的算法还能加速收敛。

kaggle大神Abhishek在他的博客中给出了常用的数据挖掘算法并给出了各个算法需要调整的参数及其范围：

Classification:

Random Forest，
GBM，
Logistic Regression，
Naive Bayes，
Support Vector Machines，
k-Nearest Neighbors

Regression：

Random Forest，
GBM，
Linear Regression，
Ridge，
Lasso，
SVR

![](http://7xiegr.com1.z0.glb.clouddn.com/AAEAAQAAAAAAAAilAAAAJDhiNGE5YTVmLTJhNTAtNGNkZS1hNDAwLTY5YTJiMTE1ZmI3Zg.png)
### 模型融合
1. Voting 和 Averaging
直接对多个模型的预测结果求平均或者投票。对于目标变量为连续值的任务，使用平均；对于目标变量为离散值的任务，使用投票的方式。
2. Stacking
这里重点介绍stacking

![](http://7xiegr.com1.z0.glb.clouddn.com/stacking.jpg)

stacking与bagging和boosting一样，都属于集成学习的一种，bagging是一种并行的集成学习模式，boosting是一种串行的集成学习模式，而stacking则不同于这两种方法，bagging和boosting都是只有一层的集成学习，而stacking则可以有多层，stacking通过将前一层的输出输入到下一层中，从而组成了一种多层模型的集成学习模式。如上图所示，假设现在要训练第一层模型，具体步骤如下：
    1. 首先将训练集分成5-folds
    2. 在其中4-folds上训练，给出在剩下的一个fold 上的预测值，同时给出测试集的预测值。
    3. 重复步骤2，5次。
    4. 将步骤2中5次的验证集上的预测值组合成一个长的向量，长度为训练集的大小，这个向量就是该算法输入到下一层的输入，同时对步骤2中五次的测试集预测值求平均
    5. 换另一个算法重复步骤1到步骤4
    6. 将所有算法得到的训练集预测值按列组合成一个高为训练集大小，宽度为算法个数的矩阵，该矩阵就是输入到下一层的样本，每一行为一个样本，每一列为一个特征，同时对于测试集上的预测值也组合成一个矩阵，输入到下一层中当测试集。
    （貌似挺拗口）

上面是一种stacking的方法，可以看出每层算法的个数决定了下一层输入样本的特征数，所以算法少的话，下一层的输入特征就变得少了，所以stacking还有其他的变种，其中一种是将上一层的输入与上一层的输出直接按列组合成一个新的矩阵输入到下一层中，比如现在有输入样本 100$\times$300,如果第一层只有一个算法，那么输入到第二层的样本矩阵大小为  100$\times$301，有效避免了特征过少的问题。 结合我们之前所说的，对于稀疏特征常采用线性模型，对于稠密特征采用复杂一点的模型比如xgboost,如果既有稀疏特征又有稠密特征，可以在第一层先使用线性模型对稀疏特征建模，将其输出在第二层与稠密特征组合形成第二层的输入，并在第二层采用xgboost等模型。

### 自动化框架

之前我也曾试过kaggle上的一些入门竞赛，但是没有那么认真做，写完代码，提交完结果之后就没怎么关注过了，所以我的代码没有系统性，也没有做好整理，所以，这次参加比赛的代码又从头开始写了。 。从上面的介绍可以看出，写好一套代码，是可以在多个数据集上重用的，而且，在做特征工程的时候，常常需要不时地添加或删除特征，所以输入数据也在一直变化，如果能够将后面地几个步骤如模型选择，参数调优，模型融合写成一个自动化地框架，那就非常方便了，因为不用每次都要手动调节参数，手动记录结果，大大地节省了时间也提高了效率。目前我主要参考的是kaggle大神 Chenglong Chen在 [Crowdflower](https://github.com/Matafight/Kaggle_CrowdFlower)竞赛中开源的代码，他的代码在模型融合中使用了bagged ensemble selection, 来自于一篇ICML2004的论文[Ensemble
selection from libraries of models](https://scholar.google.com.hk/scholar?q=Ensemble+selection+from+libraries+of+models&hl=zh-CN&as_sdt=0,5&as_vis=1)。另一个同样来自Chenglong Chen的开源代码[HomeDepot](https://github.com/Matafight/Kaggle_HomeDepot)中采用了stacking的模型融合方式，看来单一的模型融合方式不能适用于多种任务，还是要针对具体任务具体分析，多尝试，从而得到相对好的结果。

## 参考
[https://www.linkedin.com/pulse/approaching-almost-any-machine-learning-problem-abhishek-thakur](https://www.linkedin.com/pulse/approaching-almost-any-machine-learning-problem-abhishek-thakur)
[https://zhuanlan.zhihu.com/p/26820998](https://zhuanlan.zhihu.com/p/26820998)



