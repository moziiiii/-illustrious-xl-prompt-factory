---
name: illustrious-xl-prompt-factory
description: >
  Illustrious XL 提示词工厂——基于S/A/B/C四级色情等级系统，为Illustrious-XL v2.0系模型生成精确NSFW提示词。
  核心能力：色级驱动标签选择、12条场景过渡曲线、70+角色Danbooru标签查表、45+体位库(含推荐视角/尺寸)、
  姿势/镜头兼容检查、Pixiv封面模板、母狗服9锚点引擎。
  触发：描述"角色 + 主题 + 多少张图"时。先出剧情大纲确认，再展开完整prompt。
compatibility:
  - Illustrious-XL v2.0 / NoobAI / WaiNSFW / HassakuXL
  - SD WebUI Forge / ComfyUI
---

# Illustrious XL 提示词工厂

## 风格配置

默认通用 Illustrious-XL。有 LoRA 时说一次：`"我用 Renbocloud LoRA"` / `"我用 WaiNSFW"` / `"无 LoRA"`

---

## 核心流程

角色+主题+N张图 → 弧线类型 → 等级分配 → 场景节拍 → 等级速查+剧情大纲 → 确认 → 展开prompt

---

## 12条弧线

| 弧线 | 等级方向 | 形状 |
|------|---------|------|
| 堕落弧 | C→S | 逐级沉沦 |
| 调教弧 | C→S | 每张=一层枷锁 |
| 反差弧 | B→S→A→S | 揭露→沉溺→余韵→再犯 |
| 展示弧 | B→S→A→S | 穿→脱→高潮→事后 |
| 诱惑弧 | B→A→B→A→S | 挑逗→退缩→再挑逗→失控 |
| 露出弧 | B→A→S→S | 紧张→暴露→高潮 |
| NTR弧 | C→B→A→S | 忠诚→动摇→沦陷→背叛 |
| 速攻弧 | B→S→S | 直接开干 |
| 浪漫弧 | C→B→A→B | 温存不过A级 |
| 穿衣弧 | S→A→B→C | 事后被迫穿回去 |
| 戏态弧 | B→S→B→S | 反复横跳 |
| Pixiv本子弧 | 山形 | 封面S预告→①B建立→挑逗→核心S高潮→余韵→收尾 |

---

## 等级速查

| 等级 | 表情 | 服装 | 镜头 |
|------|------|------|------|
| C | 平静优雅 | 原皮整齐 | 远远看着 |
| B | 害羞动摇 | 微敞露肩 | 凑近观察 |
| A | 发情呻吟 | 半脱破损 | 怼脸看 |
| S | 阿黑颜崩坏 | 全裸/母狗制服 | 直击敏感 |

---

## 7+1模块模板

### 模块1：底座
`masterpiece, best quality, newest, excellent quality` + `(explicit:1.2), (nsfw:1.1)`
有LoRA前置 `<lora:XXX:0.6>,`

### 模块2：角色（查 Danbooru 标签库）
`references/danbooru-character-tags.md` — 70+角色标准标签

### 模块3：表情（词组轰炸）
按等级选8~12个同义标签围攻。角色堕落配色见 `references/character-database.md`

### 模块4：肉体
萝莉/成女模板切换。萝莉不加乳环。

### 模块5：服装弧线
C原皮→B微敞→A破损+BDSM元素→**S母狗制服(9锚点latex模板)**

### 模块6：姿势 → 查 `references/position-library.md`
45+体位，每个自带推荐视角+尺寸。双人S级5选1。

### 模块7：机位/环境
按等级选镜头。姿势↔镜头兼容性查 `references/compatibility-matrix.md`

### 模块8：堕落标记（S级专用）
身体写字(3~5个部位)+烙印/纹身(选配)

---

## 硬规则

- 权重上限1.3，位置>数字，同义词围攻>单标签高权重
- Euler a, Steps 35, CFG 4.5
- 封面=对比公式(过去残留+现在堕落+直视镜头)
- 反向词按等级联动(防暴露/防穿回去/防AI加衣服)
- 每3~5张换角度/体位/服装

---

## 参考文件

| 文件 | 内容 |
|------|------|
| `references/danbooru-character-tags.md` | 70+角色 Danbooru 标签 |
| `references/erotic-ranking.md` | S/A/B/C 6维度标签池 |
| `references/compatibility-matrix.md` | 姿势↔镜头兼容表 |
| `references/character-database.md` | 角色堕落配色 |
| `references/position-library.md` | 45+体位 含视角/尺寸 |
