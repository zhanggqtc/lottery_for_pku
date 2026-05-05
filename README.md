# 🎁 Seat Lottery · 座位抽奖工具

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GitHub Pages](https://img.shields.io/badge/Live-Demo-success)](https://zhanggqtc.github.io/lottery_for_pku/)

> 凭票根「X 排 X 座」的现场抽奖工具 · 控制端 + 大屏端**双屏方案** · 纯静态零依赖 · 一键 GitHub Pages 部署

适用于线下活动现场（演唱会、校园行、社团聚会、新品发布会等），观众用座位票根参与抽奖。

---

## ✨ 特性

- 📱 **双屏分离**：控制端在笔记本/手机配置，大屏端独立投影展示
- 🎯 **灵活排除**：可视化座位图点选 / 批量文本（如 `1-5`、`2-3~2-10`、`3排` 整排）
- 🎬 **电影级动画**：超大数字滚动 → 排号锁定 → 座号锁定 → 撒花 + 烟花 + 号角音
- 🏆 **奖品自定义**：奖品名/副标题/图片均可在控制端实时配置
- 🔁 **防重复**：已中奖座位自动加入排除池
- 💾 **持久化**：localStorage 保存配置和历史，刷新不丢
- 📥 **CSV 导出**：一键导出中奖名单（带 BOM，Excel 直接打开）
- 🚀 **零依赖**：纯 HTML + CSS + JS，3 个文件，无需构建

---

## 📺 在线体验（GitHub Pages）

部署后访问：

| 页面 | 地址 | 用途 |
|---|---|---|
| 入口 | `https://zhanggqtc.github.io/lottery_for_pku/` | 导航页 |
| **控制端** | `https://zhanggqtc.github.io/lottery_for_pku/admin.html` | 配置/历史 |
| **大屏端** | `https://zhanggqtc.github.io/lottery_for_pku/screen.html` | 投影展示 |

---

## 🚀 快速开始

### 方式 1：直接 fork + 启用 GitHub Pages（推荐）

1. 点击右上角 **Fork** 到你的账号
2. 进入 fork 后的仓库 → **Settings → Pages**
3. **Source** 选 `Deploy from a branch`，**Branch** 选 `main` / `/(root)` → 保存
4. 等 1 分钟，访问 `https://<你的用户名>.github.io/seat-lottery/`

### 方式 2：本地预览

```bash
git clone https://github.com/<你>/seat-lottery.git
cd seat-lottery
# 任选一个静态服务器
python3 -m http.server 8000
# 或者
npx serve .
```

打开 http://localhost:8000

> ⚠️ **不要直接 `file://` 双击打开**：浏览器会把每个 file 路径当成不同 origin，控制端和大屏端无法通过 localStorage 通信。必须通过 HTTP 服务器访问，才能保证两个标签页 origin 相同。

---

## 🎬 现场使用流程

1. **打开控制端**（笔记本浏览器）→ `/admin.html`
2. 控制端右上角点【🖥️ 打开大屏端】→ 在新标签页打开后**拖到投影屏**，按 `F` 全屏
3. 在控制端确认右上角显示 **"大屏已连接"** 绿点 ✅
4. 配置：
   - 排数 / 每排座数 / 起始排号 / 起始座号
   - 批量排除已占座位（支持 `1-5`、`2-3~2-10`、`3` 整排、中文 `1排5-10座`）
   - 上传奖品图片、修改奖品名称
5. 主持人在大屏前点【**开始抽奖**】或按 `空格` / `Enter` 启动
6. 看动画 → 念中奖座位 → 再按 `空格` 抽下一位

### ⌨️ 大屏端快捷键

| 键 | 功能 |
|---|---|
| `Space` / `Enter` | 启动抽奖；中奖展示中可跳过庆祝返回等待页 |
| `F` / `F11` | 全屏 / 退出全屏 |
| `H` | 显示/隐藏使用帮助 |
| `C` | 隐藏/显示鼠标指针 |
| `T` | 测试模式（用 `?` 占位演示一次抽奖） |
| `ESC` | 关闭帮助弹层 |

---

## 🛠️ 技术原理

```
┌─────────────────┐                    ┌─────────────────┐
│  admin.html     │                    │  screen.html    │
│  (控制端)        │                    │  (大屏端)        │
│  - 座位配置      │                    │  - 抽奖按钮      │
│  - 排除座位      │                    │  - 数字滚动      │
│  - 奖品上传      │                    │  - 中奖展示      │
│  - 历史/导出     │                    │  - 撒花烟花      │
└────────┬────────┘                    └────────┬────────┘
         │                                      │
         │     ┌──────────────────────────┐     │
         └────►│  localStorage (同 origin) │◄────┘
               │  ┌──────────────────────┐│
               │  │ seat-lottery-state-v1││ 共享状态
               │  │ seat-lottery-cmd-v1  ││ 命令通道
               │  │ seat-lottery-screen- ││ 心跳
               │  │ hb-v1                ││
               │  └──────────────────────┘│
               └──────────────────────────┘
                   ↕ storage 事件 + 兜底轮询
```

- 两个页面通过浏览器同源 `localStorage` 实时共享
- 大屏端每 2 秒发心跳，控制端右上角显示连接状态
- 大屏端是抽奖唯一执行方，控制端只发 `reset` 命令同步状态

---

## 📂 文件结构

```
seat-lottery/
├── index.html       # 导航入口（GitHub Pages 默认页）
├── admin.html       # 控制端
├── screen.html      # 大屏端
├── README.md
├── LICENSE
└── .gitignore
```

---

## 🎨 自定义

### 修改默认奖品名称

打开 `admin.html` 和 `screen.html`，搜索 `'一等奖'` 替换为你想要的默认值。或者直接在控制端的「奖品设置」面板里修改（实时生效，浏览器记住）。

### 修改主题色

两个文件顶部 `:root` 里：

```css
--red:  #ff4655;  /* 主红 */
--gold: #ffd700;  /* 高亮金 */
--cyan: #00d4ff;  /* 副蓝 */
```

### 替换大屏顶部品牌标签

打开 `screen.html` 搜索 `brandLeft` / `brandRight`，修改文本即可。

### 替换默认奖牌图

奖牌默认是一个 SVG 金色奖杯。建议直接在控制端「奖品设置」点击上传你的图片（PNG/JPG/SVG 均可，最大 5MB），无需改代码。

---

## ⚠️ 注意事项

- **必须 HTTP/HTTPS 访问**（不能 `file://`），否则 localStorage 不互通
- **同一 origin 才能联动**：例如都用 `https://xxx.github.io/seat-lottery/` 访问；不要混用不同域名/IP
- 移动端 Safari 在某些场景下 localStorage 跨标签页同步会延迟，建议用桌面浏览器（Chrome / Edge）
- 上传的奖品图存在浏览器 localStorage（最大约 5MB），不会上传到任何服务器

---

## 📝 License

MIT © [zhanggqtc](https://github.com/zhanggqtc)

---

## 🙋 反馈

欢迎提 [Issue](https://github.com/zhanggqtc/lottery_for_pku/issues) 或 PR。
