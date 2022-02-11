# Yolo笔记

## Yolov3

### 文档

* https://blog.csdn.net/weixin_44791964/article/details/105310627
* https://luckmoonlight.github.io/2018/11/28/yoloV1yolov2yoloV3/
* https://blog.csdn.net/weixin_37598106/article/details/113058426

#### 引言

![image-20220114222147830](https://cdn.jsdelivr.net/gh/TheFoxFairy/ImgStg/202201142225795.png)

总结而言，yolo3不过是把一张图片划分成不同的网格，每个网格点负责一个区域的预测，只要物体的中心点落在这个区域，这个物体就由这个网格点来确定。

#### darknet53

![webp net-resizeimage](../../../../../../Pictures/assets/04-yolov3/53487913-2df2b480-3ad0-11e9-9788-b2feab624786.png)

```python
import math
import torch


class ResidualBlock(torch.nn.Module):
    """
        1x1卷积+3x3卷积
    """

    def __init__(self, in_channels, out_channels):
        super(ResidualBlock, self).__init__()

        layers = []

        self.conv1 = self.make_single_layer(in_channel=out_channels,
                                            out_channel=in_channels,
                                            kernel_size=1, stride=1, padding=0, bias=False)

        self.conv2 = self.make_single_layer(in_channel=in_channels,
                                            out_channel=out_channels,
                                            kernel_size=3, stride=1, padding=1, bias=False)

    def make_single_layer(self, in_channel, out_channel, kernel_size=3, stride=1, padding=0, bias=True,
                          negative_slope=.1):
        return torch.nn.Sequential(
            torch.nn.Conv2d(
                in_channels=in_channel, out_channels=out_channel, kernel_size=kernel_size,
                stride=stride, padding=padding, bias=bias
            ),
            torch.nn.BatchNorm2d(out_channel),
            torch.nn.LeakyReLU(negative_slope)
        )

    def forward(self, x):
        residual = x

        output = self.conv1(x)
        output = self.conv2(output)

        output += residual

        return output


class Darknet(torch.nn.Module):

    def __init__(self, in_channels=3, out_channels=32, kernel_size=3, blocks: list = [1, 2, 8, 8, 4]):
        super(Darknet, self).__init__()

        self.channels = [int(math.pow(2, i + 1) * out_channels) for i in range(len(blocks))]

        self.conv1 = self.make_single_layer(in_channel=in_channels,
                                            out_channel=out_channels,
                                            kernel_size=kernel_size, padding=1, bias=False)

        (self.layer1, self.layer2, self.layer3, self.layer4, self.layer5) = self.make_layer(in_channel=out_channels,
                                                                                            blocks=blocks,

                                                                                            kernel_size=kernel_size)
        self.init_params()

    # 初始化
    def init_params(self):
        # 进行权值初始化
        for m in self.modules():
            if isinstance(m, torch.nn.Conv2d):
                n = m.kernel_size[0] * m.kernel_size[1] * m.out_channels
                m.weight.data.normal_(0, math.sqrt(2. / n))
            elif isinstance(m, torch.nn.BatchNorm2d):
                m.weight.data.fill_(1)
                m.bias.data.zero_()

    # 总体架构
    def make_layer(self, in_channel, blocks, kernel_size, stride=1, padding=0, bias=True,
                   negative_slope=.1):
        layers = []
        for block in blocks:
            conv = self.make_multi_layer(in_channel=in_channel,
                                         out_channel=in_channel * 2,
                                         kernel_size=kernel_size, block=block)
            in_channel = in_channel * 2

            layers.append(conv)

        return layers

    # 卷积
    def make_single_layer(self, in_channel, out_channel, kernel_size, stride=1, padding=0, bias=True,
                          negative_slope=.1):
        return torch.nn.Sequential(
            torch.nn.Conv2d(
                in_channels=in_channel, out_channels=out_channel, kernel_size=kernel_size,
                stride=stride, padding=padding, bias=bias
            ),
            torch.nn.BatchNorm2d(out_channel),
            torch.nn.LeakyReLU(negative_slope)
        )

    # 残差层
    def make_multi_layer(self, in_channel, out_channel, block, kernel_size, stride=1, padding=0, bias=True,
                         negative_slope=.1):
        layers = []
        layers.append(self.make_single_layer(in_channel=in_channel,
                                             out_channel=out_channel,
                                             kernel_size=kernel_size,
                                             stride=2, padding=1, bias=False))
        for i in range(block):
            layers.append(ResidualBlock(in_channels=in_channel, out_channels=out_channel))

        return torch.nn.Sequential(*layers)

    def forward(self, x):

        x = self.conv1(x)

        x = self.layer1(x)
        x = self.layer2(x)
        out3 = self.layer3(x)
        out4 = self.layer4(out3)
        out5 = self.layer5(out4)

        # 后面三层特征输出
        return out3, out4, out5


def darknet53():
    return Darknet(blocks=[1, 2, 8, 8, 4])
```



#### 9种尺度的先验框

![img](../../../../../../Pictures/assets/04-yolov3/v2-35affdda3427ef1fadd0c9939e3de114_720w.jpg)

<img src="../../../../../../Pictures/assets/04-yolov3/v2-7d45deb4713deda091d336b1ef01878b_720w.jpg" alt="img" style="zoom:50%;" />

#### 总框架

![请添加图片描述](../../../../../../Pictures/assets/04-yolov3/ac5ff41080e946a8b858df6783422c65.jpg)

```python
import torch
from models.darknet import darknet53 as Darknet


class YoloBody(torch.nn.Module):

    def __init__(self, anchors_mask, nclass, model: Darknet = Darknet):
        super(YoloBody, self).__init__()

        # 提取特征
        self.model = model()

        # 网络架构中每个残差层的通道数目
        # [64, 128, 256, 512, 1024]
        channels = self.model.channels

        # 输出通道
        out_channel = len(anchors_mask[0]) * (nclass + 1 + 4)

        # 计算yolo_head的输出通道数
        # (种类数目+1个数值(边框置信度，即该边框是否包含对象的置信度)+4个数值(边框坐标))*3(3个先验框)
        # 对于voc数据而言：75=(20+1+4)*3

        # 从网络模型中，获取的三个特征层数据，进行计算yolo_head，这三个层从上往下分别
        # 记为layer1,layer2,layer3

        self.layer3_conv5, self.layer3 = self.make_layer(in_channel=channels[-1], mid_channel=channels[-1] // 2,
                                                         out_channel=out_channel)

        self.layer2_conv1 = self.make_single_layer(in_channel=channels[-2], out_channel=channels[-2] // 2,
                                                   kernel_size=1)
        self.layer2_upsample = torch.nn.Upsample(scale_factor=2, mode='nearest')

        self.layer2_conv5, self.layer2 = self.make_layer(in_channel=channels[-2] + 256, mid_channel=channels[-2]//2,
                                                         out_channel=out_channel)

        self.layer1_conv1 = self.make_single_layer(in_channel=channels[-3], out_channel=channels[-3] // 2,
                                                   kernel_size=1)
        self.layer1_upsample = torch.nn.Upsample(scale_factor=2, mode='nearest')
        self.layer1_conv5, self.layer1 = self.make_layer(in_channel=channels[-3] + 128, mid_channel=channels[-3]//2,
                                                         out_channel=out_channel)

    def make_layer(self, in_channel, mid_channel, out_channel, layer=5):
        # Yolo Head本质上是一次3x3卷积加上一次1x1卷积，3x3卷积的作用是特征整合，1x1卷积的作用是调整通道数。

        layers1 = []
        layers2 = []

        # 特征提取
        layers1.append(
            self.make_single_layer(in_channel=in_channel, out_channel=mid_channel, kernel_size=1)
        )
        for i in range(layer // 2):
            layers1.append(
                self.make_single_layer(in_channel=mid_channel, out_channel=in_channel, kernel_size=3)
            )
            layers1.append(
                self.make_single_layer(in_channel=in_channel, out_channel=mid_channel, kernel_size=1)
            )

        # 预测结果
        layers2.append(
            self.make_single_layer(in_channel=mid_channel, out_channel=in_channel, kernel_size=1)
        )
        layers2.append(
            self.make_single_layer(in_channel=in_channel, out_channel=out_channel, kernel_size=3)
        )

        return torch.nn.Sequential(*layers1), torch.nn.Sequential(*layers2)

    def make_single_layer(self, in_channel, out_channel, kernel_size=3, stride=1, padding=0, bias=False,
                          negative_slope=.1):
        padding = (kernel_size - 1) // 2 if kernel_size else 0
        return torch.nn.Sequential(
            torch.nn.Conv2d(
                in_channels=in_channel, out_channels=out_channel, kernel_size=kernel_size,
                stride=stride, padding=padding, bias=bias
            ),
            torch.nn.BatchNorm2d(out_channel),
            torch.nn.LeakyReLU(negative_slope)
        )

    def forward(self, x):
        # 获取darknet网络中三层特征输出
        out1, out2, out3 = self.model(x)

        # 先计算最后一层
        out3_conv5 = self.layer3_conv5(out3)
        out3 = self.layer3(out3_conv5)

        # 做卷积和上采样操作
        x1 = self.layer2_conv1(out3_conv5)
        x1 = self.layer2_upsample(x1)

        # 和第二层特征层进行拼接
        concat1 = torch.cat([x1, out2], 1)

        # 计算第二层
        out2_conv5 = self.layer2_conv5(concat1)
        out2 = self.layer2(out2_conv5)

        # 同理
        x2 = self.layer1_conv1(out2_conv5)
        x2 = self.layer1_upsample(x2)

        concat2 = torch.cat([x2, out1], 1)

        # 计算第一层输出特征
        out1_conv5 = self.layer1_conv5(concat2)
        out1 = self.layer1(out1_conv5)

        return out1, out2, out3
```

#### 预测结果的解码

每一个有效特征层将整个图片分成与其**长宽对应的网格**，**如(N,13,13,255)的特征层就是将整个图像分成13x13个网格；然后从每个网格中心建立多个先验框，这些框是网络预先设定好的框，网络的预测结果会判断这些框内是否包含物体，以及这个物体的种类。**

由于每一个网格点都具有三个先验框，所以上述的预测结果可以reshape为：

- (N,13,13,3,85)
- (N,26,26,3,85)
- (N,52,52,3,85)

**其中的85可以拆分为4+1+80，其中的4代表先验框的调整参数，1代表先验框内是否包含物体，80代表的是这个先验框的种类，由于coco分了80类，所以这里是80。**如果YoloV3只检测两类物体，那么这个85就变为了4+1+2 = 7。

**即85包含了4+1+80，分别代表x_offset、y_offset、h和w、置信度、分类结果。**

但是这个预测结果并不对应着最终的预测框在图片上的位置，还需要解码才可以完成。

YoloV3的解码过程分为两步：
- 先**将每个网格点加上它对应的x_offset和y_offset**，加完后的结果就是**预测框的中心**。
- 然后**再利用先验框和h、w结合 计算出预测框的宽高**。这样就能得到整个预测框的位置了。

![在这里插入图片描述](../../../../../../Pictures/assets/04-yolov3/20191120215015351.png)

得到最终的预测结果后还要进行**得分排序与非极大抑制筛选**。

这一部分基本上是所有目标检测通用的部分。其对于每一个类进行判别：
**1、取出每一类得分大于self.obj_threshold的框和得分。
2、利用框的位置和得分进行非极大抑制。**

## Yolov4

Yolov4是Yolov3的加强版，其主要在Yolov3的基础上添加了一系列小改进。

因此，Yolov4与Yolov3整体的预测死了是没有差别的，解码的过程甚至是一模一样。