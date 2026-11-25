<div align="center">

# LaserWorkbench

**高性能原生 C++20 / Qt 6 激光设计、智能排料、批量变码与加工工作台**

[![Platform](https://img.shields.io/badge/Platform-Windows%20x64-0078D6?style=flat-square)](https://github.com/Vforvan/LaserWorkbench-Releases/releases)
[![Tech](https://img.shields.io/badge/Built%20with-C%2B%2B20%20%7C%20Qt%206.8-41CD52?style=flat-square)](https://github.com/Vforvan/LaserWorkbench-Releases/releases)
[![Release](https://img.shields.io/github/v/release/Vforvan/LaserWorkbench-Releases?style=flat-square&label=Latest%20Release)](https://github.com/Vforvan/LaserWorkbench-Releases/releases)

[下载最新版 Releases](https://github.com/Vforvan/LaserWorkbench-Releases/releases) · [功能亮点](#核心优势) · [性能实测](#实测性能基准) · [发展路线](#演进路线) · [问题反馈](https://github.com/Vforvan/LaserWorkbench-Releases/issues)

</div>

---

## 简介

**LaserWorkbench** 是一款面向桌面级创客与小批量生产场景的**原生桌面激光加工软件**。软件基于 **C++20 与 Qt 6.8** 从底层构建，集**矢量 CAD 绘图、高精度节点修复、位图雕刻与描摹、智能自动排料、项目变量与批量流水号/二维码/条形码、工艺图层管理与加工预览**于一体，强调**大图元不卡顿、离线数据确定性、批量生产效率与所见即所得的加工体验**。

> 本仓库为 **LaserWorkbench 官方公开发行仓库**，用于发布 Windows 预编译安装包、版本更新日志及收集用户反馈（源码仓库为私有维护）。

---

## 核心优势

### 1. 百万级图元满帧画布引擎（不卡顿、不爆内存）
- **多层混合渲染加速**：自研 `CanvasSpatialIndex` 空间索引 + 多线程世界坐标瓦片缓存（`CanvasTileCache`）+ 滚轮连续缩放旧瓦片无缝垫底 + 静态背景离屏快照，交互与渲染彻底解耦。
- **严谨内存预算**：全画布显示缓存、位图 Mipmap 与预览数据统一纳入 **64 MiB 预算账本**，超大工程也不会拖垮系统内存。
- **高视觉品质**：动静分离抗锯齿、亚像素微图元覆盖率透明度衰减（缩小看密集阵列不糊成黑团）、位图 Alpha 加权 Mipmap 金字塔（消除高频抖动图与二维码缩小时的摩尔纹），并支持**闭合矢量半透明填充预览模式**。

### 2. 面向批量生产的效率工作流
- **项目变量绑定（`{变量名}`）**：在项目级统一维护产品型号、批次号、日期等字段；**文字、QR 二维码、Code 128 条形码**均可绑定同一字段，修改一次，全图文字与码图同步重新编码。
- **批量流水号（`{序列号}`）**：支持配置前缀、起始值、步长与补零位数；阵列生成或对已排好的产品组按指定顺序批量编号，支持画布实时预览后一次应用，后续移动或重新排版自动保留原件编号。
- **智能自动排料（Auto Nesting）**：支持不规则多边形轮廓嵌套、板材利用率优化与实时渐进动画预览。
- **形状模板匹配（Shape Match）**：一键查找版面中同形状/旋转对称零件，批量选中与替换更高效。

### 3. 专业级二维矢量 CAD 与位图雕刻
- **完整绘图与几何工具**：矩形/圆角矩形、椭圆、多边形、星形、直线、折线、三次贝塞尔曲线；布尔运算（并/差/交/异或）、偏移、切分、圆角、路径闭合与连接。
- **高精度节点编辑与修复**：支持角点/平滑/对称节点切换、打断/连接/反序、微小断线缝合与曲线拟合简化；支持按轮廓设置加工起点与水口（Tabs/Bridges）。
- **位图处理与一键追踪**：内置灰度、阈值、有序抖动、Floyd-Steinberg、Atkinson、半色调、素描等多种雕刻模式；支持非破坏性裁剪与矢量蒙版、一键位图追踪描摹为原生贝塞尔轮廓，以及矢量转位图。
- **标尺、辅助线与精准测量**：支持毫米/英寸切换、网格移动与独立关键点捕获（端点/中点/圆心/交点/对齐线）、悬停测量、两点测距与选区统计（宽高/总周长/面积）。

### 4. 工艺图层、加工预览与三维工件基础
- **多工艺图层与加工仿真**：按图层独立配置功率、速度、多遍加工与水口参数，提供切割/空移/水口分级可视化预览与加工顺序进度回放。
- **开放设计交换**：支持原生 `.lwp` 项目文件与标准 SVG 矢量导入/导出，支持标准 GRBL G-code 导出与联机发送。
- **三维工件视图基础**：内置 3D STL 工件视图与拾取接口，为曲面加工与动态跟焦演进打下基础。

---

## 实测性能基准

以下数据来自 Release 版本在 `1280×800` / `1200×800` 视口下的真实压力工程基准测试（含 `9,000` 条贝塞尔曲线 / `981,360` 顶点压力工程与百万顶点路径）：

| 压力测试场景 | 帧耗时（Median / P95） | 等效帧率 |
| --- | --- | --- |
| **10,000 个矩形选区拖拽** | `0.95 ms` / `1.11 ms` | **> 900 FPS** |
| **100 万顶点单路径框选 / 平移** | `0.89 ms` / `1.48 ms` | **> 600 FPS** |
| **360 组复杂曲线（98 万点）放大平移** | `2.16 ms` / `2.58 ms` | **> 380 FPS** |
| **360 组复杂曲线（98 万点）连续滚轮缩放** | `4.02 ms` / `4.56 ms` | **> 200 FPS** |
| **80 万段重叠扫描雕刻预览** | `0.78 ms` / `1.24 ms` | **> 800 FPS** |
| **`1600×1200` 扫描线放大 / Mipmap 缩小平移** | `3.51 ms` / `2.13 ms` | **> 280 FPS** |

---

## 下载与使用

1. 前往右侧 **[Releases](https://github.com/Vforvan/LaserWorkbench-Releases/releases)** 页面下载最新的 `LaserWorkbench-vX.Y.Z-win64.zip`。
2. 解压到任意本地目录（绿色免安装，已内置所需 Qt 6 运行库）。
3. 双击 `LaserWorkbench.exe` 即可启动使用。

**系统要求**：Windows 10 / Windows 11（64 位）。

---

## 演进路线

LaserWorkbench 正在从传统 G-code 上位机向 **“激光版 Klipper”** 架构演进——不再只发送简单的 G-code 文本，而是由上位机承担完整的实时运动与能量规划：

- [x] **高性能画布引擎**：空间索引、多线程瓦片 + 缩放垫底、静态层分离、位图 Mipmap、闭合矢量填充预览
- [x] **批量生产套件**：自动排料、项目变量绑定、文字/二维码/条形码批量流水号、形状匹配
- [ ] **Klipper-Laser 主机运动编译器**：S-Curve 前瞻速度规划、Input Shaper 谐振抑制（消除高速扫描振纹）、激光开启延迟与拐角热积累补偿
- [ ] **物理级过烧热力图仿真 & 微秒级运动/功率示波器**
- [ ] **3D 曲面实时跟焦与多轴联动加工**

---

## 反馈与支持

- 遇到 Bug、显示异常或功能建议，欢迎直接在 **[Issues](https://github.com/Vforvan/LaserWorkbench-Releases/issues)** 提交，附上截图或复现步骤（如方便可附 `.lwp` / `.svg` 测试文件）会极大加快排查速度。

## 第三方组件致谢

本软件的发行版使用了以下优秀开源组件（完整许可证文本随安装包 `licenses/` 目录分发）：
- [Qt 6](https://www.qt.io/)
- [Clipper2](https://github.com/AngusJohnson/Clipper2)
- [QR-Code-generator](https://github.com/nayuki/QR-Code-generator)
