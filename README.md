# 高性能菲涅尔积分 (Fresnel Integrals) 计算引擎

[![TypeScript](https://img.shields.io/badge/TypeScript-5.0+-blue.svg)](https://www.typescriptlang.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

这是一个移植自经典 C 语言 Cephes 数学库的高性能 TypeScript 菲涅尔积分模块。

为了在 Web 前端或 Node.js 环境中达到极致的性能，本库针对 V8 引擎的 JIT 编译特性（如标量替换、分支预测、内存逃逸分析）进行了深度的底层重构。**在保持与原生 C 语言同等级别（IEEE 754 极限）精度的同时，运行效率超越了著名的 `@stdlib/math-base-special-fresnel` 标准库。**

## 🚀 特性亮点

- **极限精度**：相对误差 `< 1e-15`，完全对齐 Cephes 数学库。
- **极致性能**：采用“单出口”设计，完美触发 V8 引擎寄存器优化，零堆内存分配，GC 友好。
- **零依赖**：纯原生 TypeScript 实现，无需引入任何第三方数学库。
- **无缝接入工程计算**：专为道路工程（缓和曲线/回旋线坐标计算）、图形学、光学衍射等场景打造。

## 📐 数学定义

本模块计算如下两个标准菲涅尔积分：

- \(S(x) = \int_0^x \sin\left(\frac{\pi}{2} t^2\right) dt\)
- \(C(x) = \int_0^x \cos\left(\frac{\pi}{2} t^2\right) dt\)

### 计算策略

由于菲涅尔积分没有初等函数的解析解，本模块采用分段逼近策略以保证全局高精度：

1. **小/中参数区 (\(|x|^2 < 2.5625\))**：使用有理多项式（连分式）进行幂级数逼近。
2. **大参数区 (\(2.5625 \le |x|^2 \le 36974^2\))**：使用渐近辅助函数 \(f(x)\) 和 \(g(x)\) 展开：
   - \(C(x) = \frac{1}{2} + f(x) \sin\left(\frac{\pi}{2} x^2\right) - g(x) \cos\left(\frac{\pi}{2} x^2\right)\)
   - \(S(x) = \frac{1}{2} - f(x) \cos\left(\frac{\pi}{2} x^2\right) - g(x) \sin\left(\frac{\pi}{2} x^2\right)\)
3. **超大参数区 (\(|x| > 36974\))**：积分值逼近极限常数 \(0.5\)。
