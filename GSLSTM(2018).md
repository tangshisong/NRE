# N-ary Relation Extraction using Graph State LSTM
## Abstract
本文提出新的跨句子的n元关系抽取，目前的方法是将document graph转换为两个DAG（有向无环图）然后输入LSTM中进行处理。
但是DAG-LSTM将语句分割，会丢失重要信息。所以作者提出GS-LSTM，一种基于有向图状态的并行处理每个词。<br>
创新点：
* 相比DAG-LSTM，GS-LSTM完全采用图表示，保证了语义完整性。
* 当前状态的词可以利用图结构吸收与它邻接的词信息
* 实现了并行计算
