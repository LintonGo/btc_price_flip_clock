# BTC 机械翻页时钟

一个极简且酷炫的实时比特币（BTC）价格显示器，采用经典的机械翻页钟风格设计。

它通过 **WebSocket 实时订阅**币安（Binance）的行情数据，并具备智能节流机制，既能保证价格更新的实时性，又能提供流畅的视觉体验。

## ✨ 特性

- 🕰️ **复古机械风格**: 使用 FlipClock.js 库实现逼真的机械翻页动画效果
- 📡 **WebSocket 实时推送**: 订阅 Binance 公开 WebSocket，实时获取 BTC/USD 价格
- ⚡ **智能节流**: 可配置的更新间隔，避免过于频繁的翻页影响观看体验
- 🔄 **双重翻页模式**:
  - **顺滑翻页**: 小间隔更新时使用原生 FlipClock 翻页，逐个数字卡片翻转
  - **整体刷新**: 大间隔或初始化时使用完全重建，整体动画效果
- 🔁 **自动重连**: 连接断开后自动重连，确保持续获取数据
- 📱 **智能缩放**: 内置自适应算法，完美适配各种屏幕尺寸（手机竖屏、宽屏、横屏）
- 🎬 **流畅动画**: 价格更新时包含翻转与位移动画
- 📊 **连接状态**: 底部显示实时连接状态（连接中/实时更新中/重新连接中）
- 🎨 **暗黑极简风**: 深色径向渐变背景，适合作为桌面挂件或屏幕保护程序

## 🛠️ 技术栈

- **HTML5**: 语义化结构
- **CSS3**: Flexbox 布局、关键帧动画及媒体查询（横竖屏适配）
- **JavaScript (ES6+)**: 核心逻辑控制、WebSocket 连接、节流算法
- **jQuery**: DOM 操作
- **FlipClock.js**: 核心翻页动画组件（CDN 引入）

## 📸 效果预览

![BTC机械翻页时钟运行效果截图](./demo.png)

## 🚀 快速开始

无需安装任何后端环境或依赖包，直接在浏览器中打开即可使用。

1. **获取代码**
   将 `btc_price_flip_clock.html` 文件保存到本地

2. **运行**
   直接双击文件，使用 Chrome、Edge、Firefox 或 Safari 等现代浏览器打开

3. **查看**
   页面加载后会自动：
   - 通过 HTTP 请求获取初始价格并显示
   - 建立 WebSocket 连接订阅实时行情
   - 根据配置的间隔更新价格显示

## ⚙️ 自定义配置

可以在 `<script>` 标签内修改以下配置参数：

### 1. 修改更新间隔

找到 `UPDATE_INTERVAL` 变量：

```javascript
var UPDATE_INTERVAL = 5000; // 5秒更新一次
```

建议值：
- `5000` (5秒) - 启用顺滑翻页，逐个数字卡片翻转
- `60000` (60秒) - 启用整体刷新，完全重建时钟

### 2. 修改交易对

找到 WebSocket 端点和 HTTP API URL：

```javascript
// WebSocket 端点（推荐使用免翻墙端点）
ws = new WebSocket('wss://stream.binance.me:9443/ws/btcusdt@ticker');
// 备用端点：wss://stream.binance.com:443/ws/btcusdt@ticker
// 合约端点：wss://fstream.binance.com/ws/btcusdt@ticker

// HTTP 初始请求
url: 'https://api.binance.me/api/v3/ticker/price?symbol=BTCUSDT'
```

例如修改为 ETH：
- `btcusdt` → `ethusdt`
- `BTCUSDT` → `ETHUSDT`

## 📝 核心实现逻辑

### WebSocket 实时数据流

```
Binance WebSocket → 接收 ticker 数据 → 节流检查 → 更新时钟
     ↑
     └─ 自动重连机制（断线5秒后重连）
```

### 智能翻页模式

程序会根据 `UPDATE_INTERVAL` 自动选择翻页方式：

| 更新间隔 | 翻页模式 | 实现方式 |
|---------|---------|---------|
| < 30秒 | 顺滑翻页 | `clock.setValue(price)` - 逐个数字翻动 |
| ≥ 30秒 | 整体刷新 | 销毁重建 + CSS 动画 |

### 节流机制

```javascript
function throttledRenderClock(priceValue) {
    var now = Date.now();
    var targetPrice = Math.floor(priceValue);

    // 只有价格变化 且 距离上次更新超过间隔时间 才更新
    if (targetPrice !== lastRenderedPrice &&
        (now - lastRenderTime >= UPDATE_INTERVAL)) {
        // 执行更新...
    }
}
```

### 双层容器结构

- **`scale-wrapper`**: 负责整体的 `transform: scale()` 缩放，确保时钟在任何分辨率下都不溢出屏幕
- **`anim-wrapper`**: 负责内部的翻转与位移动画

### 自动缩放算法

1. 动态计算当前窗口的宽高
2. 基于时钟原始尺寸与目标窗口尺寸的比例
3. 取宽高比例的最小值作为缩放因子 (`Math.min`)
4. 保证内容完整显示且不溢出

### 横竖屏适配

通过 CSS `@media (orientation: landscape)` 查询，横屏模式下自动隐藏标题和页脚，提供沉浸式的大字显示体验。

## ⚠️ 注意事项

- 本项目直接调用 Binance 的公共 WebSocket 和 API。如果网络环境无法访问，可能无法获取数据
- 推荐使用 `stream.binance.me` 端点，该端点无需翻墙即可访问
- 依赖的 jQuery 和 FlipClock 库均通过 CDN 加载，请确保设备已连接互联网
- WebSocket 连接使用的是 Binance 公开端点，无需认证

## 📄 License

MIT License
