# Readme

## 前言

由于当时编写的时候没有注意写好文档，现在重新维护已经非常困难，此处只做留档处理。剩下的Readme内容是过程中的做的一些记录。



以下对项目代码进行介绍

* 标题是每个代码文件的名字
* 介绍基本按照项目的运行顺序

## 1. Depression_process

### I.模块功能

对Depression数据集中的训练集进行处理，得到用于训练2中模型的数据**depression_glove_42B_300d.pkl**。

#### 输入

* 词向量模型
* 训练数据

#### 输出

* embeding层参数
* 用于2中深度神经网络训练的数据**depression_glove_42B_300d.pkl**

### II.步骤：

* 合并1-10所有阶段的数据。
* 数据预处理
  * 去除文章中的标点，用空格分隔所有单词。
  * 然后，统计每一篇文章的长度，并得到文章长度中位数。
  * 使用中位数对所有文章进行截取，不足则用特定标识补充
* 统计所有文章，制作字典——每个单词有唯一的索引
* 将文章中所有单词，替换成对应索引
* 综合使用glove和制作的字典，得到单词对应的索引到单词对应词向量的映射
* 最后，将上述数据和映射持久化

## 2. Article_cnn

### I.模块功能

根据数据**depression_glove_42B_300d.pkl**，训练模型，并保存模型。

#### 输入

* 数据**depression_glove_42B_300d.pkl**
  * embeding层参数
  * 训练数据


#### 输出

* 对Depression数据进行二分类的模型（卷积层+线性层）model_cnn.pkl

### II.步骤：

* 写module.nn的子类，做好自定义的模型
* 加载**depression_glove_42B_300d.pkl**的数据
* 训练网络
* 持久化模型

## 3. Article_cnn_feature

### I.模块功能

使用**model_cnn.pkl**模型中的卷积层对数据进行处理，生成xgboost训练集

#### 输入

* **model_cnn.pkl**
* **depression_glove_42B_300d.pkl**中训练数据

#### 输出

* 经卷积层处理后的训练数据**feature.csv，labels.csv**

### II.步骤：

* 加载**model_cnn.pkl**和**depression_glove_42B_300d.pkl**
* 取出**model_cnn.pkl**到卷积层之前（包括卷积层）的网络序列S
* 用S处理**depression_glove_42B_300d.pkl**，得到处理后的数据D_cov
* 对D_cov使用最大池化，得到D_pol（128维的特征向量）
* 最后持久化得到**feature.csv，labels.csv**

## 4.XGBoost_training

### I.模块功能

用卷积层处理过的数据训练xgboost，并进行评估

#### 输入

* **feature.csv，labels.csv**

#### 输出

* 无输出

### II.步骤：

* 加载**feature.csv，labels.csv**
* 网格搜索调参
* 训练xgboost
* 评估



## 4plus. cnn_xgboost_training_pure

### I.模块功能

用卷积层处理过的数据训练xgboost，并进行评估，与4不同是没有调参的部分

#### 输入

* **feature.csv，labels.csv**

#### 输出

* 无输出

### II.步骤：

* 加载**feature.csv，labels.csv**
* 训练xgboost
* 评估





