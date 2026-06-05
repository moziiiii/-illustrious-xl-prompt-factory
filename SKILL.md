---
name: nsfw-prompt-factory
description: >
  NSFW提示词工厂——给角色+主题+N张图，自动推断色情等级弧线并生成完整prompt。
  基于S/A/B/C四级色情等级系统驱动标签选择。
  触发：当你描述"XX角色 + 一个主题 + 多少张图"时——例如"纳西妲 堕落 5张"、"芙莉莲 酒馆勾引到野战 4张"、"阮梅 反差母狗 6张"。
  也支持单图："阮梅 B级表情 站姿"。先出剧情大纲确认，再展开完整prompt。
compatibility:
  - SD WebUI Forge
  - Illustrious-XL v2.0 系模型
  - Renbocloud Style LoRA (0.5~0.6)
---

# NSFW提示词工厂

## 核心流程（一句话）

```
你：角色 + 主题 + N张图
    ↓
Skill：主题→弧线类型 → N张图的等级分配 → N个场景节拍 → 规划预览
    ↓
你：确认/改
    ↓
Skill：展开完整prompt（7模块+反向词+参数）
```

---

## 输入格式

### 序列模式（主要用法）
```
[角色]，[主题描述]，[N张图]
```

**示例**：
- `"纳西妲，从纯洁草神被腐化成须弥精厕，5张"`
- `"芙莉莲，高冷精灵在酒馆被灌醉勾引到野战，4张"`
- `"雷电将军，威严外表私下是反差母狗，6张"`
- `"闲云，BBW熟女诱惑展示，4张"`

### 单图模式
```
[角色]，[等级/描述]，单图
```

### 随机模式（新增）
```
[角色]，随机S级，[N张]
[角色]，随机 [维度]，[N张]
```
**用法**：骰一个 S 级核心标签 → 关联展开所有维度。
- `"随机S级"` → 从S级标签池随机选一个核心（如 `ass_focus`）
  → 关联展开：姿势=doggy_style / 角度=from_behind / 服装=latex_microskirt / 表情=looking_back+tongue_out
  → 所有维度自动匹配该核心的"自然搭档"
- `"随机 ass_focus"` → 指定核心标签，其他维度随机关联展开
- 适合"不知道今天画什么"时快速出灵感

---

## 工作流

### Step 1：解析输入

1. 提取**角色名** → 查 `references/character-database.md` 获取原设
2. 提取**主题关键词** → 判断弧线类型
3. 提取**图片数量** N

### Step 2：主题 → 弧线类型

| 主题关键词 | 弧线类型 | 等级方向 | 弧线形状 |
|-----------|---------|---------|---------|
| 堕落、腐化、恶堕、黑化、corrupt、fall | **堕落弧** | C→S | 逐级沉沦 |
| 调教、驯服、break、服从、training | **调教弧** | C→S | 每张=一层枷锁脱落 |
| 反差、私下、真实面目、ギャップ | **反差弧** | B→S→A→S | 揭露→沉溺→余韵→再犯 |
| 展示、写真、gallery、showcase | **展示弧** | B→S→A→S | 穿→脱→高潮→事后 |
| 勾引、诱惑、seduce、tease、酒馆 | **诱惑弧** | B→A→B→A→S | 挑逗→退缩→再挑逗→失控 |
| 露出、公开、public、野外、exhibition | **露出弧** | B→A→S→S | 紧张→暴露→高潮→破罐破摔 |
| NTR、寝取、夺走、stolen | **寝取弧** | C→B→A→S | 忠诚→动摇→沦陷→背叛 |
| 速堕、直接、quick、野战 | **速攻弧** | B→S→S | 直接开干，第一张还在穿最后已崩 |
| 浪漫、纯爱、love、甘い | **浪漫弧** | C→B→A→B | 青涩→升温→亲密→温存 |
| 穿衣、穿回去、事后穿衣、dress up | **穿衣弧** | S→A→B→C | S全裸→A穿内衣→B半遮→C整齐（被操完后被迫穿上衣服——"遮羞"的色） |
| 反复、play、戏态、欲拒还迎 | **戏态弧** | B→S→B→S | B扮清纯→S突然放纵→B假装正常→S又忍不住——"反复横跳" |
| 本子、Pixiv、同人志、doujinshi、封面 | **Pixiv本子弧** | **山形** | 封面钩子(A)→建立(B)→挑逗(B→A)→核心高潮(全S)→余韵(A)→收尾(B)。**高潮在中段，不是最后！** |

**如果主题描述不够清晰**：问用户"这个主题是偏向堕落/反差/展示/勾引/穿衣/反复/本子哪种？"

### Step 3：N张图 → 等级分配

根据弧线形状和 N，分配每张图的等级：

**堕落弧（线性 C→S）**：
- N=3: C, A, S
- N=4: C, B, A, S
- N=5: C, B, A, S, S（最后一张S停留收尾）
- N=6: C, B, B, A, S, S
- N=8: C, B, B, A, A, S, S, S

**反差弧（波动 B→S→A→S）**：
- N=4: B, A, S, A
- N=5: B, A, S, A, S
- N=6: B, A, S, A, S, A

**展示弧（波动 B→S→A→S）**：同反差弧

**诱惑弧（挑逗 B→A→B→A→S）**：
- N=4: B, A, B, S
- N=5: B, A, B, A, S
- N=6: B, A, B, A, S, S

**调教弧**：同堕落弧但每张强调一个"训练步骤"
**露出弧**：B, A, S, S（最后两S=破罐破摔）
**速攻弧**：B, S, S（第一张=开始，之后=堕落）
**浪漫弧**：C, B, A, B（不越过A级，保持温柔）
**寝取弧**：C, B, A, S

**Pixiv本子弧（山形 N张）** —— 封面结果预告 + 从头叙事 + 高潮中置：
- **封面=S级**（已堕落的结果——"她变成了这样"），①开始=B级从头叙事（"她曾经是这样"），高潮在60-80%位置
- N=10: 封面(S-结果预告) → ①建立(B) → ②挑逗(B) → ③挑逗(A) → ④⑤⑥⑦核心(全S) → ⑧余韵(A) → ⑨收尾(B)
- N=15: 封面(S) → ①建立(B) → ②③建立(B) → ④⑤挑逗(B/A) → ⑥⑦⑧⑨⑩⑪核心(全S) → ⑫⑬余韵(A) → ⑭收尾(B)
- N=20: 封面(S) → ①建立(B) → ②③建立(B) → ④⑤⑥挑逗(B/A) → ⑦⑧⑨⑩⑪⑫⑬⑭核心(全S) → ⑮⑯⑰余韵(A) → ⑱⑲收尾(B)
- **封面=全S级，①开始从头B级叙事**——读者看封面好奇"她怎么变这样的"，然后从①开始看过程

### 封面模板（5种，Pixiv热门验证）

Pixiv本子弧的封面独立于剧情，用A级（半脱/诱惑）当钩子——**半脱比全裸更吸引点击**。

| # | 封面类型 | 姿势 | 镜头 | 观感 |
|---|---------|------|------|------|
| ① | **正面诱惑直视** | 正面站立，双手托胸/手指含嘴里/轻捏乳头，直视镜头 | front_view, bust/upper_body | "她在看着你自摸"——直接眼神接触 |
| ② | **背面翘臀回眸** | 背对镜头，塌腰翘臀，回眸看镜头，手拉内裤边 | from_behind, looking_back, ass_focus | "回头看你——屁股是主角" |
| ③ | **躺姿M字腿** | 仰躺，M字张开腿，一手遮嘴，一手向下 | from_above, between_legs, full_shot | "什么都没藏"——完全暴露但表情害羞 |
| ④ | **跪姿口交暗示** | 跪在镜头前，上身前倾，张嘴伸舌，向上看 | from_below, looking_up, tongue_out | "服务感"——顺从的诱惑 |
| ⑤ | **动态脱衣中** | 正在脱/穿衣服（bra半挂、裙子滑落），身体扭转 | three-quarter_view, motion_lines | "下一秒就要发生"——故事感 |

**封面选择原则**：根据角色和主题从5种中选最匹配的。默认用①正面诱惑——点击率最高。

### 封面对比公式（Renbocloud验证——最重要）

**封面不是"A级诱惑照"，是"堕落的中间帧"。**

```
封面 = 至少1个"过去"残留 + 至少1个"现在"堕落 + 直视镜头
```

| 元素类型 | 是什么 | 示例 |
|---------|------|------|
| **过去残留** | 原本身份的碎片——武器、头饰、原服装碎片、翅膀、光环 | 纳西妲的金色头冠还在、芙莉莲的法杖握在手里、雷电将军的太刀、甘雨的角 |
| **现在堕落** | 母狗化的痕迹——项圈/链子、乳胶/皮革、阿黑颜、精液、撕裂的渔网袜 | 黑皮革项圈锁喉、精液挂脸上、法袍撕裂敞开、thighhighs扯烂 |
| **对比张力** | 两个时间线在一个画面里碰撞 | 头冠还在但项圈也锁上了——"智慧女神已经变成母狗" |

**为什么有效**：读者一秒看到"她原来是谁→现在变成了什么"→好奇她怎么从A变到B→点进去看整组。

**每个角色选1~2个"原皮残留"元素**作为她的堕落对比物——这些元素在她的S级图里也要偶尔出现（歪斜、破损地挂在身上），形成贯穿全组的视觉锚点。

### 节奏规则

Pixiv热门本子的节奏不是均匀的——要像呼吸，有张有弛：

- **每3~5张换一个元素**：换角度(正面→侧面→俯视→仰视) 或 换体位 或 换服装状态
- **特写/全身交替**：不要连续3张同距离，close-up→full_shot→medium_shot轮换
- **单人/双人交替**（双人本子）：单人展示→双人动作→单人表情特写→双人换体位
- **高潮段全S但维度微调**：S1=后背位、S2=骑乘位、S3=折叠位、S4=口交——4张S级4种体位，不重复

### Step 4：生成场景节拍

为每张图分配一个**场景节拍标题+一句话剧情**。节拍要和等级匹配，形成连贯叙事。

**堕落弧示例（纳西妲 5张）**：
```
① C级「净善宫的智慧主」——圣洁祈祷，闭目微笑
② B级「第一次的触碰」——被囚禁，衣衫微乱，困惑脸红
③ A级「快感的裂缝」——身体背叛意志，仰头呻吟，衣袍半褪
④ S级「须弥精厕的诞生」——完全臣服，阿黑颜折叠压杀
⑤ S级「智慧女神的终局」——M字开腿精液横流，母狗化完成
```

**诱惑弧示例（芙莉莲 4张）**：
```
① B级「酒馆的视线」——从斗篷下偷看，微醺脸红
② A级「勇者的邀请」——爬上他的腿，衣袍滑落，咬唇诱惑
③ B级「怯场的退缩」——突然害羞转头，欲拒还迎
④ S级「沦陷的精灵」——再也忍不住，骑乘位阿黑颜
```

### Step 5：先出等级速查 + 剧情大纲（不等确认不展开）

**输出等级速查表**——让不看色级表的人也能一眼看懂每级是什么感觉：

```markdown
## 等级速查（给不熟标签的人）

| 等级 | 表情给人的感觉 | 服装给人的感觉 | 镜头给人的感觉 |
|------|--------------|--------------|--------------|
| **C** | 平静、优雅、禁欲——"还没开始" | 穿得整整齐齐、原皮/日常服 | 正常距离看全身——"远远看着" |
| **B** | 害羞、动摇、脸红——"有点感觉了" | 微敞、露肩、掀裙子——"开始松了" | 靠近了、侧面偷看——"凑近观察" |
| **A** | 发情、呻吟、沉迷——"已经忍不住了" | 半脱、破损、情趣材质——"差不多没了" | 贴脸特写、偷窥角——"怼脸看" |
| **S** | 阿黑颜、崩坏、失控——"彻底坏掉了" | 全裸、开裆、只有项圈——"一丝不挂" | 直击两腿之间——"镜头伸进去了" |
```

**然后输出剧情大纲**——表格里写的是"观感"，不是英文 tag：

```markdown
## 剧情大纲
**角色**：纳西妲 (short white hair, green eyes, petite, flat chest — 萝莉体型)
**主题**：堕落弧 C→S，从纯洁草神到彻底崩坏
**张数**：5张

| # | 节拍 | 等级 | 这张图的观感 |
|---|------|------|------------|
| ① | 净善宫的智慧主 | **C** | 远远看着穿整齐神官袍的纳西妲，她闭眼微笑、双手合十祈祷，圣洁不可侵犯 |
| ② | 第一次的触碰 | **B** | 近了——她坐在桌上，衣袍微敞露出肩膀，脸红害羞地看向你，腿不自觉地微微张开 |
| ③ | 快感的裂缝 | **A** | 俯拍她仰头呻吟，神官袍撕裂半挂在身上，发情的眼神咬着嘴唇，已经被后入 |
| ④ | 须弥精厕的诞生 | **S** | 双腿之间仰视——阿黑颜翻白眼流口水，全裸只戴项圈拴着链子，被折叠压杀插到深处 |
| ⑤ | 智慧女神的终局 | **S** | 正上方俯拍M字开腿，脸上身上精液横流，手指掰开还在滴精的穴——曾经的智慧神已经没了 |

**修改方式**：不用记标签，直接说"第X张再色一点"、"第X张衣服多一点"、"换个体位"就行。
确认后展开完整 prompt？
```

### Step 6：展开完整 prompt（确认后）

按 7 模块格式输出每组 prompt。**每张图是独立的一次生成**。

---

## 7模块正向词模板

### 模块1：底座（焊死）
```
<lora:IL_Renbocloud Style:0.6>, (explicit:1.2), (nsfw:1.1), masterpiece, best quality, newest, excellent quality, renbocloud,
```

### 模块2：角色基础（从数据库查表 + Danbooru 标签）

**C/B级原皮服装不靠猜——从 `references/danbooru-character-tags.md` 查角色的「标志服饰」标签，直接用 Danbooru 标准 tag。**

**单人**：
- **C/B级**：`1girl, [Danbooru核心锚点], [Danbooru外貌特征], [Danbooru标志服饰], pale skin,` — 不加 gyaru
- **A级**：`1girl, [Danbooru核心锚点], [Danbooru外貌特征], (dark tanned skin:1.1), gyaru,` — 原皮 Danbooru 标签改成 torn/open/slipping
- **S级**：`1girl, [Danbooru核心锚点], [Danbooru外貌特征], dark tanned skin, gyaru,` — 原皮标签换成母狗制服

**双人**：同上，男性固定 `black man, muscular, dark skin, huge black cock, bbc`

**为什么用 Danbooru 标签而不是自己写？** Danbooru 是 Illustrious-XL 的训练数据来源——用它的标签=AI 最容易识别角色。写 `frieren, sousou no frieren` 比写 `frieren from sousou no frieren` 准确 10 倍。

### 模块3：表情（词组轰炸策略 + 角色堕落配色）

**实测验证：15+同义词围攻的叠加效果远超单一标签加权。**
- 从色级表表情池按等级选 **8~12 个** 标签，形成强度叠加
- S级：`ahegao, rape_face, female_orgasm, crossed_eyes, rolling_eyes, drooling, saliva, drool_string, long_tongue, tongue_out, heavy_breathing, moaning`——12个同义围攻比1个加权强3倍
- A/B级同样：用标签密度代替权重数字
- 仅核心灵魂标签（如 `ahegao`）挂 1.3 锚定方向，其余裸词围攻

**角色堕落配色（S级/A级必须用，每个角色不一样）：**
- 每个角色有自己的"堕落色"=原皮色的黑暗颠倒版，贯穿眼影/唇釉/指甲/母狗制服
- 芙莉莲：原皮白+蓝→**deep purple**。眼影`deep purple eyeshadow, thick purple eyeliner`、唇釉`glossy dark plum lipstick`、指甲`purple nail polish`
- 纳西妲：原皮绿→**荧光绿/dark green**。眼影`green eyeshadow, dark green eyeliner`
- 雷电将军：原皮紫→**深紫偏黑**。眼影`dark purple eyeshadow`
- 阮梅：原皮白+绿→**酒红/dark red**。眼影`burgundy eyeshadow`
- S级母狗制服配色要跟眼影/唇釉/指甲同色系：芙莉莲=黑皮革+紫光泽项圈、纳西妲=绿乳胶+绿宝石项圈
### 模块4：肉体 → 萝莉/成女模板 + 手脚挂件
### 模块5：服装（服装也有一条堕落叙事线）

**不是按等级随机选标签——服装也有自己的弧线。**

| 等级 | 服装逻辑 | 示例（芙莉莲） |
|------|---------|--------------|
| **C** | 原著原皮，完整整洁 | 白色法师袍+蓝色披风，整齐穿戴 |
| **B** | 原皮微敞——扣子松一颗、肩带滑落、裙摆掀 | 法袍从肩滑落一点，露锁骨，thighhighs |
| **A** | 原皮破损/撕裂/半脱 + 开始出现BDSM元素 | 法袍撕裂敞开，胸部露出，项圈出现 |
| **S** | 完全替换为母狗化制服 | 黑乳胶/黑皮革/渔网/项圈+链子/开裆/全裸 |

**S级母狗制服规则**（S级不穿全裸——穿了母狗服比全裸更色）：

**为什么 latex catsuit 模板出图一致性 80%？** 单一材质+全身包裹+多个锚点（领口/项圈/铃铛/开胸/开裆/手套/袜）= AI 有足够锚点锁住服装。

**母狗服通用结构**（9个锚点，按角色配色）：
```
([角色堕落色] latex catsuit:1.3), skintight, high gloss shine, reflective latex,
[色] latex high collar, [色] latex choker, [配件:bell/bow/gem],
(heart shaped chest cutout:1.3), exposed nipples,
(crotchless:1.3), [色] latex straps crossing pussy, exposed pussy,
[色] latex thigh high stockings, stocking feet, no shoes,
[色] latex half gloves,
```

**各角色母狗服配色**：

| 角色 | 主色 | choker/配件 | 特殊适配 |
|------|------|-----------|---------|
| 芙莉莲 | **deep purple latex** | black latex choker + silver bell | purple latex pointed elf ear covers |
| 纳西妲 | **green latex** | gold latex choker + golden bell | green latex leaf-shaped ear covers |
| 雷电将军 | **dark purple latex** | black latex choker + purple electro bell | dark purple latex hair ornament |
| 阮梅 | **burgundy latex** | black latex choker + red gem bell | burgundy latex hair clips |
| 通用 | **black latex** | white latex choker + gold bell | 按角色特征加 latex 配件 |

**为什么加 ear covers/tail sleeve？** 原版模板证明了：给角色的非常规部位（耳朵/尾巴/角）加 latex 覆盖物，既保持角色辨识度又新增锚点——AI 更容易锁住整套服装。

**用户可以换母狗服**：说"换白色乳胶"、"换黑皮革不要latex"、"铃铛换蝴蝶结"、"开胸改全包"——模板保持9锚点结构不变，只换配色/材质/配件。每次生成时自动填角色堕落色+角色特征。

**S级母狗制服池**（堕落完成后穿的，不是原皮）：
```
black latex, black leather, fishnets, crotchless, exposed_pussy,
spiked collar, leather collar, leash, chains, handcuffs, bound_wrists,
microskirt, thong, string_bikini, see-through, sheer, skintight,
torn_thighhighs, black_thighhighs, garter_straps, high_heels
```
搭配原则：材质选1~2个（latex+leather经典），结构选开裆/全裸，配件项圈+链子必带。

**为什么S级不能穿原皮？** 原皮是角色身份的象征。堕落=放弃原本身份=换上母狗的制服。原皮出现在S级只应该是一种情况：破烂地挂在身上/地上作为对比。
### 模块6：姿势 → 查 `references/arc-position-pairing.md`

**不是随机抽——是弧线叙事驱动。** 每条弧线=一条体位序列，每等级有 2~3 候选。
从配对表选体位后，从 `references/position-library.md` 查推荐视角+尺寸。
### 模块7：机位/环境 → 从色级表镜头池按等级选 + 场景背景 + 氛围

### 模块8：堕落标记（S级恶堕专用）

**身体写字**——选 3~5 个分布在身体不同部位：
```
body writing,
(cum dump written on belly:1.2),
(public property written on inner thigh:1.2),
(free use written on chest:1.2),
(whore written on forehead:1.2),
(BBC only written above pussy:1.2),
(slut written on ass:1.2),
(owned written on lower back:1.2),
(arrow pointing down to pussy:1.1),
```
**烙印/纹身**（选配）：
```
womb tattoo, heart shaped womb tattoo, slave brand, barcode tattoo,
```
**分布原则**：额头+胸+肚子+大腿内侧+臀部各1个，不堆在同一区域。

---

## 批量输出

用户说 **"全展开"** 或 **"全部输出"** → 一次性输出所有场景完整 prompt，不用每张确认。
用户说 **"一张一张来"** → 逐张输出。

---

## 已验证种子（可选参考）

| 角色 | 好种子 | 备注 |
|------|--------|------|
| 流萤 | **3919147288** | 已验证 |
| 知更鸟 | **2117408570** | 已验证（注意：短turquoise hair+green eyes，不是长粉紫发） |

输出 prompt 时可附带提醒 `Seed: [数字]`（如果数据库有对应角色种子）。

---

## 硬规则

### 权重金字塔
- **上限 1.3**，不越线
- **位置 > 数字**：前排裸词 > 后排加权
- **同义词围攻**：`sweat, glossy skin, body oil, wet` > `(sweat:1.5)`

### 参数锁死
| 参数 | 值 |
|------|-----|
| Sampler | Euler a |
| Steps | 35 |
| CFG | 4.5 |
| Hires | 4x-UltraSharp 2x, CFG 4, Denoising 0.38 |
| ADetailer | 只开脸 |
| 分辨率 | 832×1216(竖) / 1216×832(横) |

### 致命禁区
- **gyaru 跟等级走**：C/B级不加 gyaru/dark_tanned_skin（还没堕落就是原皮），A级开始加(dark tanned skin:1.1)+gyaru，S级全开 dark_tanned_skin+gyaru。gyaru 是堕落程度的视觉标记。
- 萝莉体不加乳环/乳钉
- 侧面体位 must `from_side`
- 双人图用 `(eye contact:1.3)` 不用 `looking up at viewer`

### FFM/多人图铁律（实战验证）

**不是 MF 的简单叠加——AI 对多角色的处理天然不稳定。**

| 规则 | 说明 |
|------|------|
| **体型紧贴角色名** | 每个角色名后紧跟 `(体型:权重)`，中间不插其他标签。`frieren, (slim:1.2), (medium breasts:1.2)` ✅ / `frieren, slim, medium breasts` ❌ |
| **两人同姿 > 分工** | 两人做同一个动作稳定，分工型体位（一人骑脸一人口交）AI 训练数据少，出图率低。选 FFM 体位优先"并排同动作" |
| **衣服只写颜色+choker** | 不要给每个角色写完整 9 锚点 latex 标签。每人写 `[颜色] latex catsuit, [颜色] choker, [配件]` + 角色特征覆盖物，公用材质放末尾统一 |
| **表情共享一套** | 不分别写两个人的表情。所有角色的表情词只写一次，共享 |
| **展示图加性行为压制** | 正向加 `posing, display` / 反向加 `penis_inside, penetration, doggy_style, missionary, cowgirl, blowjob, sex, fucking` 防止 AI 看到 `crotchless+BBC` 后自动补全性行为 |
| **颜色强对比** | 多角色母狗服用不同颜色（一粉一黑 / 一绿一紫），AI 分得清 |
- ahegao 已自带 rolled eyes/tongue out
- `face_close_up` 和 `upper_body` 互斥
- `head hanging back` + `eye contact` = 颈椎骨折

### 反向词等级联动（不是只改 safe，是防什么就反向加什么）

| 等级 | 正向服装状态 | 反向词侧重 | 核心防什么 |
|------|------------|----------|----------|
| **C** | 全着装 | 防意外暴露 | `(safe:1.3), exposed, naked, panties_removed, bra_removed, completely_naked` |
| **B** | 微敞/露肩 | 防脱光 | `(safe:1.2), completely_naked, no_panties, exposed_pussy` |
| **A** | 半脱/破损 | 防AI"好心穿回去" | `(safe:1.2), buttoned, zipped, tidy, neat, fully_dressed, clothed` |
| **S** | 全裸/开裆 | 防AI"自作主张穿上" | `(safe:1.2), clothes, dressed, covered, panties, bra, skirt, shirt, dress` |
- **双人**：不管等级都删 `male, penis, cock`
- S级反向词去掉了衣服相关词后，AI不会再试图给全裸角色画衣服

- **服装弧线**：C原皮整齐→B原皮微敞→A原皮破损+首件BDSM元素→**S抛弃原皮换母狗制服**(latex/leather/collar/leash/crotchless)。S级不穿原皮，除非原皮破烂挂身上作对比。

---

## 输出格式

```text
=== 场景N：节拍标题（等级：X，单/双人）===

【正向词】
<lora:IL_Renbocloud Style:0.6>, (explicit:1.2), (nsfw:1.1), masterpiece, best quality, newest, excellent quality, renbocloud,
[7模块标签]

【反向词】
[按等级+单/双人选]

【参数】
Steps: 35, CFG: 4.5, Sampler: Euler a, Size: 832×1216, Hires: 4x-UltraSharp 2x @0.38
```

---

## P站发布辅助

组图完成后，用户说 **"生成发布信息"** → 输出：
```markdown
**标题**：[中文标题]
**说明**：[中文简介，1-2句]
**标签**：[日文标签] [中文标签]
```
标签格式遵循用户发布规则：标题说明用中文，标签中日双语。Danbooru 标签在前，中文在后。

---

## 常用角色数据库速查

完整库见 `references/character-database.md`。

| 角色 | 英文名 | 体型 | 特殊规则 |
|------|--------|------|---------|
| 纳西妲 | nahida, genshin impact, short white hair, green eyes | petite, flat chest | **不加乳环** |
| 芙莉莲 | frieren, sousou no frieren, long white hair twin tails, green eyes, elf ears | slim, medium breasts | — |
| 阮梅 | ruan mei, honkai star rail, long dark hair, purple eyes | curvy, big breasts | **必配高跟鞋** |
| 闲云 | xianyun, genshin impact, long gray hair, glasses | curvy, huge breasts, thick | BBW模板 |
| 雷电将军 | raiden shogun, genshin impact, long purple hair | curvy, big breasts | — |
