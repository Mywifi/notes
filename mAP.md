# 目标检测中的指标：Accuracy, Precision, Recall, AP, mAP

1. Accuracy（准确率）
定义： 准确率是指模型预测正确的样本占总样本数的比例。它是用来衡量模型整体预测正确性的指标。
其实就是考虑全部样本，测对的占多少。
计算方法： 
 \text{Accuracy} = \frac{\text{TP} + \text{TN}}{\text{TP} + \text{FP} + \text{FN} + \text{TN}} 
 
其中： 
TP (True Positive): 预测为正例并且实际上是正例的数量，真阳性
TN (True Negative): 预测为负例并且实际上是负例的数量，真阴性
FP (False Positive): 预测为正例但实际上是负例的数量，假阳性（实际上是阴性）
FN (False Negative): 预测为负例但实际上是正例的数量，假阴性（实际上是阳性）
Positive/Negative表示预测结果，True/False表示预测的结果是否正确

2. Precision（精确率）
定义： 精确率是指在所有预测为正例的样本中，实际为正例的比例。它是用来衡量预测为正例的样本中有多少是真正的正例。
其实就是考虑阳性样本，真阳性占多少。
计算方法： 
 \text{Precision} = \frac{\text{TP}}{\text{TP} + \text{FP}} 

3. Recall（召回率）
定义： 召回率是指在所有实际为正例的样本中，被正确预测为正例的比例。它是用来衡量模型能够正确识别出的正例样本的比例。
其实就是考虑实际上为真阳性的，有多少被测出来，也叫测全率。
计算方法： 
 \text{Recall} = \frac{\text{TP}}{\text{TP} + \text{FN}} 
 
4. Average Precision (AP)
定义： 平均精度（AP）是在排序的预测结果中，计算不同召回率水平下的精确率的平均值。它主要用于评估模型在特定类别的表现。
计算方法： AP的计算通常涉及到绘制精确率-召回率曲线（Precision-Recall Curve），并在曲线下方的面积进行积分。具体来说，可以计算在不同召回率水平下对应的精确率，并取这些精确率的平均值。
其实就是设置IoU在0.5以上的认为是TP，然后取不同的Confidence（预测框的置信度，是某类别的概率）值，会得到一系列的Precision-Recall值，绘制成PR曲线，然后计算PR曲线下的面积：
Recall= \sum_{i=1}^{Rank} (Recall_i-Recall_{i-1})\times max(Precision_{i,...,Rank})

5. Mean Average Precision (mAP)
定义： 平均精度均值（mAP）是在多个类别的AP基础上计算出来的平均值。它用于评估多类别分类或多标签分类任务中模型的整体性能。
计算方法：  \text{mAP} = \frac{1}{nc} \sum_{i=1}^{nc} \text{AP}_i  其中  nc  是类别总数， AP_i  是第  i  类别的平均精度。

6. MS COCO评价指标
[https://cocodataset.org/#detection-eval](https://cocodataset.org/#detection-eval)

COCO中的AP实际上是mAP50-95

参考：

[MS COCO数据集的评价标准以及不同指标的选择推荐（AP、mAP、MS COCO、AR、@、0.5、0.75、1、目标检测、评价指标）_ms coco dataset-CSDN博客](https://blog.csdn.net/weixin_44878336/article/details/134030021)
