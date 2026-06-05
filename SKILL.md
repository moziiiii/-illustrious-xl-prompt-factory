---
name: illustrious-xl-prompt-factory
description: >
  Illustrious XL 提示词工厂——基于S/A/B/C四级色情等级系统，为Illustrious-XL v2.0系模型
  生成精确的NSFW提示词。核心能力：色级驱动标签选择、场景等级过渡曲线、角色Danbooru标签自动查表、
  姿势/镜头兼容性检查、Pixiv封面模板。
  触发：当描述"XX角色 + 主题 + 多少张图"时——例如"纳西妲 堕落 5张"、"芙莉莲 Pixiv本子 10张"。
  也支持单图。先出剧情大纲确认，再展开完整prompt。
compatibility:
  - Illustrious-XL v2.0 系模型 (NoobAI / WaiNSFW / HassakuXL 等兼容)
  - SD WebUI Forge / ComfyUI
  - 可选：任意风格 LoRA
---

# Illustrious XL 提示词工厂

---

## ⚙️ 风格配置（使用前先告诉 skill 你的设置）

skill 默认使用通用 Illustrious-XL 配置。如果你有特定风格 LoRA，说一次即可：

- `"我用 Renbocloud LoRA"` → 底座换 `<lora:IL_Renbocloud Style:0.6>` + 堕落后加 `gyaru, dark tanned skin`
- `"我用 WaiNSFW"` → 底座换对应 LoRA + WaiNSFW 风格词
- `"我用 NoobAI"` → 换 NoobAI 底座（v-pred需Danbooru标签）
- `"无 LoRA"` → 纯 Illustrious-XL，不加任何风格词

如未声明，默认**纯 Illustrious-XL**。

---

## 核心流程

```
你：角色 + 主题 + N张图
    ↓
Skill：主题→弧线类型 → N张图等级分配 → N个场景节拍 → 等级速查 + 剧情大纲
    ↓
你：确认/改
    ↓
Skill：展开完整 prompt（7/8模块 + 反向词 + 参数）
```

---

## 输入格式

### 序列模式
```
[角色]，[主题描述]，[N张图]
```

### 单图模式
```
[角色]，[等级/描述]，单图
```

### 随机模式
```
[角色]，随机S级，[N张]
```

---

## 主题 → 弧线类型

| 主题关键词 | 弧线 | 等级方向 | 形状 |
|-----------|------|---------|------|
| 堕落、腐化、恶堕 | **堕落弧** | C→S | 逐级沉沦 |
| 调教、驯服、break | **调教弧** | C→S | 每张=一层枷锁 |
| 反差、私下、真实面目 | **反差弧** | B→S→A→S | 揭露→沉溺→余韵→再犯 |
| 展示、写真、gallery | **展示弧** | B→S→A→S | 穿→脱→高潮→事后 |
| 勾引、诱惑、seduce、酒馆 | **诱惑弧** | B→A→B→A→S | 挑逗→退缩→再挑逗→失控 |
| 露出、公开、public、野外 | **露出弧** | B→A→S→S | 紧张→暴露→高潮→破罐破摔 |
| NTR、寝取、夺走 | **寝取弧** | C→B→A→S | 忠诚→动摇→沦陷→背叛 |
| 速堕、直接、quick、野战 | **速攻弧** | B→S→S | 第一张穿最后崩 |
| 浪漫、纯爱、love | **浪漫弧** | C→B→A→B | 温存不超过A级 |
| 事后穿衣、dress up | **穿衣弧** | S→A→B→C | 完事后被迫穿回去 |
| 反复、戏态、欲拒还迎 | **戏态弧** | B→S→B→S | 扮清纯→放纵→装正常→又犯 |
| 本子、Pixiv、同人志、封面 | **Pixiv本子弧** | **山形** | 封面预告→建立→挑逗→核心高潮→余韵→收尾 |

---

## N张图 → 等级分配

**堕落弧（线性 C→S）**：
N=4: C,B,A,S | N=5: C,B,A,S,S | N=6: C,B,B,A,S,S

**Pixiv本子弧（山形，高潮中置）**：
- N=10: 封面(S) → ①建立(B) → ②挑逗(B) → ③挑逗(A) → ④⑤⑥⑦核心(S) → ⑧余韵(A) → ⑨收尾(B)
- N=15: 封面(S) → ①②建立(B) → ③④⑤挑逗(B/A) → ⑥⑦⑧⑨⑩⑪核心(S) → ⑫⑬余韵(A) → ⑭收尾(B)

**诱惑弧**：N=4: B,A,B,S | N=5: B,A,B,A,S

---

## 封面模板（Pixiv本子弧专用）

**封面=S级结果预告**——"她已经变成这样了"，读者点进去看过程。

| 类型 | 姿势 | 镜头 |
|------|------|------|
| ①正面诱惑直视 | 托胸/手指含嘴，直视镜头 | front_view, bust |
| ②背面翘臀回眸 | 塌腰翘臀，回眸看镜头 | from_behind, looking_back |
| ③躺姿M字腿 | 仰躺M字开腿 | from_above, between_legs |
| ④跪姿口交暗示 | 跪镜头前张嘴伸舌 | from_below, tongue_out |
| ⑤动态脱衣中 | bra半挂/裙子滑落 | three-quarter_view |

**封面对比公式**：至少1个"过去残留"(头饰/武器/原皮碎片) + 至少1个"现在堕落"(项圈/阿黑颜/精液) + 直视镜头

---

## 等级速查

| 等级 | 表情感觉 | 服装感觉 | 镜头感觉 |
|------|---------|---------|---------|
| **C** | 平静优雅——还没开始 | 原皮整齐 | 远远看着 |
| **B** | 害羞动摇——有感觉了 | 微敞露肩 | 凑近观察 |
| **A** | 发情呻吟——忍不住了 | 半脱破损 | 怼脸看 |
| **S** | 阿黑颜崩坏——坏掉了 | 全裸/母狗制服 | 直击敏感 |

---

## 7+1模块提示词模板

### 模块1：底座
```
masterpiece, best quality, newest, excellent quality,
```
如配置了风格 LoRA，前置 `<lora:XXX:0.6>,`。前面加 `(explicit:1.2), (nsfw:1.1),`

### 模块2：角色基础（查 Danbooru 标签库）
从 `references/danbooru-character-tags.md` 查角色的核心锚点+外貌特征+标志服饰标签。

**C/B级**（堕落前）：`1girl, [Danbooru标签], pale skin,`
**A/S级**（堕落中/后）：肤色/风格按用户配置变化

### 模块3：表情（词组轰炸）
从 `references/erotic-ranking.md` 按等级选 **8~12** 个同义标签围攻，仅核心加(1.3)。
每个角色有专属堕落配色（眼影/唇釉/指甲）。

### 模块4：肉体
- 萝莉体：`(petite:1.2), flat chest, small breasts, (puffy nipples:1.2), (dark areolas:1.2),` — 不加乳环
- 成女：`(huge breasts:1.3), (thick hips:1.2), (fat ass:1.2), voluptuous, narrow waist,`

### 模块5：服装弧线
C=原皮整齐 → B=原皮微敞 → A=原皮破损+首个BDSM元素 → **S=母狗制服**

**S级母狗制服模板**（9锚点，颜色按角色堕落色填充）：
```
([角色堕落色] latex catsuit:1.3), skintight, high gloss shine, reflective latex,
[堕落色] latex high collar, [配件色] latex choker, [配件],
(heart shaped chest cutout:1.3), exposed nipples,
(crotchless:1.3), [堕落色] latex straps crossing pussy, exposed pussy,
[角色特征 latex 覆盖物],
[堕落色] latex thigh high stockings, stocking feet, no shoes,
[堕落色] latex half gloves,
```

### 模块6：姿势
从 `references/erotic-ranking.md` 姿势池按等级选。双人S级：折叠/侧入/骑乘/口交/后背 5选1。

### 模块7：机位/环境
从 `references/erotic-ranking.md` 镜头池按等级选。姿势/镜头兼容性查 `references/compatibility-matrix.md`。

### 模块8：堕落标记（S级恶堕专用）
身体写字（选3~5个）：`body writing, cum dump on belly, public property on inner thigh, free use on chest, whore on forehead, BBC only above pussy`
烙印/纹身（选配）：`womb tattoo, heart shaped womb tattoo, slave brand`

---

## 硬规则

- 权重上限 **1.3**，不越线
- 位置>数字：前排裸词 > 后排加权
- 同义词围攻 > 单标签高权重
- 萝莉体不加乳环
- 侧面体位 must `from_side`
- ahegao 已自带 rolled eyes/tongue out
- 封面S级+堕落标记 → 收尾可回落B级

### 参数锁死
| 参数 | 值 |
|------|-----|
| Sampler | Euler a |
| Steps | 35 |
| CFG | 4.5 |
| Hires | 4x-UltraSharp 2x, CFG 4, Denoising 0.38 |
| 分辨率 | 832×1216 / 1216×832 |

### 反向词等级联动

| 等级 | 正向服装 | 反向防什么 |
|------|---------|----------|
| C | 全着装 | `(safe:1.3), exposed, naked, completely_naked` |
| B | 微敞 | `(safe:1.2), completely_naked, exposed_pussy` |
| A | 半脱 | `(safe:1.2), buttoned, zipped, fully_dressed` (防AI穿回去) |
| S | 全裸/制服 | `(safe:1.2), clothes, dressed, covered, panties, bra` (防AI加衣服) |

---

## 节奏规则

- 每3~5张换角度/体位/服装
- 特写/全身交替，不连续3张同距离
- 高潮段4张S级4种体位不重复
- 双人图每张换镜头角度

---

## 输出控制

- `"全展开"` → 一次性输出所有场景完整 prompt
- `"一张一张来"` → 逐张输出
- `"生成发布信息"` → 输出中日双语标题+标签

---

## 参考文件

| 文件 | 内容 |
|------|------|
| `references/danbooru-character-tags.md` | 70+角色 Danbooru 标准标签 |
| `references/erotic-ranking.md` | 6维度 S/A/B/C 标签池 + 组合建议 |
| `references/compatibility-matrix.md` | 姿势↔镜头兼容表 |
| `references/character-database.md` | 角色堕落配色速查 |

---

## 已验证种子

| 角色 | 种子 | 备注 |
|------|------|------|
| 流萤 | 3919147288 | 已验证 |
| 知更鸟 | 2117408570 | 已验证（短turquoise hair+green eyes） |
