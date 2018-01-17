title: xgboost 常用参数
date: 2017-03-23 17:08:31
categories: 机器学习
tags: XGBoost
---
### [XGBoost 的参数][1]
#### 一般参数
1. booster[default = gbtree],使用哪种基分类器，可选：gblinear,dart
2. silent[default = 0],0表示输出运行信息，1表示不输出
3. nthread, 使用多少线程运行xgboost
4. num_pbuffer,xgboost自动设置
5. num_feature,自动设置，取决于读入样本特征的最大值

#### Tree booster的参数
1. **eta[default = 0.3,alias: learn_rate]**,缩减系数，范围[0-1]
2. **gamma[default = 0,alias:min_split_loss]**
    分裂节点的损失减小值只有大于等于gamma节点才分裂，gamma值越大，算法越保守，越不容易过拟合，但性能就不一定能保证，所以要平衡
3. **max_depth[default = 6],**树的最大深度，增大深度容易过拟合
4. **min_child_weight[default = 1]**,最小样本权重和（每个样本的权重等于其对应的二阶导），如果新分裂的节点的样本权重和小于min_child_weight则停止分裂。range:[0,无穷大]
5. **max_delta_step[default = 0]**,待定
6. **subsample[default = 1]**,每棵树对训练集的采样率，如果设为0.5，则每训练一棵树都随机选择一半的训练集训练。
7. **colsample_bytree[default = 1]**,刚开始构建树时对特征的采样率
8. **colsample_bylevel[default = 1]**,在构建每一层节点时列的采样率
9. **lambda[default = 1]**,L2范数正则项系数
10. **alpha[default = 0]**,L1正则项系数
11. **tree_method,string[default='auto']**
    choices:{'auto','exact','approx'}
12. **sketch_eps[default = 0.03]**,近似直方图算法中分桶的参数，仅用于近似算法中，一般不用设定
13. **scale_pos_weight[default = 1]**,用于控制正例与负例权重的平衡，对于不平衡数据有用，[具体参见这里][2]

#### Learning Task parameters
学习任务参数：objective[default=reg:linear]
1. "reg:linear",linear regression
2. "reg:logistic",logistic regression
3. "binary:logistic",用于二类问题的logistic regression,输出概率
4. "binary:logitraw",同上，不过输出的是sigmoid function转换之前的score
5. "count:poisson",用于计数问题的泊松回归问题？
6. "multi:softmax",用于多类分类问题，并采用softmax损失函数，需要设定 num_class
7. "multi:softprob",同上，不过输出所有n个类的概率
8. "rank:pairwise",用于排序问题，损失函数为pairwise loss
9. "reg::gamma"
10. base_score[default = 0.5],所有样本的初始预测值，迭代次数多时，该参数影响不大
11. eval_metric,评价准则
    有以下几种：
        1. 'rmse'
        2. 'mae'
        3. 'logloss',  负的对数似然
        4. 'error',二类分类错误率，输出大于0.5时为正例，否则负例
        5. 'error@t',同上，不过指定正负例阈值
        6. 'merror',多类分类错误率
        7. 'mlogloss',多类的logloss
        8. 'auc'
        9. 'ndcg',[Normalized Discounted Cumulative Gain][3]
        10. 'map',[Mean average precision][4]
        11. 等。。
12. seed[default = 0]
13. random number seed


[XGBoost一些样例代码][5]




  [1]: http://xgboost.readthedocs.io/en/latest/parameter.html#general-parameters
  [2]: http://xgboost.readthedocs.io/en/latest/how_to/param_tuning.html
  [3]: https://en.wikipedia.org/wiki/Discounted_cumulative_gain
  [4]: https://en.wikipedia.org/wiki/Information_retrieval#Mean_average_precision
  [5]: https://github.com/dmlc/xgboost/blob/master/demo/README.md#basic-examples-by-tasks