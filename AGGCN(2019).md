# Attention Guided Graph Convolutional Networks for Relation Extraction
## Abstract
这篇文章提出AGGCN，基于CGCN改进。<br>
创新点：
* 抛弃了复杂的剪枝算法，直接使用完整的依存树作为输入,采用attention机制，让模型自己选择信息
* 实现了在依存树上的并行，不需要对输入进行额外的处理

`note：就是在GCN里面加了注意力（这里用的是transformer的multi-attention），这个玩意是真的好用啊`

## Architecture
![](https://github.com/tangshisong/NRE/blob/master/image/1.png)<br>

* input：n个d维词向量以及依存树的邻接矩阵A
* output：每种关系的概率值
### GCN（这里和CGCN差不多）
第i个节点在第l层的计算式：![](https://latex.codecogs.com/gif.latex?h_%7Bi%7D%5E%7B%28l%29%7D%3D%5Csigma%20%28%5Csum_%7Bj%3D1%7D%5E%7Bn%7D%20A_%7Bij%7D%20W%5E%7B%28l-1%29%7D%20h_%7Bj%7D%5E%7B%28l-1%29%7D%20&amp;plus;%20b%5E%7B%28l-1%29%7D%29)

### Attention Guided Layer
在CGCN的结构中，复杂的剪枝算法不仅操作难度大且效果并不是很优，所以AGGCN采用简单的attention机制，将依存树转换为一个带有权值的全连通图<br>
这里作者采用transformer中的multi-head attention,使得model可以收集来自不同子空间的信息<br>
在Attention层第t个head的权值矩阵:
![](https://latex.codecogs.com/gif.latex?%5Ctilde%7BA%7D%5E%7B%28t%29%7D%3Dsoftmax%28%5Cfrac%7BQW_%7Bi%7D%5E%7BQ%7D%20*%28KW_%7Bi%7D%5E%7BK%7D%29%5E%7BT%7D%7D%7B%5Csqrt%7Bd%7D%7D%29V)<br>
Q和K代表AGGCN的第l-1层的word representation, W是一个d\*d维矩阵超参数,输出的矩阵A代表第t(t<=N)次head<br>

### Densely Connected Layer
Attention Guided Layer输出的是一个n\*n的权值矩阵,在这里使用密集连接以获取更多结构化的信息<br>
因为Attention层产生了N个不同的权值矩阵,所以在密集连接层也需要N个Densely Connected Layer.<br>
为了提高参数的利用效率,对于每一个密集连接层,它有L个sub-layers,每个子层的输入维度为d/L,最后再将子层输入连接起来即可.<br>

将GCN中每个词在不同层的表示向量连接,定义![](https://latex.codecogs.com/gif.latex?g_%7Bj%7D%5E%7B%28l%29%7D%3D%5Bx_%7Bj%7D%3Bh_%7Bj%7D%5E%7B%281%29%7D%3B...%3Bh_%7Bj%7D%5E%7B%28l-1%29%7D%5D)作为节点j初始值(词向量)与节点j在1到l-1层的连接向量<br>

将连接后的word representation与注意力层的各个head的矩阵进行线性运算:
![](https://latex.codecogs.com/gif.latex?h_%7Bt_%7Bi%7D%7D%5E%7B%28l%29%7D%3D%5Csigma%20%28%5Csum_%7Bj%3D1%7D%5E%7Bn%7D%5Ctilde%7BA%7D_%7Bij%7D%5E%7B%28t%29%7D%20W_%7Bt%7D%5E%7B%28l%29%7D%20g_%7Bj%7D%5E%7B%28l%29%7D%20&amp;plus;%20b_%7Bt%7D%5E%7B%28l%29%7D%29)<br>

### Linear Combination
最后使用线性层将N个密集连接层的输出整合:
![](https://latex.codecogs.com/gif.latex?h_%7Bcomb%7D%3DW_%7Bcomb%7Dh_%7Bout%7D&amp;plus;b_%7Bcomb%7D),hout为N个不同的密集连接层的d\*N输出矩阵<br>

### Relation Extraction
最后的操作和CGCN一致,将句子级的representation和实体级的representation连接,一起输入到FFNN中<br>
句子级的representation是来自已经经过mask的线性层的输出矩阵,最后经过一个max-pooling函数转换为向量
将FFNN的输出输入到一个logistics regression classifier得到概率值.




