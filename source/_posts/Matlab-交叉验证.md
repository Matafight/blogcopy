title: "Matlab 交叉验证"
date: 2015-03-08 10:12:51
categories: Matlab
tags: 毕设
---

```matlab
N=size(data,1);
indices=crossvalind('kfold',N,10);
%indices 中是N维向量，对应着N个数据所分配的包，所以范围是1-k
for k=1:10,
	test=(indices==k);
	train=~test;%逻辑非
	train_data=data(train,:);
	test_data=data(test,:);
	%训练并求误差
end
```
