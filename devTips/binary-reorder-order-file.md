# 二进制重排 / Order File 启动优化（iOS）

> 对应 [mkPlan#2](https://github.com/mythkiven/mkPlan/issues/2) 的实操笔记。

## 原理（一句话）

冷启动时尽量让**启动路径上的代码和数据在磁盘上连续排列**，减少 page fault 和随机 IO，从而缩短首屏时间。

## 推荐流程

### 1. 采集启动符号

- Xcode → **Product → Profile → App Launch** 录一次冷启动；或
- 在 Debug 配置下打开 `DYLD_PRINT_STATISTICS=1` 观察加载耗时；或
- 使用 [MKLaunchMonitor](https://github.com/mythkiven/MKAppKit) 做启动打点对比。

### 2. 生成 Order File

把启动阶段命中的符号按调用顺序写入文本文件，例如 `app.order`：

```text
+[AppDelegate application:didFinishLaunchingWithOptions:]
-[ViewController viewDidLoad]
...
```

可用 Instruments 导出、脚本解析 Link Map，或基于自定义 hook 统计 `+load` / 首屏 VC 符号。

### 3. 配置 Xcode

| Build Setting | 值 |
| --- | --- |
| **Order File** | `$(SRCROOT)/app.order` |
| **Link-Time Optimization**（可选） | Incremental / Full |

重新 Archive，对比优化前后：

- 冷启动 P50/P90
- 主二进制 page fault 次数（Instruments / MetricKit）

### 4. 验证与回滚

- 在真机多机型验证，避免 order file 遗漏导致符号未链接的极端情况
- 保留 baseline 包，便于 A/B 对比

## Android（APK 重排）

思路相同：统计启动热 class/dex，将热数据连续排列。可参考 issue #2 中支付宝公开文章。

## 延伸阅读

- [抖音：基于二进制文件重排的解决方案](https://mp.weixin.qq.com/s/Drmmx5JtjG3UtTFksL6Q8Q)
- [Apple: Improving Locality of Reference](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/CodeFootprint/Articles/ImprovingLocality.html)
- [WWDC 2016 — Optimizing App Startup Time](https://developer.apple.com/videos/play/wwdc2016/406/)
