# ENUSP

**面向可泛化手势识别的环境、用户与位置显式解耦方法**

论文题目：*ENUSP: Explicit Decoupling of Environment, User, and Position for Generalizable Gesture Recognition*

作者：Dachuan Mei、Yongtao Ma、Bobo Wang、Chenglong Tian、Lele Yin。

[English](README.md) · [代码申请说明](CODE_ACCESS.md) · [手写承诺书模板](RESEARCH_UNDERTAKING.md)

ENUSP 面向毫米波雷达手势识别中的环境、用户和位置变化，采用 Purify-then-Align
策略，将局部与全局时空编码、独立门控融合、软正交约束和三路域对抗训练结合起来。
本项目提供七类 ENUSP 主方法及严格环境—用户—位置划分的实现说明。

## 代码申请

GitHub 页面公开项目介绍、使用说明和申请材料。完整代码通过邮件向研究申请者提供。

1. 阅读[研究用途及申请条件](CODE_ACCESS.md)。
2. **亲笔抄写承诺书正文**，填写姓名、单位、邮箱、研究用途，并亲笔签名、注明日期。
3. 将清晰扫描件或照片发送至 **meidachuan@tju.edu.cn**。
   邮件主题建议为：`ENUSP代码申请—姓名—单位`。
4. **收到完整手写承诺书后 15 个自然日内，将通过申请邮箱发送 ENUSP 代码项目。**

承诺书可使用中文或英文版本，无需同时提交两份。请通过邮件提交，勿将签名材料上传到公开 issue。

## 项目内容

代码包保留 ENUSP 模型、数据读取、训练、评估、默认配置和运行说明。
数据需向原始数据集提供方单独获取；项目不随包分发雷达数据。

- [方法与实现对照](docs/METHOD.md)
- [数据格式与划分](docs/DATA.md)
- [代码运行说明](docs/RUNNING.md)

整理版以论文主方法和最新三审配置为依据，具体实现差异记录在方法说明中。
该版本尚未通过完整重训验证论文中的准确率，旧实验结果不作为整理版的实测结果。

## 引用与联系

使用本项目开展研究时，请引用 ENUSP 论文，作者与题目见 [CITATION.cff](CITATION.cff)。
出版年份、卷期和 DOI 待确认后补充。

联系人：Dachuan Mei  
邮箱：**meidachuan@tju.edu.cn**

代码仅按[研究用途条件](CODE_ACCESS.md)提供。公开项目页面不代表授予代码的无限制使用或再分发权限。
