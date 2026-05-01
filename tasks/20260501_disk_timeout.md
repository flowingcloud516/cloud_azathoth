# 盘侧超时机制：需求与设计任务

## 业务背景

大模型推理中用 KV Cache 对长文本做持久化存储。计算时从存储系统读取——由于 SSD 固有长尾（P99.9 可达 ms~百 ms），GPU 等数据就位期间完全 idle，浪费算力。

**目标**：盘侧控制 IO 时延——要么快速返回数据，要么快速返回失败状态，让 GPU 立即重算而非空等。

## 设计边界（who am I）

掌控范围：Host 侧驱动 → 盘侧固件，全链路可设计，不限于软件层面。

## 盘侧超时的两个价值点

1. **KV Cache 长尾控制**：数据可重算（GPU FLOPs 代价已知且可控），确定性时延 > 随机长尾，更适合流水线调度。
2. **池化存储的调度杠杆**：盘侧对接上千 Client，可以在盘侧做全局调度、QoS、预取——这是 Host 侧单点无法做到的。

## 协议实现方向

### NVMe 已有机制评估

| 机制 | 能力 | 缺口 |
|------|------|------|
| **CDL**（T10，提案中，未落入协议） | per-IO 指定 duration limit | 仅 hint 语义，不强制 abort，未定义超时后 completion status |
| **Limited Retry** | 介质读错时的重试次数控制 | 次数维度，非时间维度 |
| **Set Features Timeout** | Host 侧看门狗 | 非 per-command，非盘侧主动 |
| **Abort 命令** | Host abort 指定 command ID | Host 驱动行为，被动触发 |

**结论**：标准 NVMe spec 没有现成的"盘侧主动超时+abort+返回特定状态"机制。

### 自定义方案的协议载体

以 Read/Write 命令 Dword 布局看：

| 位置 | 大小 | 可用性 |
|------|------|--------|
| **DW2**（byte 8-11） | 4 bytes | **最佳候选**，NVM Command Set 下为 Reserved |
| CDL Duration Descriptor | vendor-specific interpretation | 可定义"Limit A = 软 deadline，Limit B = 硬 deadline" |

建议：DW2 承载 16-bit timeout 字段（单位 μs 或 100μs），完全不冲突现有语义。

## 可靠性：两层 Abort 模型

Host 侧仍需保底超时（网络不可控），因此实际是两层 abort：

```
Host timeout > Disk timeout + 网络 RTT + Completion 处理时间
```

### 需要解决的关键问题

1. **竞态**：盘判定超时发起 abort 的同时介质操作恰好完成——需要 per-command atomic state machine（Pending → Aborting → Aborted | Completed）。
2. **DMA in-flight**：Read abort 时数据可能已部分 DMA 到 host memory，需 PCIe TLP 层面截断或标记 buffer 无效。
3. **Host Abort 与盘自 abort 重叠**：盘已完成（带超时错误）→ Abort 应返回 Command Already Completed。
4. **Write abort 语义不同**：Read abort = 数据无效 → 重算；Write abort = 可能 partial write → host 需标记 KV 块为 invalid。
5. **Abort 风暴**：大量命令同时超时 → 内部 abort 消耗 controller 资源 → 需速率限制。
6. **健康监测闭环**：频繁超时的 LBA 范围 → 触发 wear-leveling / bad block remapping / die 级 QoS 隔离。

## 分析结论（见笔记）

详见 [[盘侧超时机制]]，5 个分析问题均有结论：

1. IO 长尾成因 + 占比数据
2. 盘侧超时意义（合理，per-IO 可配置）
3. NVMe 已有机制（CDL 最接近但不完整）
4. 保留字段（DW2）
5. 可靠性额外问题（6 项）
