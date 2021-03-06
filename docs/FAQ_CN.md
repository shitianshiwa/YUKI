# YUKI 常见问题（Frequently Asked Questions）

## 综合篇

### YUKI 和 VNR 相比有何优缺点？

YUKI 相比 VNR，有如下优点：

1. 基于 Electron->Node->Google V8 的框架链，使得其相比于基于 PyQt->Python 的 VNR 有了**巨幅**的性能提升
2. 相比功能强大的 VNR，其配置难度**可能**较低（如果你熟悉一点编程）
3. 基于 Textractor 的文本提取，可以免特殊码支持更多的游戏

当然，作为一个功能迭代时间不算长的项目，YUKI 相比 VNR 有如下的缺点：

1. 缺少很多基于网络数据库的功能，如共享辞书、人工字幕等。这点不急于实现的原因很大程度上在于目前在线翻译 API 逐渐 AI 化，导致替换文字有时反而会降低其翻译质量
2. 缺少 OCR 功能，尚处于摸索阶段
3. BUG 多，这点没什么好说的
4. Electron 应用的通病，磁盘和内存占用较大

### 为何这么多功能设置都需要手动修改配置文件？

因为设计 UI 比实现功能难多了（确信）

### 为何修改配置文件后需要重启 YUKI？

由于架构设计问题，目前很多配置仅在程序启动时进行加载并参与模块构建，此后该模块在程序运行期间一直驻留内存，无法进行热替换，因此必须重启以重新加载模块。

这也是上一个问题的另一部分原因。

~~然而改架构又太费时间，说到底还是一条懒狗。~~

### 为何那么多功能计划拖了这么久还不实现？

作者目前留学中，更新频率相比以往会有很大程度的下降。

当然，作为一个开源项目，肯定是希望有更多人参与项目讨论与开发的。把功能计划放出来的目的就是期待有开发者能够一同完成/改进。

## 使用篇

### 如何开启/关闭某一翻译 API？

请参照 [config.json 配置详解](/docs/ConfigFiles_CN.md#configjson)，修改各个 onlineApi 的 enable 属性为 true/false，重启 YUKI 即可。

### 某些翻译 API 经常挂掉？

目前监测下来百度和沪江容易出现此情况，可能跟其限制同一设备日均访问次数/频率有关，建议

- 文本不要推得太快，给出一定的 API 调用间隔
- 如果不幸被限制，可以更换其私人 API（如百度开放平台）
- 禁用该 API，换用其它的 API

### 如何去除文本中出现的叠字（如变 AAABBBCCC 为 ABC）？

请参照 [text.json 配置详解](/docs/ConfigFiles_CN.md#textsjson)，修改 deduplicate 属性为 true，重启 YUKI 即可。

### 怎样调整翻译窗口透明度？

翻译器窗口->翻译器设置 中有修改背景色的选项，第二个拖动条就是 Alpha 通道，其值位于 0~1，1 为完全不透明。如果想要全透明效果，记得调整该值为 0。

### 翻译窗口有一层毛玻璃效果，怎样去除？

修改 gui.json 文件中的 translatorWindow.renderMode 为 transparent，重启 YUKI 即可。

### 我要快进一段剧情，怎样不让 YUKI 翻译快进部分的文本？

使用“暂停文本获取”功能。

1. 点击翻译窗口右上角的暂停按钮
2. 快进到想要的位置
3. 点击翻译窗口右上角的播放按钮

### 翻译窗口挡住了游戏窗口，不能正常操作，怎么办？

善用“翻译窗口置顶”功能。其位于翻译窗口右上角图案为锁的按钮，点击即可在窗口置顶/窗口不置顶间切换。

一种可能的操作方案：

1. 点击按钮，切换为窗口不置顶模式
2. 点击游戏窗口。此时游戏窗口显示在翻译窗口上方
3. 操作游戏窗口
4. 在进入对话阶段后，点击翻译窗口。此时翻译窗口显示在游戏窗口上方
5. 点击按钮，切换为窗口置顶模式
6. 点击游戏窗口，推进文本，查看翻译

### 我想要...功能！

请先查阅[YUKI 配置文件详解](https://github.com/project-yuki/YUKI/blob/master/docs/ConfigFiles_CN.md)，看看想要的功能是否已经存在，如果是，则按照对应要求修改配置文件以开启该功能。

## 进阶篇

### config 文件夹下的那些 .js 文件是什么?

外部 API（External API），一种可编程式翻译 API，可以供 YUKI 在翻译文本时调用。

为什么要有这样的设计？

一部分原因在于基于配置的 API（也称为内部 API）很难计算出一些翻译 API 要求的 sign 或 timestamp，而这些值对于其 API 正常工作来说是必要的，因此可以在外部 API 中编写一些函数用以生成此类参数。

另一部分原因在于这种设计可以将 API 与翻译器本身解耦，以防止像目前的 VNR 一样出现停止维护后翻译 API 需要修改源代码才能进行更新/修复的情况，以最大程度延长未来 YUKI **可能的**停止维护后的可用时间。

### 我自己编写了一个可以使用的外部 API，怎样提供给他人使用？

由于外部 API 文件独立于主程序之外，可以直接将分发出去，他人设置好 config.json 中对应的属性，即可正常使用。

当然，如果想要合并进 YUKI 以供更多人使用交流，欢迎提交 Pull Request :)
