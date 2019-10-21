# Attention Guided Graph Convolutional Networks for Relation Extraction
## Abstract
这篇文章提出AGGCN，基于CGCN改进。<br>
创新点：
* 抛弃了复杂的剪枝算法，直接使用完整的依存树作为输入
* 采用attention机制，让模型自己选择信息
* 实现了在依存树上的并行，不需要对输入进行额外的处理

`note：就是在GCN里面加了注意力，这个玩意是真的好用啊`

## Architecture
![](https://github.com/tangshisong/NRE/blob/master/image/1.png)
