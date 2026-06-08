---
name: nsfw-prompt-factory
description: >
  NSFW提示词工厂——给角色+主题+N张图，自动推断色情等级弧线并生成完整prompt。
  基于S/A/B/C四级色情等级系统驱动标签选择。12条弧线×45+体位配对。
  触发："角色 + 主题 + 多少张图"。也支持单图+随机模式。先出剧情大纲确认，再展开prompt。
compatibility:
  - SD WebUI Forge / ComfyUI
  - Illustrious-XL v2.0 系 (NoobAI/WaiNSFW/HassakuXL 兼容)
  - Renbocloud Style LoRA (0.6, 可选)
---

# NSFW 提示词工厂

## 风格配置（用前说一次）

`"我用 Renbocloud"` / `"我用 WaiNSFW"` / `"无 LoRA"` → 底座+堕落标记自动切换。

---

## 核心流程

角色+主题+N → 弧线类型 → 等级分配 → 查 `arc-position-pairing.md` 体位序列 → 场景节拍 → 大纲确认 → 展开prompt

---

## 12条弧线

| 弧线 | 等级方向 | 逻辑 |
|------|---------|------|
| 堕落 | C→B→A→S→S | 站→坐→跪→趴→压 |
| 调教 | C→B→A→S→S | 自由→束缚→服务→被控 |
| 反差 | B→A→S→A→S | 装→露馅→崩→假→又崩 |
| 展示 | B→A→S→A | 穿→脱→曝→瘫 |
| 诱惑 | B→A→B→A→S | 偷看→出招→收→再攻→陷 |
| 露出 | B→A→S→S | 紧张→暴露→高潮→破罐 |
| NTR | C→B→A→S | 忠诚→动摇→沦陷→背叛 |
| 速攻 | B→S→S | 通信号→插入→再来 |
| 浪漫 | C→B→A→B | 不过A级，温存收尾 |
| 穿衣 | S→A→B→C | 全裸→内衣→半遮→整齐 |
| 戏态 | B→S→B→S | 装→崩→又装→又崩 |
| Pixiv本子 | 山形 | 封面S→建立B→挑逗→核心S高潮→余韵A→收尾B |

N=10 本子弧：封面(S) → ①B ②B ③A → ④⑤⑥⑦S → ⑧A → ⑨B
N=5 堕落弧：C, B, A, S, S

---

## 等级速查

| 等级 | 表情 | 服装 | 镜头 |
|------|------|------|------|
| C | 平静优雅 | 原皮整齐 | 远远看着 |
| B | 害羞动摇 | 微敞露肩 | 凑近观察 |
| A | 发情呻吟 | 半脱破损 | 怼脸看 |
| S | 阿黑颜崩坏 | 母狗制服 | 直击敏感 |

---

## 7+1模块

### 模块1：底座
```
<lora:IL_Renbocloud Style:0.6>, (explicit:1.2), (nsfw:1.1), masterpiece, best quality, newest, excellent quality,
```
无LoRA则去掉lora行。

### 模块2：角色
- **角色标签**：查 `danbooru-character-tags.md`。C/B级原皮，A/S级堕落色
- **男性**：默认 `black man, muscular, dark skin, huge black cock, bbc`。用户可换，见 `male-character-options.md`
- **FFM/多人**：体型紧贴角色名+权重，衣服只写颜色+choker，表情共享，公用材质末尾统一

### 模块3：表情 — **词组轰炸**
从 `erotic-ranking.md` 按等级选 8~12 同义标签围攻。核心挂 1.3，其余裸词。角色堕落配色查 `character-database.md`

### 模块4：肉体
萝莉体：petite, flat chest **不加乳环**。成女：huge breasts

### 模块5：服装弧线
C原皮 → B微敞 → A破损+首个BDSM → **S母狗制服(9锚点latex)**

S级模板见 `latex-catsuit-template.md`。用户说"换颜色/材质"即可改。

### 模块6：姿势
查 `arc-position-pairing.md`（12弧线×体位序列）。每级2~3候选，选后从 `position-library.md` 查视角+尺寸。

### 模块7：机位/环境
按等级选镜头。兼容性查 `compatibility-matrix.md`

### 模块8：堕落标记（S级专用）
身体写字(3~5部位)：`cum dump on belly, public property on thigh, free use on chest, whore on forehead, BBC only above pussy` 等。分布额头+胸+肚子+大腿+臀。

---

## 硬规则

| 规则 | 值 |
|------|-----|
| 权重上限 | **1.3** 不越线 |
| 位置>数字 | 前排裸词 > 后排加权 |
| 同义词围攻 | 12裸词 > 单(1.5) |
| 采样器 | Euler a |
| Steps | 35 |
| CFG | 4.5 |
| Hires | 4x-UltraSharp 2x, CFG 4, Denoising 0.38 |
| 分辨率 | 832×1216 / 1216×832 |

### 致命禁区
- gyaru 跟等级走(C/B不加, A开始, S全开)
- 萝莉不加乳环 / 侧面体位 must from_side
- ahegao自带 rolled eyes/tongue out 别叠
- `head hanging back` + `eye contact` = 💀
- `face_close_up` ⊥ `upper_body`

### 反向词等级联动
C防暴露 / B防脱光 / A防AI穿回去 / S防AI加衣服。双人删 male/penis/cock

### 节奏
每3~5张换角度/体位/服装。4张S级高潮体位不重复。

---

## 输出控制
- `"全展开"` → 一次性输出
- `"一张一张来"` → 逐张
- `"生成发布信息"` → 中日双语标题标签

---

## 参考文件

| 文件 | 内容 |
|------|------|
| `danbooru-character-tags.md` | 70+角色 Danbooru 标签(人气排行) |
| `erotic-ranking.md` | S/A/B/C 6维度标签池+组合建议 |
| `arc-position-pairing.md` | 12弧线×体位配对序列 |
| `position-library.md` | 45+体位(含视角/尺寸) |
| `compatibility-matrix.md` | 姿势↔镜头兼容表 |
| `character-database.md` | 角色堕落配色+验证标记 |
| `cover-templates.md` | 5种封面+对比公式 |
| `latex-catsuit-template.md` | 9锚点母狗服模板 |
| `male-character-options.md` | 男性角色可选模板 |
| `inpaint-hand-fix.md` | Inpaint 手脚修复一体化模板 |
