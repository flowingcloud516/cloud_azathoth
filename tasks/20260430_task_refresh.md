

```markdown
# 任务：为我的知识库 cloud_azathoth 创建内核 IO 子系统 MOC

## 背景
我的知识库采用 Git + Markdown + MOC（Map of Content）架构，目录结构如下：
- `notes/` - 存放原子笔记
- `mocs/` - 存放 MOC 索引文件
- `README.md` - 根入口

## 需要你执行的操作

### 第一步：创建 MOC 文件
在 `mocs/` 目录下创建 `内核IO子系统.MOC.md`，内容如下：

```markdown
# 内核 IO 子系统.MOC

> 本 MOC 管理所有与内核 IO 子系统相关的学习笔记。

## 当前焦点（高优先级）
- [[盘侧超时机制]] — 节后要交，重点：产生、检测、处理逻辑
- [[NVMe RDMA 框架]] — 代码学习，理解数据路径与核心数据结构

## 待深入（中优先级）
- [[swap 机制]] — 内存交换与块层的交互

## 学习路径建议
1. **盘侧超时机制** — 从块层错误处理入手
2. **NVMe RDMA 框架** — 高性能路径，与超时机制有交叉（RDMA 超时处理）
3. **swap 机制** — 理解内存与 IO 的边界

## 笔记列表
- 待补充

## 关联 MOC
- [[五一学习.MOC]]
```

### 第二步：创建对应的原子笔记占位文件
在 `notes/` 目录下创建三个占位笔记：

**notes/盘侧超时机制.md**
```markdown
---
tags: [io, block-layer, timeout]
date: {{date}}
---

# 盘侧超时机制

## 核心要点
（待补充）

## 详细内容
（待学习）

## 相关链接
- [[内核IO子系统.MOC]]
```

**notes/NVMe RDMA 框架.md**
```markdown
---
tags: [nvme, rdma, io, kernel]
date: {{date}}
---

# NVMe RDMA 框架

## 核心要点
（待补充）

## 详细内容
（待学习）

## 相关链接
- [[内核IO子系统.MOC]]
```

**notes/swap 机制.md**
```markdown
---
tags: [swap, memory, io, kernel]
date: {{date}}
---

# swap 机制

## 核心要点
（待补充）

## 详细内容
（待学习）

## 相关链接
- [[内核IO子系统.MOC]]
```

### 第三步：更新根 README.md
在 `README.md` 的「领域地图」部分，添加内核 IO 子系统的链接：

```markdown
## 领域地图
- [[五一学习.MOC]]
- [[存储系统.MOC]]
- [[内核IO子系统.MOC]]   ← 新增
- [[AI与工具.MOC]]
```

如果 `存储系统.MOC` 存在，也可以在其中添加对内核 IO 子系统的引用（可选）。

### 第四步：提交变更
```bash
git add .
git commit -m "feat: 添加内核IO子系统MOC及三个原子笔记占位"
```

## 执行要求
- 检查目录是否存在，不存在则创建
- 使用当前日期替换 `{{date}}`
- 执行完成后，输出 tree 命令的结果让我确认结构
```
