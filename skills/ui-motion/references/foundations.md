# 动效设计基础原则

不管做哪个具体动效,这些底线都要满足。

## 时长分级

不要一刀切用 300ms,根据元素大小和层级选择:

| 元素类型 | 推荐时长 | 例子 |
|---|---|---|
| 小元素、状态反馈 | 150–250ms | 按钮点击反馈、图标旋转、checkbox 打勾 |
| 中等面板、卡片 | 300–450ms | 弹出菜单、卡片展开、抽屉展开 |
| 整页转场、大容器 | 450–600ms | 页面切换、全屏模态框 |
| 任何动效 | 不超过 700ms | 再长就是卡顿了 |

## 曲线选择

**禁止 linear**。直线运动像机器人,必须用缓动曲线。

| 场景 | 曲线 | 值 |
|---|---|---|
| 进场(淡入、滑入) | ease-out | `cubic-bezier(0, 0, 0.2, 1)` |
| 退场(淡出、滑出) | ease-in | `cubic-bezier(0.4, 0, 1, 1)` |
| 双向(hover、按压) | ease-in-out | `cubic-bezier(0.4, 0, 0.2, 1)` |
| 轻微回弹(点赞、切换) | 弹簧曲线 | `cubic-bezier(0.34, 1.56, 0.64, 1)` 或 `spring(1, 80, 10)` |

**回弹分寸**: 超调不超过 8%。弹到 1.08 可以,弹到 1.2 就过了。

## 性能规则

**只动 transform 和 opacity**,避免触发重排(reflow)。

| ✅ 推荐 | ❌ 避免 | 原因 |
|---|---|---|
| `transform: scale()` | `width`, `height` | 改尺寸会触发重排,卡顿 |
| `transform: translate()` | `top`, `left`, `margin` | 改位置会触发重排 |
| `opacity` | `visibility` 切换时渐变 | visibility 要么全显示要么全隐藏,不能渐变 |

**确实需要改尺寸时用 FLIP 技巧**:
1. First: 记录元素当前位置和大小
2. Last: 直接切到最终状态,记录新位置和大小
3. Invert: 用 transform 把元素变回起始位置和大小
4. Play: 让 transform 回到 0,看起来就是流畅地从起始变到最终

## 层次感

**一组元素依次出现**,间隔 30–60ms,不要一起蹦出来。

```css
/* ❌ 错误:菜单项一起出现 */
.menu-item { animation: fadeIn 200ms; }

/* ✅ 正确:依次淡入 */
.menu-item:nth-child(1) { animation: fadeIn 200ms 0ms; }
.menu-item:nth-child(2) { animation: fadeIn 200ms 40ms; }
.menu-item:nth-child(3) { animation: fadeIn 200ms 80ms; }
```

**或者用 JS 循环**:
```js
items.forEach((item, i) => {
  item.style.animationDelay = `${i * 40}ms`;
});
```

## 有来源、可逆

- **有来源**: 元素从哪里来、回哪里去要讲得通。按钮变成菜单,就是按钮本身在变形,不是凭空弹出新的一层。
- **可逆**: 打开怎么来,关闭就怎么回去,路径一致。

```js
// ✅ 正确:菜单从按钮位置展开,关闭时缩回按钮
fab.onclick = () => {
  menu.style.transformOrigin = 'bottom right';
  menu.animate([
    { transform: 'scale(0)', opacity: 0 },
    { transform: 'scale(1)', opacity: 1 }
  ], { duration: 350, easing: 'ease-out' });
};

close.onclick = () => {
  menu.animate([
    { transform: 'scale(1)', opacity: 1 },
    { transform: 'scale(0)', opacity: 0 }
  ], { duration: 250, easing: 'ease-in' }); // 退场稍快
};
```

## 反馈

可点击的元素要有 **按下态** 和 **hover 态**:

- **按下态**: 缩放到 0.96–0.98,按下去 60ms,松开回弹 150ms。
- **Hover 态**: 轻微放大到 1.02 或加阴影,200ms 过渡。

```css
.btn {
  transition: transform 200ms ease-out;
}
.btn:hover {
  transform: scale(1.02);
}
.btn:active {
  transform: scale(0.97);
  transition-duration: 60ms;
}
```

触屏上用 JS 加点击反馈:
```js
btn.addEventListener('touchstart', () => {
  btn.style.transform = 'scale(0.97)';
});
btn.addEventListener('touchend', () => {
  btn.style.transform = 'scale(1)';
});
```

## 无障碍

尊重系统设置,`prefers-reduced-motion: reduce` 时,动画改成 150ms 以内的淡入淡出:

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.15s !important;
    transition-duration: 0.15s !important;
  }
}
```

或者直接关掉动画:
```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation: none !important;
    transition: none !important;
  }
}
```

## 页面切换与空间关系

切换不是"换一张图"，要让用户感觉页面在一个连续的空间里移动。

- **方向一致**: 点右边的 Tab，新内容从右边轻推进来；往左滑标签页，内容往左走。方向和操作反了，用户会迷路。
- **背后有反应**: 弹窗、抽屉出现时，背后页面缩小（约 0.92）、变圆角、压暗，像退后一步；关闭时同步恢复。背后一动不动，弹窗就像贴上去的。
- **指示器要流动**: 胶囊、下划线切换时，前沿先到、后沿慢半拍跟上（两个快慢不同的弹簧分别驱动左右边缘），途中自然拉长再收回，不要整块平移，更不要瞬移。
- **进度联动**: 可以跟手的切换（滑动标签、拖弹窗、滚动折叠标题），所有相关元素都按同一个进度值实时插值（位置、缩放、颜色、透明度），不要等到达后才突变。
- **不停在半路**: 折叠标题、弹窗、分页这类有明确"两个状态"的界面，松手后按速度和位置吸附到最近的状态。

```js
// 两个弹簧驱动指示器左右边缘，做出"水滴/毛毛虫"的拉伸感
const lead = spring({ stiffness: 420, damping: 26 });   // 前沿：快
const tail = spring({ stiffness: 190, damping: 20 });   // 后沿：慢半拍
function moveTo(i) {
  const goingRight = i > current;
  (goingRight ? rightEdge : leftEdge).animateWith(lead);
  setTimeout(() => (goingRight ? leftEdge : rightEdge).animateWith(tail), 60);
}
```

## 参数集中管理

把动效参数写成 CSS 变量或 JS 常量,方便用户微调:

```css
:root {
  --dur-small: 200ms;
  --dur-panel: 400ms;
  --dur-page: 500ms;
  --ease-out: cubic-bezier(0, 0, 0.2, 1);
  --ease-in: cubic-bezier(0.4, 0, 1, 1);
  --spring: cubic-bezier(0.34, 1.56, 0.64, 1);
}

.menu { animation: fadeIn var(--dur-panel) var(--ease-out); }
```

或者 JS:
```js
const DUR = { small: 200, panel: 400, page: 500 };
const EASE = {
  out: 'cubic-bezier(0, 0, 0.2, 1)',
  in: 'cubic-bezier(0.4, 0, 1, 1)',
  spring: 'cubic-bezier(0.34, 1.56, 0.64, 1)'
};

menu.animate([...], {
  duration: DUR.panel,
  easing: EASE.out
});
```
