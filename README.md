# 社会背景介绍

随着社会的发展，现在人们的生活水平不断提高，越来越多的家庭拥有了汽车。据公安部统计，2022年全国机动车保有量达4.17亿辆，其中汽车3.19亿辆；机动车驾驶人达5.02亿人，其中汽车驾驶人4.64亿人。疲劳驾驶是诱发交通事故的重要因素，研究如何快速准确地识别出驾驶员的疲劳状态，并在事故发生前进行疲劳预警，对预防疲劳驾驶、促进交通安全具有重要的研究价值和社会意义。德国Daimler-Benz公司的研究结果显示：若在事故发生前0.5 s进行预警，则能够避免50%的碰撞事故，若预警时间提前1 s，则能够避免90%的事故发生。因此，如能对驾驶员的疲劳状态进行实时准确地检测、合理有效的预警，将大大降低疲劳导致的交通事故率，保障行车安全。我国的《道路交通安全法实施条例》指出，连续驾驶机动车超过4 h，中途未停车休息或者停车休息时间少于20 min均属于疲劳驾驶，将对驾驶员进行扣分处理。我们决定在疲劳驾驶检测这一方面做工作，于是实现了疲劳驾驶检测模型。

# 技术背景介绍

1.  基于车辆的方法则通过车辆内部的传感器持续监测线路偏离、方向盘运动和油门踏板压力等指标。这些指标的任何变化超过一定阈值都表明驾驶员疲劳程度增加。尽管这些方法无侵入性且易于记录，但基于车辆的测量存在以下主要问题导致效率低下且判断准确率下降，同时该方法会受到外界天气及路面情况影响，例如强风和路面凹凸不平，可能会干扰检测过程。

2.  生物信号方法使用生理信号如脑电图(EEG)、眼电图(EOG)进行监测和检测。在所有的疲劳检测措施中，EEG检测方法对疲劳措施的检测效果最好且准确性较高。由于其时间分辨率和高灵敏度，EEG适用于疲劳驾驶的检测。然而，尽管基于EEG的方法表现可靠，其侵入性使其在驾驶中不实用，佩戴生物检测设备会对驾驶员的驾驶体验造成较大的影响。

3.  相比之下基于计算机视觉的检测可以在保证检测度的情况下尽可能的减少路况影响和外界因素的干扰，同时是一种无需佩戴式设备的Driver-Friendly的检测方式。

# 指标介绍

驾驶员疲劳通常反映在眨眼的变化上，例如眨眼的持续时间和频率。PERCLOS 是随时间推移眼睑闭合在瞳孔上的百分比，它是眼睛特征嗜睡识别的常用参数：

n是闭眼帧数，N是一段时间内的总帧数。平均而言，大多数人的眨眼频率约为15至20次/分钟，每次眨眼持续0.1至0.4秒。实验表明，正常眨眼持续3到7帧，眨眼频率在5秒内为1-3次。因此选择20%作为正常闪烁（1级）的PERCLOS阈值。根据驾驶员的不同行为设置阈值，将疲劳分为四个等级;t。当疲劳程度超过3时，驾驶员可能会感到困倦或入睡。驾驶员疲劳检测系统会根据疲劳程度给予驾驶员不同的刺激以保持驾驶员的警觉性。

# 模型介绍

我们采用Yolov5作为整个系统的基础，在yolov5模基础上接入一个大小仅为5Mb左右的多模态分类网络实现疲劳驾驶检测的功能，不仅满足实时效率性，还能够充分利用取得的图像特征。此外，由于以Yolov5模型作为基础，我们的系统有很强的拓展性，可以根据不同的需求在此基础上进行功能拓展。

同时，我们对Yolov5模型结构进行了一定的改进，进一步提高模型检测的鲁棒性和准确率。首先，我们改进了特征金字塔网络的结构，利用Bi-FPN替换了原本的FPN，从而增强了不同尺度特征的融合和传递，提高了目标检测性能。其次，我们在基于DarkNet的Backbone基础上，增添了一个SimAM注意力机制，SimAM注意力机制具备自适应选择信息能力，并且计算高效，参数量小，较好地捕捉空间与通道的关系，实现特征信息交互。

要监测驾驶员的疲劳状态，需要对被试者进行精确观察。然而，在涉及到快速动作（如眨眼、眼动和微表情）的应用中，标准成像技术的硬件限制往往会限制性能。因此，在追求性能的同时，确保准确性是一项挑战。

我们提出了一种可以用于疲劳驾驶检测的方案并实现了代码，成功地开发出了客观有效的疲劳驾驶检测网络模型。该模型采用了两个阶段的方法来完成任务。

在第一阶段，我们使用了YOLO（You Only Look Once）目标检测算法来检测由摄像头视频抽帧获得的驾驶员状态图像。这一阶段的目标主要是切分出感兴趣区域（ROI），并准确地检测眼部、嘴巴。通过应用YOLO算法，我们可以准确地定位眼部、嘴巴的位置，从而提取出这些感兴趣区域的图像片段。

在第二阶段，我们使用一个多模态深度学习网络对这些区域进行进一步的特征提取，并将眼睛和嘴巴的特征向量连接，一同输入分类器，对驾驶员状态进行预测，以判断驾驶员是否处于疲劳状态。该多模态深度学习模型能够结合眼睛和嘴巴的特征信息，提供更加精确的判断能力。

为了训练我们的模型，我们收集并自制了一个小型数据集，其中包含了标注了疲劳和非疲劳样本的图像数据（测试集60张，训练集340张）。我们对这个数据集进行了整理和标注，并使用它来训练目标检测器和多模态深度学习模型。

最后，我们对模型进行了评估和改进。我们使用独立的测试数据集对模型进行评估，评估指标可以包括准确度、帧率和模型大小等。通过评估结果，我们可以进一步改进模型的性能和效果。

**整体流程**

1.  相机采集视频

2.  视频抽帧形成Batch

3.  Batch喂入模型

4.  一阶段：使用YOLOV5进行ROI提取

5.  二阶段：将ROI喂入双模态网络

6.  模型输出概率预测结果

7.  记录结果数据并分析是否疲劳

8.  后续硬件设备的相关操作

# 应用价值

当应用于实际企业中，我们开发的疲劳驾驶检测网络模型可以提供以下优势和应用价值：

1.  提高驾驶安全性：疲劳驾驶是道路交通事故的一个主要原因。我们的模型通过实时监测驾驶员的疲劳状态，能够及时预警驾驶员，降低疲劳驾驶引发的事故风险，从而提高驾驶安全性。

2.  自动化检测：我们的模型可以自动化地检测疲劳驾驶状态，无需人工干预。这意味着企业无需额外雇佣人力进行驾驶员监测，减少了人力成本和工作负担。

3.  实时监控：我们的模型能够在实时视频流中进行疲劳驾驶检测，及时发现驾驶员的疲劳状态变化。这对于长时间驾驶、夜间驾驶或需要高度专注的任务非常重要，能够帮助企业及时采取措施，保障驾驶员和车辆的安全。

4.  可定制化和扩展性：我们的模型可以根据企业的需求进行定制化和扩展。可以根据企业的特定要求和驾驶环境进行参数调整和模型优化，以实现更高的准确性和适应性。

5.  数据分析和报告：我们的模型可以收集和分析大量的驾驶数据，并生成详细的报告和统计信息。这些数据和报告可以帮助企业进行驾驶行为分析、驾驶员培训和决策制定，以提高整体的运营效率和安全管理水平。

综上所述，我们的疲劳驾驶检测网络模型为企业提供了一种可靠的解决方案，可以帮助提高驾驶安全性、降低事故风险，并实现自动化检测、实时监控和数据分析等功能。这将有助于企业保障驾驶员和车辆的安全，并提升整体运营效率。

# 方案设计

由于时间和资源的有限性，我们对作品还有一些内容不能完整的补充，以下提出对作品的改善意见：

## 嵌入式系统算子优化

1.  算子级别的优化：除了考虑整体任务的算子分配，还可以对每个算子进行进一步的优化。例如，针对视频抽帧操作，可以使用更高效的算法或技术，如帧间压缩或稀疏采样，以减少计算量和存储需求。对于目标检测和图像分类算子，可以采用轻量级模型或模型剪枝技术，以降低计算复杂度和内存占用，同时保持一定的精度。

2.  硬件加速和优化：除了软件层面的优化，还可以考虑利用硬件加速技术来进一步提高模型的效率。例如，利用专用的神经网络加速器（如GPU、FPGA或ASIC）来加速目标检测和图像分类算子的计算。

## 基于“端-云”协同计算

1.  端计算：在端计算阶段，可以对摄像头采集到的视频进行预处理。其中，除了对视频进行抽帧，只选择关键帧进行处理，以降低数据量和网络传输量。此外，还可以应用一些简单的基础运算，如卷积和图像特征提取，以提取关键信息并减少后续处理的计算量。

2.  云计算：经过端计算的预处理后，只需将数据传输给云端进行目标检测和多模态分类的问题。在云端可以使用更强大的计算资源和深度学习模型来完成这些任务，从而提高检测的准确性和效果。

3.  这种方法具有以下优点和改进：

- 隐私和安全性：通过在嵌入式设备上进行端计算，只需要传输经过预处理的关键数据，可以避免敏感数据在传输过程中被泄露的隐私和安全风险。

- 成本效益：将预处理任务放在嵌入式设备上进行，可以减少云端计算和存储的成本。简单的基础运算和抽帧操作可以在计算资源有限的设备上进行，而更复杂的任务则交由云端完成，充分利用云计算的优势。

- 减少网络传输量和延迟：仅传输预处理后的关键数据到云端，可以大幅降低数据传输量，减少网络带宽压力和传输延迟。这对于实时性要求较高的疲劳驾驶检测系统尤为重要，可以提高系统的响应速度和效率。

- 充分利用边缘端设备算力，同时采用异步IO队列的方式，尽可能的减少网络IO中Packet的大小，提升IO队列的使用效率，提升中心算例的利用率，进而加强了疲劳检测系统的性能表现。

## 增加独立计算单元FPGA硬件设备

- 不难发现整个过程中对图像的提取和模型的前推运算过程中是一个Computation Density Task，因此我们可以考虑单独为已经存在的处理器添加一个独立的计算单元来将计算密集任务分配给FPGA/GPU等可以加速计算且并不会占用主处理器资源的方式将计算任务分配，该方法可以加速在独立于网络云端的嵌入式系统中模型预测的性能。

## 优化模型中ROI的提取算法

我们目前采用的是YOLOV5的方法来获取多模态网络的输入ROI，在后期可以考虑使用模板匹配的算法，降低提取ROI 的算力消耗，提升网络整体的计算速度，进一步降低整个系统对设备算力的要求从而降低实施成本。另一种优化方式为：采用事件相机而非循环抽帧，通过异步的方式减低相机和嵌入式处理器之间的IO消耗。

# 参考文献

\[1\]林学志,汪涵,章力成等.基于端边云架构的疲劳驾驶预警系统设计\[J\].电脑知识与技术,2023,19(26):99-101.DOI:10.14004/j.cnki.ckt.2023.1339.

\[1\]陈见哲.疲劳驾驶检测方法研究进展\[J\].汽车实用技术,2023,48(21):179-186.DOI:10.16638/j.cnki.1671-7988.2023.021.036.

\[1\]尤海娟,伍凌云,王慧宇.面部识别在检测疲劳驾驶的应用研究\[J\].汽车电器,2023(07):22-23+26.DOI:10.13273/j.cnki.qcdq.2023.07.025.

\[1\]王畅,李雷孝,杨艳艳.基于面部多特征融合的疲劳驾驶检测综述\[J\].计算机工程,2023,49(11):1-12.DOI:10.19678/j.issn.1000-3428.0066661.

\[1\]赵洋,陈侃,梅珀彰.基于图像识别技术的疲劳驾驶视觉检测与预警系统设计\[J\].集成电路应用,2023,40(07):142-143.DOI:10.19339/j.issn.1674-2583.2023.07.060.

# 实验部分

## 实验平台

|      |                 |
|------|-----------------|
| 设备 | 型号            |
| CPU  | Intel-i7-11800H |
| GPU  | NVIDIA RTX 3060 |

## 数据集介绍

|            |                   |
|------------|-------------------|
| 数据集部分 | 数据量（张）      |
| 训练集     | 正类160 + 负类160 |
| 测试集     | 正类40 + 负类 40  |

## 实验结果

### 模型大小测试

在采用yolov5作为区域提取器的情况下，分类器的参数大小经过实验得到如下数据：

|                        |              |
|------------------------|--------------|
| 分类器模型             | 模型参数大小 |
| alexNet                | 27.8MB       |
| mobileNetV3-small      | 7.49MB       |
| mobileNetV3-small + SE | 8.23MB       |

### 多个模型对比

|                               |              |        |        |            |
|-------------------------------|--------------|--------|--------|------------|
| 实验模型                      | 实验输入     | Acc    | Loss   | 训练过程图 |
| YOLOv5 + MobileNet_small      | eye + mouth  | 95.00% | 0.012  | To beDone  |
| YOLOv5 + MobileNet_small + SE | eye + mouth  | 95.00% | 0.031  | To beDone  |
| YOLOv5 + MobileNet_Large      | eye + mouth  | 97.50% | 0.039  | To beDone  |
| yolov5 + AlexNet              | eye + mouth  | 98.75% | 0.0041 | To beDone  |
| yolov5 + AlexNet              | face + mouth | 95.00% | 0.0070 | To beDone  |
| yolov5 + AlexNet              | eye + face   | 96.25% | 0.0029 | To beDone  |
| yolov5 + AlexNet + SE         | eye + mouth  | 98.75% | 0.0024 | To beDone  |

### 整体模型数据

| 参数         | 数据               |
|--------------|--------------------|
| 精确率       | 约95%              |
| fps          | 25                 |
| 模型参数大小 | 22.23Mb（8.23+14） |

## 实验配置

|                |                      |
|----------------|----------------------|
| 实验配置       | 信息                 |
| Epoch          | 40                   |
| Lr             | 0.01                 |
| workers number | 4                    |
| batch size     | 4                    |
| Loss Func      | Binary Cross Entropy |
| Optimizer      | SGD                  |
