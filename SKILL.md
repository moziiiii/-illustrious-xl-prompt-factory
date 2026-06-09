---
name: nsfw-prompt-factory
description: >
  NSFW提示词工厂——给角色+主题+N张图，自动推断色情等级弧线并生成完整prompt。
  基于S/A/B/C四级色情等级系统驱动标签选择。13条弧线×60体位配对。
  触发："角色 + 主题 + 多少张图"。也支持单图+随机模式。先出剧情大纲确认，再展开prompt。
compatibility:
  - SD WebUI Forge / ComfyUI
  - Illustrious-XL v2.0 系 (NoobAI/WaiNSFW/HassakuXL 兼容)
  - Renbocloud Style LoRA (0.6, 可选)
---

# NSFW 提示词工厂

## 触发条件

用户输入匹配以下任一模式时激活：
- `[角色名] + [主题/弧线关键词] + [数字]张`
- `[角色名] + [等级S/A/B/C] + [描述]`
- `"帮我写 NSFW prompt"` `"生成一组图"`
- `"随机S级"` `"全展开"` `"一张一张来"`

## 风格配置（用前说一次）

`"我用 Renbocloud"` / `"我用 WaiNSFW"` / `"无 LoRA"` → 底座自动切换。

---

## 核心流程

角色+主题+N → 弧线类型 → 等级分配 → 查 `arc-position-pairing.md` 体位序列 → 场景节拍 → 大纲确认 → 展开prompt

---

## 13条弧线

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
| 圣洁反差 | C→B→A→S | 圣母/新娘→堕落。光环/婚纱+项圈/精液=反差冲击 |
| Pixiv本子 | 山形 | 封面S→建立B→挑逗→核心S高潮→余韵A→收尾B |

N=10 本子弧：封面(S) → ①B ②B ③A → ④⑤⑥⑦S → ⑧A → ⑨B
N=5 堕落弧：C, B, A, S, S

**N≠弧线标准长度时**：
- N<标准：从中段抽掉节拍（先减重复等级）
- N>标准：延长高潮段(S级多重复1~2张)或收尾段
- 速攻/展示/NTR等短弧线 N>4：多余张数插入高潮段，S 级体位扩充不重复
- 不清楚时在大纲阶段问用户"XX弧线标准N=4，你要5张的话我延长高潮段可以吗？"

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
**Renbocloud**：`<lora:IL_Renbocloud Style:0.6>, (explicit:1.2), (nsfw:1.1), masterpiece, best quality, newest, excellent quality,`
**WaiNSFW**：`masterpiece, best quality, newest,` ⚠️ 未验证——WaiNSFW 已内置NSFW风格，(explicit/nsfw)标签效果未知，建议先用单张测试底座
**无 LoRA**：`(explicit:1.2), (nsfw:1.1), masterpiece, best quality, newest, excellent quality,`

### 模块2：角色
- **角色标签**：查 `danbooru-character-tags.md`。C/B级原皮，A/S级堕落色
- **男性**：**不默认，问用户**。首次双人图问"男主类型？BBC/亚裔/白人/无脸？" → `male-character-options.md`
- **FFM/多人**：体型紧贴角色名+权重，衣服只写颜色+choker，表情共享，公用材质末尾统一。⚠️ 多人体位单提示词翻车率高——建议搭配 **Regional Prompter** 或 ComfyUI 区域分层，否则极容易出"融合怪"。纯文本方案：MMF 用 `male behind, male in front` 代替 `male1, male2`，但不保证不融合
- **S级角色锚点防丢失**：堕落越深AI越画不像。每张 S 级强制保留 1 个角色特征锚点——`elf ears`(芙莉莲), `ahoge`(芙宁娜), `horns`(甘雨), `braid`(雷电将军) 等。从 Danbooru 外貌特征查，一行解决

### 模块3：表情 — **词组轰炸+妆容时间线**
从 `erotic-ranking.md` 按等级**精选 5~6 个**标签（不是堆量——是选不同维度的尖兵）。1个眼神+1个嘴部+1个面部泛红+1个精神状态+1~2个体液，核心挂 1.3 其余裸词。角色堕落配色查 `character-database.md`

**体位→表情天然配对**（每个体位对应一个表情区，从该区**选 1 个主方向**展开。注意致命禁区：ahegao 和 rolling_eyes/tongue_out 互斥，二选一）：
- 后背/回眸→**口水系** `tongue_out, drooling, saliva, looking_back`
- 骑乘/正面→**诱惑系** `bedroom_eyes, seductive_smile, smirk`
- 折叠/被压→**阿黑颜系** `ahegao` 或 `crossed_eyes, rolling_eyes` **（二选一，不叠）**
- 口交/跪姿→**口水系+眼泪** `drooling, tears, saliva, tongue_out`
- M字展示→**崩坏系** `twitching, spasm, trembling, mind_broken`

**妆容时间线**（S级张数映射，同阶段内递进）：
```
S级1张 → S4终态 / S级2张 → S2→S4 / S级3张 → S2→S3→S4
S级4张 → S1→S2→S3→S4 / S级5+张 → S1维持1张, S2-S3循环, 最后S4
```
S1: lipstick intact, hair slight mess / S2: lipstick smeared, hair wild / S3: lipstick ruined, hair destroyed / S4: lipstick gone, hair drenched in sweat/cum

### 模块4：肉体
**保持角色原设体型**，从 `character-database.md` 或 `danbooru-character-tags.md` 查。不主动加大胸/肥臀。
- 萝莉体：petite, flat chest **不加乳环**
- 成女原设体型：不额外改成 huge，除非用户要求
**阴毛**：默认无。熟女/BBW/用户指定→加 `pubic_hair, thick_pubic_hair, (black pussy:1.2)` 问用户要不要。

### 模块5：服装弧线
C原皮 → B微敞 → A破损+首个BDSM → **S母狗制服(9锚点latex)**

S级模板见 `latex-catsuit-template.md`。用户说"换颜色/材质"即可改。

**B/A级服装质感轮换**（每张从各列选1~2个，别15张全一样）：
```
破损方式: torn, ripped, unbuttoned, zipper_down, drawstring_loose, side_slit, ass_cutout, pulled_aside
材质(非latex场景): denim, lace, sheer, spandex, fur_trim, see-through, wet_clothes, fine_fabric
装饰: frilled, layered, side_cutout, o-ring, loose_belt, turtleneck, halter_top
```

### 模块6：姿势
查 `arc-position-pairing.md`（13弧线×体位序列）。每级2~3候选，选后从 `position-library.md` 查视角+尺寸+**手部动作**。

**基础动作点缀**（每张加1~2个，增加画面动感）：
```
leaning_forward, leaning_back, arched_back, presenting, hair_tucking,
against_glass, wiping_tears, trembling, twitching, flexing, spinning,
cuddling, dragging, hanging, biting, drinking, drying, bathing
```
C/B级多用 `hair_tucking, leaning, drying`（日常感）。A/S级多用 `trembling, twitching, arched_back, against_glass`（失控感）。别叠超过2个。

### 模块7：机位/环境
按等级选镜头。兼容性查 `compatibility-matrix.md`

**光照自动轮换**（5选1，别15张图同一种光）：
```
① upper half hot pink lower half dark black gradient, dramatic spotlight, moody_lighting
② rim light, strong rim light, dark background, cinematic_lighting
③ golden hour lighting, warm glow, against backlight at dusk, intense shadows
④ volumetric_lighting, Tyndall effect, god rays, atmospheric, dim
⑤ glowing neon lights, purple and blue ambient, dark atmosphere, moody_lighting
```
每3~5张换一种光照。裸露图避免①（粉紫渐变+裸体=容易翻车），选②④。

**场景质感池**（阶段切换时换一组，同阶段内不变。服从阶段场景锁）：
```
地板: tile_floor, wooden_floor, wet_floor, cold_stone_floor, carpet
道具: curtains, mirror, candles, scattered_clothes, window, lamp
织物: rumpled_sheets, pillow, mattress, cushions, torn_fabric
```

**场景骨架 vs 微道具**（解冲突——场景骨架锁死，微道具允许流动）：
- 场景骨架（地板类型/光照方案）：**同阶段完全锁死**，阶段切换才换
- 微道具状态（蜡烛燃尽/床单凌乱/窗帘开合）：**允许同阶段递进**

**场景道具时间线**（微道具——同阶段内递进）：
```
S1(刚开始): candles lit, sheets slightly rumpled
S2(干了): candles half burned, sheets tangled
S3(疯了): candles nearly gone, sheets soaked
S4(坏了): candles out, moonlight/dawn creeping in, sheets destroyed
```

### 模块8：堕落标记（S级专用）
身体写字(3~5部位)：`cum dump on belly, public property on thigh, free use on chest, whore on forehead, BBC only above pussy` 等。分布额头+胸+肚子+大腿+臀。

---

## 硬规则

### 🚨 出图炸了怎么办
- **纯黑/纯白图** → CFG 太高或提示词太短。降 CFG 到 3.5，加场景描述词
- **畸形/触手** → 反向词手脚高压线不够。加 `(extra arms:1.3), (extra legs:1.3), (mutated hands:1.3)`
- **脸崩** → ADetailer 没开。开了还崩→降 Denoising 到 0.35
- **连续3张都翻车** → 退回最后一次成功版本重来，别继续修
- **服装不跟** → 材质锚点太少。B/A 级加材质词（denim/lace/sheer），S 级用 9 锚点模板

| 规则 | 值 |
|------|-----|
| 权重上限 | **1.3** 不越线 |
| 位置>数字 | 前排裸词 > 后排加权 |
| 同义词围攻 | 5-6 裸词(不同维度) > 单(1.5) |
| 采样器 | Euler a |
| Steps | 35 |
| CFG | 4.5 |
| Hires | 4x-UltraSharp 2x, CFG 4, Denoising 0.38 |
| 分辨率 | 832×1216 / 1216×832 |

### Hires锚点
裸体+简单背景=Hires 找不到细节锚点→发焦/虚化/体液翻倍。
- 裸体图 Hires Denoising 降到 **0.32**（Euler a 最低安全值；DPM++ 2M Karras/UniPC 可用 0.25），或加最小锚点(脚环/腰链/项圈)
- 有服装纹理/蕾丝/场景细节的图才用 Denoising 0.38

### 体液
裸体图/近景 `sweat` 一个字够用，最多 `glossy skin`。`body oil, saliva, drool` 在 Hires 时会翻倍溢出。

### 致命禁区
- gyaru 跟等级走(C/B不加, A开始, S全开)
- 萝莉不加乳环 / 侧面体位 must from_side
- ahegao 自带 rolled_eyes/tongue_out/drooling——已用 ahegao 就别叠同效标签。选 ahegao 或 rolling_eyes+tongue_out 组合，二选一
- `head hanging back` + `eye contact` = 💀（物理矛盾：头后仰时颈椎无法正面直视镜头，AI会撕裂脖子）
- `face_close_up` ⊥ `upper_body`（构图层级冲突：close_up裁到胸以上，upper_body要腰部，同框→身体截断）

### 反向词等级联动
**C级** `(safe:1.3), exposed, naked, panties_removed, bra_removed, completely_naked, nsfw`
**B级** `(safe:1.2), completely_naked, no_panties, exposed_pussy, nude`
**A级** `(safe:1.2), buttoned, zipped, tidy, neat, fully_dressed, clothed`（防AI穿回去）
**S级** `(safe:1.2), clothes, dressed, covered, panties, bra, skirt, shirt, dress`（防AI加衣服）
双人不管等级：删 `male, penis, cock`。多人图额外加 `body_horror, merged_bodies, fused_limbs`

### 节奏
每3~5张换角度/体位/服装。4张S级高潮体位不重复。

### 反模板 + 阶段锁（核心——别套模板）

**3 条铁律**：
1. **表情分区选**：S/A 级各有 4 口味区，每张主攻 1 区+从其他借 2 个。体位自动配对表情区（后背→口水系/骑乘→诱惑系/折叠→阿黑颜系/口交→口水+眼泪/M字→崩坏系）
2. **同阶段锁场景**：同阶段地板/床单/道具/光照不变。只换体位+表情+手部+镜头。阶段切换才换场景
3. **4 张 S 级递进**：体位不重复 + 妆容递进(intact→smeared→ruined→gone) + 道具时间线(candles lit→half→gone→morning)

| 阶段 | 场景 | 锁什么 |
|------|------|--------|
| 建立(C/B) | 公开(公会/教室) | 整洁+日常光 |
| 挑逗(B/A) | 私密(卧室/后台) | 布景开始松 |
| 核心(S) | **同场景4张锁死** | 地板/床单/灯光。只换体位+表情+手部+镜头。⚠️ 纯文本换体位背景必偏移——ComfyUI 用户建议加 Depth ControlNet 或 IP-Adapter 锁背景 |
| 余韵(A) | 同场景氛围换 | 床乱/烛灭/晨光 |
| 收尾(B) | 同场景或原点 | 穿衣/坐回 |

**S 级 4 口味区**（ahegao 已自带 rolling_eyes/tongue_out，口味区里的为可选替代品——和 ahegao 之间选 1 个方向不是全部叠加）：
🔥阿黑颜系 `ahegao,crossed_eyes,rape_face,female_orgasm` / 💀黑化系 `dark_persona,crazy,mind_broken,empty_eyes` / 💧崩坏系 `spasm,trembling,twitching,tears,moaning` / 🤤口水系 `saliva,drool_string,long_tongue,cum_on_face,cum_on_tongue`

**A 级 4 口味区**：😈诱惑系 `bedroom_eyes,seductive_smile,smirk,smug,biting_lip` / 😰崩溃系 `scared,despair,anguish,wince,jitome` / 🍷迷醉系 `drunk,heavy_blush,dilated_pupils,sleepy` / 😤抗拒系 `frown,disgust,scowl,glaring`

---

## 用户反馈修改

跑完某张回来改，用户通常说这些——只改对应维度，不动其他模块：

| 用户说 | 只改什么 | 不改什么 |
|--------|---------|---------|
| "表情太重/降一级" | 模块3：换低一级口味区 | 体位/服装/场景不动 |
| "衣服多一点" | 模块5：回退一级服装状态 | 表情/姿势不动 |
| "换个体位" | 模块6：换同级另一候选 | 服装/场景不动 |
| "光不对" | 模块7：换下一个光照预设 | 全部不动 |
| "胸太大了/太小了" | 模块4：改体型词 | 全部不动 |
| "不像角色" | 模块2：检查角色锚点是否漏了 | 全部不动 |

**原则**：一次只改一个维度。不要因为一个表情问题重写整张 prompt。

## 封面→① 闪回标记
封面 S 级(她坏掉的结果) → ① B 级(她曾经的样子)。大纲标注 `⏪ 闪回：时间倒流到堕落之前`。读者看封面好奇"怎么变这样的"，从头看过程。

## 组图完成后检查清单
用户跑完一组，主动问：
- ✅ 服装弧线连贯？(C整齐→B微敞→A破损→S母狗制服)
- ✅ 阶段场景切换合理？(公会→卧室→卧室→卧室→卧室)
- ✅ S级高潮段地板/床单/灯光锁死？
- ✅ 体位 4 张不重复？
- ✅ 每张 S 级有至少 1 个角色特征锚点？
- ✅ 封面有原皮残留+现在堕落对比？
- 有一项不对→指出哪张重新跑

---

## 故障降级
- `arc-position-pairing.md` 缺失 → 默认堕落弧体位：站→坐→跪→后背→折叠
- `position-library.md` 缺失 → 通用姿势 `missionary, doggystyle, cowgirl` + 自己做手部动作
- `erotic-ranking.md` 缺失 → 裸写表情词，不用口味区
- 多人图纯文本 → 默认 MF 模式（MMF/FFM 只在有 Regional Prompter/ComfyUI 时推荐）

---

## 参考文件

| 文件 | 内容 |
|------|------|
| `danbooru-character-tags.md` | 70+角色 Danbooru 标签(人气排行) |
| `erotic-ranking.md` | S/A/B/C 6维度标签池+组合建议 |
| `arc-position-pairing.md` | 13弧线×体位配对序列 |
| `position-library.md` | 60体位(含视角/尺寸) |
| `compatibility-matrix.md` | 姿势↔镜头兼容表 |
| `character-database.md` | 角色堕落配色+验证标记 |
| `cover-templates.md` | 5种封面+对比公式 |
| `latex-catsuit-template.md` | 9锚点母狗服模板 |
| `male-character-options.md` | 男性角色可选模板 |
| `inpaint-hand-fix.md` | Inpaint 手脚修复一体化模板 |
| `advanced-techniques.md` | 进阶技巧：Hires锚点/妆容/体液/多人空间/圣洁反差 |
