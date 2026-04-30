# 个人知识库搭建操作建议

## 任务目标
在当前目录下创建一个基于 Git + Markdown 的个人知识库，采用 MOC（Map of Content）+ 原子笔记的组织方式。

## 执行步骤

### 第一步：创建目录结构
在当前工作目录下执行：

```bash
mkdir -p knowledge-base/notes
mkdir -p knowledge-base/mocs
mkdir -p knowledge-base/_templates
cd knowledge-base
git init
```

### 第二步：创建模板文件
在 `_templates/atomic-note.md` 中写入：

```markdown
---
tags: []
date: {{date}}
---

# {{title}}

## 核心要点


## 详细内容


## 相关链接
- 
```

### 第三步：创建根 MOC（README.md）
在 `knowledge-base/README.md` 中写入：

```markdown
# 我的知识库

> 入口索引，所有知识从这进入。

## 当前焦点
- [[盘侧超时机制]]（节后要交，高优先级）

## 领域地图
- [[五一学习.MOC]]
- [[存储系统.MOC]]
- [[AI与工具.MOC]]

## 项目与任务
- [[五一待办]]

## 使用说明
1. 新笔记放到 `notes/` 目录，使用 `_templates/atomic-note.md` 模板
2. 在对应的 MOC 文件中添加笔记链接
3. MOC 文件放在 `mocs/` 目录，以 `.MOC.md` 结尾
4. 定期 commit 到 Git
```

### 第四步：创建第一个 MOC
在 `mocs/五一学习.MOC.md` 中写入：

```markdown
# 五一学习.MOC

## 高优先级（节后要交）
- [[盘侧超时机制]]

## 中优先级
- [[AI知识库设计方案]]

## 低优先级
- [[搭龙虾笔记]]

## 学习进度
- [ ] 盘侧超时机制 - 学习中
- [ ] AI知识库设计 - 规划中
- [ ] 搭龙虾 - 待开始
```

### 第五步：创建第一个原子笔记
在 `notes/盘侧超时机制.md` 中写入（可先用你已知的内容占位）：

```markdown
---
tags: [io, storage, timeout]
date: 2026-04-30
---

# 盘侧超时机制

## 核心要点
（待补充：什么是盘侧超时、触发条件、检测方式、处理逻辑）

## 详细内容
（待学习）

## 相关链接
- 
```

### 第六步：创建待办 MOC
在 `mocs/五一待办.MOC.md` 中写入：

```markdown
# 五一待办.MOC

## 高优先级
- [ ] 学习 IO 盘侧超时机制（节后要交）
- [ ] 输出盘侧超时理解文档

## 中优先级
- [ ] 梳理拿 AI 做什么
- [ ] 设计知识库形式

## 低优先级
- [ ] 填充知识技能库
- [ ] 考虑搭龙虾

## 已完成
- [x] 搭建知识库目录结构
```

### 第七步：提交初始版本
```bash
git add .
git commit -m "init: 初始化知识库结构，采用MOC+原子笔记模式"
```

## 后续使用惯例

### 添加新知识点
1. 在 `notes/` 创建新笔记，使用模板
2. 在相关的 MOC 文件中添加一行链接 `- [[笔记名]]`

### 知识脉络演化
- 当某个 MOC 下的链接超过 10 个时，考虑拆分子 MOC
- 当发现多个笔记自然形成主题时，创建新的 MOC
- 不需要一次性设计完美分类，让脉络自然生长

### Git 提交习惯
每完成一个知识点的学习或整理，随手 commit：
```bash
git add .
git commit -m "add: 盘侧超时机制 - 学习笔记"
```

## 验证方法
执行以下命令确认结构正确：

```bash
tree knowledge-base
# 应该看到：
# knowledge-base/
# ├── README.md
# ├── _templates/
# │   └── atomic-note.md
# ├── mocs/
# │   ├── 五一学习.MOC.md
# │   └── 五一待办.MOC.md
# └── notes/
#     └── 盘侧超时机制.md
```

## 可选：生成全局索引脚本
如果想快速查看所有笔记和标签，可以在根目录创建一个 `index.sh`：

```bash
#!/bin/bash
echo "# 笔记索引"
echo ""
echo "## 所有笔记"
ls -1 notes/*.md | sed 's|notes/| - [[|' | sed 's|\.md|]]|'
echo ""
echo "## 所有标签"
grep -rh "^tags:" notes/ | sort | uniq
```

