# GECToR – Grammatical Error Correction: Tag, Not Rewrite

[arxiv](https://arxiv.org/pdf/2005.12592.pdf) [PDF](D:\learning\论文\GEC\【2020 235】GECToR--grammatical error correction tag, not rewrite.pdf)  [code](https://github.com/grammarly/gector) 

> 本文设计了一种自定义的token级别的变换方法，将输入token映射为目标纠正；该模型经过了两个阶段的训练：1）在错误语料预训练和微调，2）在错误和正确语料进行微调。

## 以往工作的缺陷

- NMT方法的推理速度慢
- NMT方法需要大量的训练数据

## 解决的问题

- 使用token级别的变换方法，速度较快
- 设计了三个训练阶段：错误语料数据（合成数据）预训练、错误语料数据微调、正确和错误语料数据微调，提升性能
- 两种变化方法：**basic transformations**和**g-transformations**

## 方法

### token级别变换方法

- Basic transformations：KEEP、DELETE、APPEND、REPLACE
- g-transformations：CASE tags、MERGE tags、SPLIT tags、NOUN NUMBER、VERB FORM

### 预处理方法

- 由于使用了sequence tagging方法，所以需要先将训练集和验证集的目标序列转换为序列标注，其中每一个标注被映射为单个token

    - 1）将原句的每一个token映射为目标句的token子序列：$(x_1,...,x_N)->(y_1,...,y_M)$。对于每一个原句中的token $x_i$，需要在目标中找到最合适的匹配子序列 $\Gamma_i=(y_{j1},...,y_{j2})$，其中，$1\le j1\le j2\le M$。使用的方法是 **Levenshtein distance**。

    > Levenshtein distance，也称为编辑距离（Edit Distance）或莱文斯坦距离，是由俄罗斯科学家弗拉基米尔·莱文斯坦在1965年提出的，用于度量两个序列（字符串）差异大小的方法。该距离定义为：在使用一个单词修改为另一个单词时，通过编辑单个字符（如插入，删除，修改）所需要的最小次数。
    >
    > 具体来说，Levenshtein Distance指两个字符串之间，由一个转换成另一个所需的最少编辑操作次数。允许的编辑操作包括将一个字符替换成另一个字符、插入一个字符、删除一个字符。这个数学公式最终得出的数值就是Levenshtein distance的值。
    >
    > 例如，字符串“kitten”和“sitting”之间的Levenshtein距离为3，因为可以通过以下三个步骤将“kitten”转换为“sitting”：
    >
    > 将“k”替换为“s”（kitten -> sitten）
    > 在“i”前插入“t”（sitten -> sitting）
    > 将“e”替换为“g”（sitting -> sitting）
    > 因此，“kitten”和“sitting”之间的Levenshtein距离为3。Levenshtein distance越小，表示两个字符串越相似。这种距离度量方法在自然语言处理、信息检索、生物信息学等领域有着广泛的应用。
    >
    > 总的来说，Levenshtein distance是一种简单而有效的字符串相似度度量方法，可以帮助我们快速比较两个字符串的相似程度。

    ![](D:\learning\论文\论文笔记\GEC\图片\GECToR – Grammatical Error Correction Tag, Not Rewrite_1.png)

    - 构建原句到目标句的映射关系，并找到对应的transformations操作

        ![](D:\learning\论文\论文笔记\GEC\图片\GECToR – Grammatical Error Correction Tag, Not Rewrite_2.png)

    - 对每个原token，仅保留一种transformation操作（当存在多个transformation时，仅保留第一个不是KEEP的变换）

    ![](D:\learning\论文\论文笔记\GEC\图片\GECToR – Grammatical Error Correction Tag, Not Rewrite_3.png)

### 迭代序列标注

- 由于某些token的纠错可能依赖于其它纠错，所以纠错一次可能不够充分，所以使用了迭代序列纠错的方法，对上一次

修正后的句子再次进行纠错

![](D:\learning\论文\论文笔记\GEC\图片\GECToR – Grammatical Error Correction Tag, Not Rewrite_4.png)

![](D:\learning\论文\论文笔记\GEC\图片\GECToR – Grammatical Error Correction Tag, Not Rewrite_5.png)

### 改进的推理流程

> 引入两个推理超参数，从而获得更精确的纠正。

- positive confidence bias：为**$KEEP标签**添加了一个永久的positive confidence bias（按照某一概率阈值，跳过TN和TP）
- sentence-level minimum error probability：为错误检测层的输出添加了一个最小误差概率阈值

![](D:\learning\论文\论文笔记\GEC\图片\GECToR – Grammatical Error Correction Tag, Not Rewrite_6.png)