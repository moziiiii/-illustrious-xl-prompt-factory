# 进阶技巧（实战踩坑总结）

## Hires 锚点理论

裸体+简单场景=Hires找不到细节锚点→发焦/虚化/液体暴增。
- 裸体图降 Hires Denoising 到 **0.25**，或加最小锚点：`anklet, waist chain, thigh strap, collar`
- 有服装纹理/蕾丝/场景细节的图，Denoising 0.38 才安全

## 妆容双模式

- **精简模式**：`heavy slutty makeup` 一个词，适合远/中景
- **轰炸模式**：`thick eyeshadow, heavy eyeliner, long false lashes, glossy lipstick, mascara, dramatic makeup, heavy blush` 七个词围攻，适合近景/封面

## 体液精简

裸体图 Hires 时 `body oil, glossy skin, saliva, drool` 会翻倍溢出。
- **穿衣服+中远景**：用全套体液词围攻 `sweat, glossy skin, body oil, after sex glow`
- **裸体图/近景**：`sweat` 一个字够用，最多加 `glossy skin`

## 退回到成功版本

连续翻车不要继续修 prompt。回退到最后一次出图成功的版本，从那里改。

## 角色 LoRA 取舍

- **热门角色**（雷电将军/纳西妲/芙莉莲等）：不挂角色 LoRA 也能稳定出图，Danbooru 标签够用
- **冷门角色**：才需要挂角色 LoRA
- 挂了角色 LoRA 会锁死表情/姿势灵活性，堕落场景反而不如不挂

## 自然语言写字

- **英文** ✅：`body writing, cum dump written on belly, slut` — AI 能画出来
- **中文** ❌：AI 画中文身体字的成功率极低，别用

## POV 深喉方图

1024×1024 方图模板：
```
close-up, POV, from_below, looking_up, face_focus,
kneeling, looking up at viewer, tongue out, oral_invitation,
outdoors/alley, public, risky,
```
适用场景：口交POV、颜射前、深喉表情特写

## 圣洁反差弧

圣母/新娘+恶堕视觉模板：
```
holy symbol still present, (wedding dress/nun habit:1.2), stained glass window, church altar,
cross on wall, scattered rose petals,
halo cracked/flickering/dimming,
corrupted bride, fallen saint,
```
核心张力：圣洁符号（光环/婚纱/教堂）+ 堕落痕迹（项圈/精液/撕裂）= 反差冲击

## 多人空间方位

spitroast/前后夹击时：
- ✅ `male behind, male in front` — AI 理解空间位置
- ❌ `male1, male2` — AI 分不清谁是谁
- MMF 图两个男性用物理位置描述，不用编号
