title: kaggle 入门
date: 2017-03-23 17:10:17
categories: kaggle
tags: kaggle
---
**这里以Kaggle上的一个入门竞赛[House Prices][1] 为例**
### 处理缺失数据和非数值特征
```python
#读入数据
import numpy as np
import pandas as pd

train_data = pd.read_csv('train.csv')
test_data = pd.read_csv('test.csv')
test_ids=  test_data['Id']
#将训练集和测试集一同处理，可以利用train_data.head()方法查看数据前几行，而且还可以查看dataframe的columns names,从而可以选择指定特征列，如下行所示。
all_data = pd.concat((train_data.loc[:,'MSSubClass':'SaleCondition'],test_data.loc[:,'MSSubClass':'SaleCondition']),axis = 0)

#查看所有数值特征
num_feat = all_data.dtypes[all_data.dtypes !='object'].index
#用均值填充空值
all_data[num_feat] = all_data[num_feat].fillna(all_data[num_feat].mean())
#对于非数值特征，使用one-hot-encoding方式转换，对于值为null的非数值特征，转换后为全零向量
all_data = pd.get_dummies(all_data)
```

### 进一步处理
查看目标值分布：
```python
import seaborn as sns
sns.distplot(train_data['SalePrice'])
```
![](http://7xiegr.com1.z0.glb.clouddn.com/1.PNG)
可以看到分布的中心向左偏斜,对其取对数得到
```python
sns.distplot(np.log1p(train_data['SalePrice']))
```
![](http://7xiegr.com1.z0.glb.clouddn.com/2.PNG)
经过对数转换的数值明显对称了许多，对称与不对称这个指标可以使用 scipy.stats 中的skew函数来计算
```python
from scipy.stats import skew
print(skew(train_data['SalePrice']))
print(skew(np.log1p(train_data['SalePrice'])))
#输出分别为1.8809 0.1212，数值越大，偏斜越大
train_data['SalePrice'] = np.log1p(train_data['SalePrice'])
```

同理，偏斜指标也可以应用到特征中，也可以对偏斜的特征做对数变换
```python
skew_val = all_data[num_feat].apply(lambda x:skew(x))
skew_feat = skew_val[skew_val > 0.75].index #计算偏度大于给定阈值的特征,对这些特征做对数变换
all_data[skew_feat] = np.log1p(all_data[skew_feat])
x_train = all_data.iloc[:train_data.shape[0],:]
y_train = train_data['SalePrice']
x_test = all_data.iloc[train_data.shape[0]:,:]
```
### 选择模型训练与预测
这里使用adaboost模型来训练
```python
import xgboost as xgb
from sklearn.cross_validation import KFold
from sklearn.grid_search import ParameterGrid
from sklearn.metrics import  mean_squared_error
kfold = KFold(x_train.shape[0],5)

param_grid = {'silent':[1],
              'nthread':[2],
              'eval_metric':['rmse'],
              'eta':[0.03],
              'objective':['reg:linear'],
              'max_depth':[5,7],
              'num_round':[500],
              'subsample':[0.5,0.6,0.7],
              'colsample_bytree':[0.5,0.7]}

min_score = 10000
for params in ParameterGrid(param_grid):
    print(params)
    total_score = []
    for train_index,test_index in kfold:
        train_x_train,test_x_train = x_train.iloc[train_index,:],x_train.iloc[test_index,:]
        train_y_train,test_y_train = y_train[train_index],y_train[test_index]
        xgb_train = xgb.DMatrix(train_x_train,label = train_y_train)
        xgb_test =  xgb.DMatrix(test_x_train,label = test_y_train)
        watchlist = [('train',xgb_train),('test',xgb_test)]
        model = xgb.train(params,200,watchlist,early_stopping_rounds = 20)
        y_preds = model.predict(xgb_test)
        total_score.append(np.sqrt(mean_squared_error(y_preds,test_y_train)))
    if(min_score > np.mean(total_score)):
        min_score = np.mean(total_score)
        best_params = params
        best_rounds=  model.best_iteration
#0.1345

xgb_test_data = xgb.DMatrix(x_test)
xgb_train_data = xgb.DMatrix(x_train,label = y_train)
wathchlist=  [('train',xgb_train_data)]
new_model = xgb.train(best_params,xgb_train_data,best_rounds,watchlist)
pred_test = np.expm1(new_model.predict(xgb_test_data))

#submission
summ = pd.DataFrame({'Id':test_ids,'SalePrice':pred_test})
summ.to_csv('mysumm_adaboost.csv',index = False)

```
![](http://7xiegr.com1.z0.glb.clouddn.com/3.PNG)
上图是我提交的结果，下面比较好的结果是直接运行讨论版里一个使用[Lasso做出来的结果][2]，0.13的结果是使用上面adaboost代码跑出来的结果，数据预处理两个方法是类似的，问题在于adaboost的参数可能没调好，而且adaboost可能过拟合了。

下一步，研究[Crowdflower Search Results Relevance][3]竞赛第一名的[解决方案][4].
以及[Kaggle 入门指南][5]。


  [1]: https://www.kaggle.com/c/house-prices-advanced-regression-techniques
  [2]: https://www.kaggle.com/guosch/house-prices-advanced-regression-techniques/regularized-linear-models
  [3]: https://www.kaggle.com/c/crowdflower-search-relevance
  [4]: https://github.com/ChenglongChen/Kaggle_CrowdFlower
  [5]: https://zhuanlan.zhihu.com/p/25742261