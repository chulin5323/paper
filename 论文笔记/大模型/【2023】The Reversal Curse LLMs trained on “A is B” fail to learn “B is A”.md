# 【2023】The Reversal Curse: LLMs trained on “A is B” fail to learn “B is A”

[(PDF)](D:\learning\论文\大模型\【2023】The Reversal Curse LLMs trained on “A is B” fail to learn “B is A”.pdf) [(arxiv)](https://arxiv.org/pdf/2309.12288.pdf) [(code)](https://github.com/lukasberglund/reversal_curse)  

> 自回归大语言模型存在逆转诅咒问题（Reversal Curse），大模型在形如“A is B”的句子上进行训练后，在推理时它不会自动地泛化到相反的形式“B is A”，即对“B is A”形式的句子无法进行判断。反转诅咒在模型大小和模型族中都是稳健的，并且不能通过数据增强来缓解。

<img src="图片/The Reversal Curse_1.png" style="zoom: 67%;" />

## 问题

​	当模型在形如“**<name> is <description>**” 的句子上进行训练后，它不会自动地预测相反的方向“**<description> is <name>**”。特别地，当模型在预测时，以“<description>”为条件，那么模型对于"<name>"预测的概率不会高于随机猜测。

​	然而该现象并不能说明LLM不理解逻辑推理，比如像GPT-4这样的LLM，当在其上下文窗口里给出“A is B”的话，它可以完美的推断出“B is A”。（逆转诅咒不适用于上下文学习——in-context learning）

## 实验设计

### 实验1——虚构的name和description

在虚构的人名和描述上**微调**LLM（这些人名和描述不会出现在LLM的训练数据中）。微调“<name> is <description>”，测试“<description> is <name>”。

<img src="图片/The Reversal Curse_2.png" style="zoom:67%;" />

<img src="图片/The Reversal Curse_3.png" style="zoom:67%;" />

### 实验2——真实世界

无任何微调，直接在LLM上进行测试。

<img src="图片/The Reversal Curse_4.png" style="zoom:67%;" />