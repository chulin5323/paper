# 【2022 19】SynGEC Syntax-Enhanced Grammatical Error Correction with a Tailored GEC-Oriented Parser

[arxiv](https://arxiv.org/pdf/2210.12484.pdf) [PDF](‪D:\learning\论文\GEC\【2022 19】SynGEC Syntax-Enhanced Grammatical Error Correction with a Tailored GEC-Oriented Parser.pdf) [code](https://github.com/HillZhang1999/SynGEC) 

> 本文提出了SynGEC，能有效地将依存语法信息（dependency syntactic information，[参考连接](https://www.zhihu.com/question/483923820)）融入GEC模型中。现有的句法解析器在处理语法结构不规则的句子时并不可靠，因为本文设计了一个GEC导向的解析器（GOPar），GOPar能从语法不规则的句子中构建语法信息，而构建的语法信息通过图神经网络编码后与Transformer编码器输出的信息进行融合，共同参与Seq2Seq训练。
>
> dependency syntax structure（依存语法结构）：句子中存在主从关系，用箭头表示一个单词与主单词之间的关系；
>
> constituency syntax structure（成分语法结构）：句子中单词表示的成分，比如名词、介词、动词等。

## 以往工作存在的缺陷

- 以往工作将输入序列是为一系列的token，而未考虑到句子本身带有的语法信息
- 现有的句法解析器在处理语法不规则句子时是不可靠的（因为它们在规则语句上进行训练）
- 目前已有工作通过人工注释来构建句子的语法信息，但这些工作并未扩展方法以适应句子中的错误，此外，人工注释的成本也较高

## 解决的问题

- 提出 GECoriented parser (**GOPar**)，其思想是利用并行的**源/目标句子**构建语法构建依存语法信息。

    - 首先使用现有的解析器在目标句子上构建语法信息
    - 然后通过树投射（tree projection）在源句子上构建语法结构树，为了更好地适应句子的语法错误，该过程提出了一个扩展的语法表示方法，以便使用一个统一的树结构来表示**语义错误**和**语法信息**。

    <img src="D:\learning\论文\论文笔记\GEC\图片\SynGEC_1.png" style="zoom: 67%;" />

- 在构建的结构树上对GOPra进行训练，在训练过程中，**GOPar用于生成输入句子的语法信息**。

## 方法

- ### Extended Syntax Representation Scheme

    - 在构建句子依存语法结构的基础上，加入了语法错误（substituted, redundant and missing——S R M）
        - S：包括各种替换操作（spelling errors, tense errors, singular/plural inconsistency errors等）
        - R：表示某个词需要被删除，该冗余词依赖于其右边的词，最后一个词依赖于其左边的词
        - M：表示某个词需要被插入，对缺失词的右侧相邻词分配M标签

- ### Training GOPar

    - 对于句子的错误提取，可以直接使用评价指标工具（如ERRANT等）

![](D:\learning\论文\论文笔记\GEC\图片\SynGEC_2.png)

- ### The DepGCN-based GEC Model

    - Seq2Seq结构，在encoder中加入了GOPar，用于提取输入句子的语法信息，并与encoder输出的语义信息进行加权融合

<img src="D:\learning\论文\论文笔记\GEC\图片\SynGEC_3.png" style="zoom: 80%;" />

