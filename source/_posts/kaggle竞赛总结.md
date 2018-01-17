title: kaggle竞赛总结
date: 2017-08-05 20:29:11
categories: kaggle
tags: machine learning
---
## 简介
最近刚参加了一个kaggle比赛(Mercedes-Benz Greener Manufacturing),据赛题介绍主要的任务是预测一辆刚出厂的汽车做完检测所需要的时间，从而能够合理安排检测任务，节省能源？创建一个绿色环保的世界？？
我的最终排名是 273/3835,进了top10%，得到了一个Bronze medal,是我得到的第一个奖牌，开心。
在这里总结一下参加这个比赛的收获。

##  特征部分
这个比赛的特征都是经过脱敏的，所以你不知道各个特征的具体意思，这一点对于特征工程来说就比较难受了，这个数据与在Quora比赛中使用的数据类型不同，Quora数据的一个样本是两个句子，任务是判断这两个问题相同或不相同，所以任务的很大一部分都是做特征工程，提取特征，比如两个句子的cos距离，tf-idf特征，pearson相关系数，通过word2vec提取出来的向量等等都可以用作Quora任务的特征，但是，Benz竞赛的数据只是给定的脱敏数据，具体的特征工程需要自己尝试。

这次比赛的重点我放在了模型融合和集成学习方面，所以特征方面探索得比较少，这也是未来需要改进的方向。
我的做法是对于categorical features，直接采用one-hot 编码，对于numerical features,分别使用了PCA,SVD，ICA，随机投影等方法抽取特征，并将抽取出的特征与原有特征连接在一起组成新的特征，之后的模型融合也基于这些新的特征。

后来第二名在讨论区中公布了他的[解决方案](https://www.kaggle.com/c/mercedes-benz-greener-manufacturing/discussion/36390),他通过类似于wrapper的方式做特征选择(不同于filter方式，wrapper的feature subset的好坏由training algorihtm's crossvalid performance决定，而filter方式的feature subset选择与training algorithm 相互独立，subset的选择是通过其他方式),我在这里贴一下他的代码：
```python
from numpy.random import choice
n_elements = train.shape[0]
feats = train.columns.values[9:]
n_feats = len(feats)
weights = np.array(np.zeros(n_feats))
weights += 1/n_feats

best_feats = []
best_score = 0
epochs = 3000    # number of rounds / feature bags
n_features = 10 # try out different values 
wt_g = .2  #weight growth rate
w_threshold = .2  #weight to add feature
kf = KFold(n_elements, n_folds=3, shuffle=True)
y = train['y'].values
scores = []
top_feats = np.array([])
new_feats = np.array([])
for i, ind in enumerate(list(range(epochs))):

    sample_feat_ids = choice(a=n_feats, size=n_features, 
                             replace=False, p=weights)
    sample_feats = np.append(top_feats, feats[sample_feat_ids])
    tst_P = np.array(np.zeros(n_elements))

    for trn, tst in kf: 
        X = train.loc[:,sample_feats]
        trn_X, tst_X = X.iloc[trn,:], X.iloc[tst,:]
        trn_Y, tst_Y = clipped_y[trn], clipped_y[tst]
        #mod = SVR(C=50, epsilon=3, gamma=.2)
        #mod = GBR(alpha=.01, n_estimators=50, max_depth=5, min_samples_leaf=15, subsample=.5, random_state=1776)
        mod = RFR(n_estimators=100, max_depth=12, max_features=20, min_samples_leaf=4, n_jobs=2, random_state=1776)
        mod.fit(trn_X, trn_Y)
        tst_P[tst] = mod.predict(tst_X)

    # I don't want to overfit to outliers so I am clipping all y's at 155
    tst_rsq = r2(y_pred=tst_P, y_true=clipped_y)

    if ind > 29:
        scores.append(tst_rsq)
        ma_rsq = np.mean(scores[-30:])
        if ind % 25 == 0:
            print(ind, ma_rsq, tst_rsq)
        if tst_rsq > ma_rsq:
            weights[sample_feat_ids] *= (1+wt_g)
            sum_w = np.sum(weights)
            weights /= sum_w
            if tst_rsq &gt; best_score:
                best_score = tst_rsq
                best_feats = sample_feats
        else:
            weights[sample_feat_ids] *= (1-wt_g)
            sum_w = np.sum(weights)
            weights /= sum_w
        mx_w = np.max(weights)

        # add feature to the top feats if weight is &gt; threshold
        if mx_w > w_threshold:
            feat_imps = pd.Series(index = feats, data = weights)
            new_feats = feat_imps[feat_imps > w_threshold].index.values
            top_weights = feat_imps[feat_imps > w_threshold].values
            top_feats = np.append(top_feats, new_feats)
            feats = list(feats)
            weights = list(weights)
            for f,w in zip(new_feats,top_weights):
                print(f, w)
                feats.remove(f)
                weights.remove(w)
            n_feats = len(weights)
            feats = np.array(feats)
            weights = np.array(weights)
            sum_w = np.sum(weights)
            weights /= sum_w

        if ind % 25 == 0: 
            print(mx_w)
    else:
        scores.append(tst_rsq)
        print(ind, tst_rsq)

```
不过这种选择方法的时间复杂度还是挺高的。
## 模型选择
我采用了stacking的集成方式(stacking 与 blending的区别在于blending首先将数据划分为两部分，其中一部分用来训练，然后用训练好的模型预测另一部分，将这一部分的预测值当成下一层的输入，而stacking则是通过cross validation的方法预测所有的数据，然后将所有预测值输入到下一层)，blending的缺点是当数据量较少的时候，第二层的训练数据太少，优点是两层数据不相关，避免过拟合效果好，但是由于stacking是采用交叉验证的方式生成预测值，所以也能避免过拟合。

stacking采用了如下子模型：
- RandomForest
- Gbregressor
- Lasso
- ridge
- SVR

stacking的方式可以参考我上一篇博客，代码可以参看我的[github](https://github.com/Matafight/Kaggle)。

我最终的结果还参考了public kernel上另一个比较好的[Solution](https://www.kaggle.com/hakeem/stacked-then-averaged-models-0-5697?scriptVersionId=1236940/code),基本上是将我的预测值与这个kernel的预测值做了一个平均，这也可以看作是一种集成方式吧。

其实我觉得stacking也算是一种黑盒子吧，把那么多方法“堆”在一起，虽然有效果，但也是说不清道不明的原因，有一种说法我觉得还是比较有道理的，第二层的模型相当于对第一层的输出做组合，组合出最好的结果,所以第二层一般采用线性组合。这就是集成的思想。

集成学习最重要的就是保证各个分类器有足够的diversity和accuracy，要有足够的差异性和准确性，不能都是猪队友，也不能都是同一个行业的精英。需要注意的是同一个分类器的不同参数配置也可以看作是不同的模型。







## tips

- kernel和discussion经常会有人提供新的想法和证据来分析这个问题和数据，这是个学习数据处理的好地方。

- 不要太看重public LB，因为最终的结果由private LB决定，一般public LB占总测试集的20%左右，private LB的结果占80%左右，很多情况下会过拟合到public LB上去，此时最好相信本地的local CV score，特别是一些比赛可以通过public LB的反馈来获取public LB所采用测试集的分布和其他信息，比如对于二分类问题，提交全零的结果上去，如果PLB采用的评价准则为01误差，就可以得到PLB测试集的正负样本分布了，知道了样本分布就可以提高PLB上的分数了。但是这个分数对于Private LB没有意义，只会过拟合。

- 怎么根据样本分布提高分数呢？
我们知道对于类别不平衡问题有好几种解决方法。1. 重采样，降采样。2. rescaling，再缩放，比如对于logistic regression，y为样本为正例的概率， y/(1-y)>1时为正例，y/(1-y)<1时为负例，这个结果建立在正负样本平衡的基础上，如果正负不平衡，假设正例个数为m+，负例个数为m-,那么根据观测值得到的阈值为m+/m-， 这里假设观测值为总体值的无偏估计，所谓再缩放就是将1替换为m+/m-，重新判断正负。3.采用auc等对不平衡数据不敏感的度量方式。

- 特征还是比模型更重要的，模型基本上用来用去都是集成,stacking，xgboost等，如何做特征选择，如何分析特征还是最重要的。我这次这方面下的功夫比较少。

- 自动化，自动化，自动化脚本非常重要。

- 在对模型结果做weighted-averaging的时候权重该怎么选择？
   1. 可以根据两个模型的cv score的比值分配权重
   2. 暴力搜索，然后交叉验证，虽然暴力但也是一种方法

