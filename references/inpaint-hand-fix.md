# Inpaint 局部重绘（手脚后修）

## 核心原则
**只写面具里有什么，删光大图词。**

## 修手一体化模板（Forge Inpaint）

**正向词（删光所有大图词，只写这个）**：
```
<lora:IL_Renbocloud Style:0.6>, masterpiece, best quality, newest, excellent quality, renbocloud,
(dark tanned skin:1.1),
[手部动作: gripping / relaxed / resting],
(beautiful hands:1.2), (detailed fingers:1.2), perfect hands, elegant fingers, well-defined fingernails, five fingers,
[指甲颜色: purple / red / pink nail polish],
```

- 手在抓握/摸：加 `(a hand gripping thigh:1.2)` 或 `hand holding, realistic skin contact`
- 修脚：换词为 `(beautiful feet:1.2), (detailed toes:1.2), individual toes, five toes, realistic soles`

**反向词（只留这些）**：
```
bad hands, deformed hands, missing fingers, extra digits, fused fingers, mutated hands, poorly drawn hands, extra fingers,
```

**参数**：
- Inpaint area: `Only masked`
- Mask blur: 4~6
- Denoising: 0.5
- 不满意不改词，连点抽 3~4 次

## 修手（普通）
```
(beautiful hands:1.2), (detailed fingers:1.2), perfect hands, elegant fingers, well-defined fingernails, five fingers,
```

## 修手（抓握/互动）
```
(a hand gripping thigh:1.2), (detailed fingers:1.2), five fingers, perfect hands, hand holding, realistic skin contact,
```

## 修脚
```
(beautiful feet:1.2), (detailed toes:1.2), individual toes, five toes, realistic soles, perfect anatomy,
```

## 手脚终极玄学
- **Hires fix 是救星**：小图手脚糊不怕，姿势没崩就靠 0.38 Denoising 进 4xUltrasharp 自动补
- **大图绝不开 ADetailer hand**：双人纠缠/手指掰穴时 hand_yolov8n 会把脚/大腿识别成"畸形手"修出恐怖片
- **纯靠正反向词 + Euler a 盲盒硬抽**才是正道
