# 图(Graphs)的结构

## 什么是图（Why is Networks？）

**网络（Networks）/神经网络图**：偏向于自然存在的图结构

* 社交网络
* 人大脑中的许多许多的神经元就是一个个结点，构成了一个庞大的神经网络
* 互联网

**信息图（Information Graphs）**：偏向于人为概念出来的图结构

* 信息/知识是有组织和联系的
* 场景图表:场景中的对象是如何关联的
* 相似网络:取数据，连接相似点

有时这两者的区别是**模糊**的。

以下是一些网络的例子：

![image-20210107174123585](assets/01-%E4%BB%8B%E7%BB%8D/image-20210107174123585.png)

在许多系统背后都有一个复杂的接线图，一个网络，它定义了组件之间的交互我们永远无法建模和预测这些系统，除非了解他们背后的网络。

**图研究的主要问题**：

1. 图系统是如何组织的，
2. 它们有什么特征？
3. 我们如何利用图的结构特性进行更好的预测。

**图研究的主要驱动原因**：

除非能够理解和了解这些图结构的系统，否则无法对它们进行建模及预测。因此，如何利用图的关系结构来更好地进行**预测**。

**图研究的具体应用的问题**：

* 预测给定节点的类型/颜色：**节点分类**

* 预测两个节点是否链接：**链接预测**

* 找出紧密相连的节点群：**社区检测**

* 度量两个节点/网络的相似性：**网络相似**

**嵌入结点**：

目标：将节点映射到$d$维嵌入，这样具有相似网络邻域的节点就被紧密嵌入在一起

![image-20210107192107362](assets/01-%E4%BB%8B%E7%BB%8D/image-20210107192107362.png)

 

## 图论

[地址](../../机器学习笔记/图论基础.md)

