title: "some note about everything"
date: 2015-03-18 22:23:34
categories: 杂记|Matlab
tags:
---
1. npm是node.js 的一个包管理器
2. windows cmd 下切换目录时不能直接切换驱动器，比如说 `cd D:\`是不对的， 直接是`D：`就可以换到D盘，或者是 `cd /d D:`也可以
3. MATLAB find 函数 

 [I,J] = find(X,...) returns the row and column indices instead of
 linear indices into X. This syntax is especially useful when working
 with sparse matrices.  If X is an N-dimensional array where N > 2, then
    J is a linear index over the N-1 trailing dimensions of X.（对于N-1维来说是线性索引）把N-1 维压扁 ==！

```bash
 x=rand(4,4,4)

x(:,:,1) =

    0.2457    0.3803    0.3243    0.9167
    0.4642    0.5737    0.0303    0.0036
    0.5914    0.5610    0.3230    0.2233
    0.1566    0.2534    0.7597    0.3770


x(:,:,2) =

    0.1609    0.3538    0.3369    0.5338
    0.2316    0.2767    0.0435    0.5904
    0.0504    0.0088    0.2802    0.5258
    0.3219    0.9347    0.3948    0.7523


x(:,:,3) =

    0.2162    0.4048    0.5402    0.1208
    0.7479    0.4334    0.9278    0.6576
    0.1362    0.6889    0.6467    0.1966
    0.7265    0.6083    0.5620    0.8226


x(:,:,4) =

    0.7260    0.8630    0.5400    0.1000
    0.8283    0.2061    0.5350    0.0685
    0.4744    0.9978    0.5983    0.0924
    0.5947    0.0654    0.1530    0.0807

>> [i,j]=find(x==max(max(max(x))))

i =

     3


j =

    14
```

把第二维压扁数是第14 个，还有另一种最好的方法就是ind2sub

```matlab 
%求一个三维矩阵最大值的坐标：
a=rand(4,4,4);
ind=max(a(:)); %或者 ind=max(max(max(a)))
[i,j,k]=ind2sub(size(a),ind);
```

4. MATLAB max min 函数

   For vectors, max(X) is the largest element in X. For matrices,
   max(X) is a row vector containing the maximum element from each
   column. For N-D arrays, max(X) operates along the first
   non-singleton dimension.
   矩阵的第一个尺寸不是1的维，比如说a=2*4*2 第一个尺寸不是1的维就是第一维。

```matlab
>> a=rand(2,2,2)

a(:,:,1) =

    0.1200    0.4447
    0.8586    0.8364


a(:,:,2) =

    0.5872    0.6861
    0.5046    0.8075

>> sum(a)

ans(:,:,1) =

    0.9786    1.2811


ans(:,:,2) =

    1.0918    1.4936
```
正常的sum也是按照这个原理，第一个尺寸不是1的维是行，所以是按行加，max min 也是同理

```matlab
max(a,[],1)%从第一维中寻找最大值 等价于 max(a) 找到的最大值是一个行（助记）
max(a,[],2)%从第二维中寻找最大值
``` 
[Y,I]=max(a,[],2) Y返回一列数，分别是每行的最大值（假设a是2*2），I返回的是索引，不过I中存的是列数，即对应的最大值是在第几列。


5. repmat 函数

    1.B = repmat(A,n) returns an array containing n copies of A in the row and column dimensions. The size of B is size(A)*n when A is a matrix.
    
    2.B = repmat(A,r1,...,rN) specifies a list of scalars, r1,..,rN, that describes how copies of A are arranged in each dimension. When A has N dimensions, the size of B is size(A).*[r1...rN]. For example, repmat([1 2; 3 4],2,3) returns a 4-by-6 matrix.
    
    3.B = repmat(A,r) specifies the repetition scheme with row vector r. For example, repmat(A,[2 3]) returns the same result as repmat(A,2,3).

复制矩阵A

6. 求每一行的最大值，并且将不是最大值的元素设为0，保留最大值。
      [Y,I] = max(A, [], 2);
      B = zeros(size(A));
      B(sub2ind(size(A), 1:length(I), I')) = Y;


7. 将空值设为0
	`B(isnan(B)) = 0`

8. bsxfun 函数的强大功能