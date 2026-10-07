# UI/UX 动效交互 Skill

[![version](https://img.shields.io/badge/version-0.2-blue?style=flat-square)](https://github.com/c2311395651-stack/ui-motion-skill/releases) [![license](https://img.shields.io/badge/license-MIT-green?style=flat-square)](https://github.com/c2311395651-stack/ui-motion-skill/blob/main/LICENSE) [![motions](https://img.shields.io/badge/动效库-6_个-orange?style=flat-square)](https://github.com/c2311395651-stack/ui-motion-skill/blob/main/skills/ui-motion/references/motions.md)

给 AI 编码助手（Claude Code、Codex、Cursor 等）用的动效技能包。

**装上之后，AI 做界面时会自动加上有细节、有手感的交互，而不是"点一下就硬切"。**

动效库会跟着抖音视频每期更新。

---

## 快速开始

### 安装

```bash
npx skills add https://github.com/c2311395651-stack/ui-motion-skill
```

### 使用

装好后，在对话里直接说：

> 用 ui-motion 给这个页面加上弹出菜单  
> 做一个旅行 App 的目的地列表，卡片点开要有展开动画  
> 用 ui-motion 实现一个果冻开关

**AI 会按动效库里的参数（时长、曲线、间隔）来写代码，不用你再复制提示词。**

---

## 不装 skill 也能用

每期视频的提示词都整理在 `prompts/` 文件夹，直接复制给 AI 也行：

| 期 | 主题 | 提示词 |
|---|---|---|
| 第01期 | 4 个按钮动效：点赞爆粒子、提交按钮三连变、果冻开关、长按确认 | [按钮动效.md](prompts/按钮动效.md) |
| 加餐 | 2 个弹出交互：按钮长出菜单、卡片展开详情 | [弹出交互.md](prompts/弹出交互.md) |

---

## 动效库（skill 内置）

装了 skill 的话，AI 会自动按这些动效的完整参数实现，你只需要说场景：

| 编号 | 动效 | 适用场景 |
|---|---|---|
| 01 | 变形菜单 Morph Menu | 悬浮按钮展开快捷操作 |
| 02 | 卡片展开 Expand Card | 列表卡片进入详情 |
| 03 | 点赞爆粒子 Like Burst | 点赞、收藏、关注 |
| 04 | 提交按钮三连变 Submit Morph | 提交、支付、保存等待结果 |
| 05 | 果冻开关 Jelly Toggle | 设置开关、深色模式切换 |
| 06 | 长按确认 Hold to Confirm | 删除、注销等危险操作 |

每个动效都有：
- ✅ 什么时候用、怎么实现、完整参数
- ❌ 什么时候不用（避免 AI 用错场景）
- 📝 对应的一句话提示词（复制即用）

**完整动效库**：[motions.md](skills/ui-motion/references/motions.md)

---

## 核心能力

### 1. 按场景自动选择动效

你说"做个点赞按钮"，AI 会自动用 **点赞爆粒子**（爱心弹起 + 粒子炸开 + 数字滚动）。

你说"删除前确认一下"，AI 会用 **长按确认**（进度环走满才删除），而不是弹一个"确定吗？"。

### 2. 严格遵守参数规范

- **时长分级**：小元素 150–250ms / 面板 300–450ms / 转场 450–600ms
- **曲线规则**：进场 ease-out / 退场 ease-in / 弹性用轻微回弹（超调不超过 8%）
- **性能规则**：只动 transform 和 opacity，避免触发重排
- **层次感**：一组元素依次出现，间隔 30–60ms

**完整规范**：[foundations.md](skills/ui-motion/references/foundations.md)

### 3. 交付前自检反廉价

AI 写完代码后会按检查清单逐条自检：
- 是不是用了 linear 曲线？
- 元素是不是一起蹦出来的？
- 按钮有没有按下态和 hover 态？
- 有没有尊重 prefers-reduced-motion？

**完整清单**：[anti-cheap.md](skills/ui-motion/references/anti-cheap.md)

---

## 更新记录

- **v0.2**（2026-10-07）：第01期 · 4 个按钮动效（点赞爆粒子、提交按钮三连变、果冻开关、长按确认），动效库扩充到 6 个。每个动效补充了"什么时候不用"和完整参数。新增 `foundations.md` 通用规则文档。
- **v0.1**（2026-10-06）：首发，收录 2 个弹出交互动效（变形菜单、卡片展开）和一份反廉价自检清单。

---

## 对应视频

每期视频的选题、动效演示和提示词对比：

- 第01期：[4个提示词，让AI做出有手感的按钮](#)（发布后补链接）
- 测试样片：[AI做的界面总差点意思？这2个弹出交互，一句提示词就能做](#)

---

## 许可

MIT，可以免费商用。

动效库会跟着视频每期更新，想要最新的动效，重新运行一遍安装命令即可：

```bash
npx skills add https://github.com/c2311395651-stack/ui-motion-skill
```
