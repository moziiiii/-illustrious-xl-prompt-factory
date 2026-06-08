# Illustrious XL Prompt Factory

基于 **Illustrious XL v2.0** 的 NSFW 提示词生成器，一个 **Claude Code Skill**。

## 干什么的

你说：`"纳西妲，堕落线，5张图"`

Skill 自动做这些事：
1. 从 70+ 角色库查 Danbooru 标准标签
2. 把主题匹配到弧线类型（堕落/勾引/露出等）
3. 按弧线形状给每张图分配 S/A/B/C 色情等级
4. 从各级标签池选取表情、服装、镜头、姿势、体液标签
5. 检查姿势和镜头角度是否兼容
6. 先展示人类可读的剧情大纲（不需要懂标签），你确认再展开

## 功能

- **S/A/B/C 四级色情等级系统** —— 每类标签按等级分级。说"B 级表情"就自动选对应标签，不用背几百个 Danbooru 词
- **12 种场景转换弧线** —— 堕落、勾引、露出、NTR、反差、调教、纯爱等，每条弧线有验证过的等级分布曲线
- **70+ 角色 Danbooru 数据库** —— 原神/崩铁/绝区零/鸣潮/芙莉莲/尼尔等角色的精确可查标签
- **姿势→镜头兼容矩阵** —— 25+ 种姿势，标注推荐和禁止的镜头角度
- **Pixiv 封面模板** —— 5 种验证过的封面构图（直视、越肩、M字开脚、跪姿挑逗、半脱）
- **乳胶紧身衣模板** —— 9 个锚点确保服装一致性
- **身体写字模块** —— S 级场景的堕落文字标记
- **等级联动负面提示词** —— 负面提示词随每级的服装状态自适应
- **Pixiv 发布助手** —— 自动生成中日双语标题和标签

## 环境要求

- SD WebUI **Forge** 或 **ComfyUI**
- **Illustrious XL v2.0**（兼容：NoobAI、WaiNSFW、HassakuXL）
- 可选：任意风格 LoRA（Renbocloud 等）

## 安装

```bash
npx skills add moziiiii/-illustrious-xl-prompt-factory
```

或手动：把文件夹复制到 `~/.claude/skills/`

## 快速上手

**风格配置**（每次会话说一次）：
```
"我用的是 Renbocloud LoRA"
"我用的是原版 Illustrious XL"
```

**序列模式**（主要用法）：
```
"雷电将军，堕落线，5张图"
"芙莉莲，Pixiv同人本弧线，10张图"
"纳西妲，从纯洁草神到崩坏母狗，6张图"
"银狼，游戏厅勾引，4张图"
```

**单图模式**：
```
"黄泉，B级表情，站姿"
"阮梅，S级，乳胶紧身衣"
```

**输出控制**：
- `"全展开"` —— 一次输出全部 prompt
- `"一张一张来"` —— 逐张输出
- `"生成发布信息"` —— 生成 Pixiv 标题和标签

## 参考文件

| 文件 | 内容 |
|------|------|
| `references/danbooru-character-tags.md` | 70+ 角色验证过的 Danbooru 标签 |
| `references/erotic-ranking.md` | S/A/B/C 六级标签池（表情/服装/镜头/姿势/体液/场景） |
| `references/compatibility-matrix.md` | 姿势↔镜头兼容性表 |
| `references/character-database.md` | 角色堕落配色方案 |
| `references/position-library.md` | 25+ 姿势库 |
| `references/arc-position-pairing.md` | 弧线↔姿势配对方案 |

## 锁定参数

| 参数 | 值 |
|---|---|
| 采样器 | Euler a |
| 步数 | 35 |
| CFG | 4.5 |
| 高清修复 | 4x-UltraSharp 2x, CFG 4, 降噪 0.38 |
| 分辨率 | 832×1216 / 1216×832 |

## 许可

MIT
