# 移动 WebView 与修改器滚动

用于把游戏包进 WebView，或实现 DOM 浮层、长表单、背包列表时。不能只在开发机新浏览器验证，也不能把现代 CSS 的失败归为手机物理分辨率低。

## 环境判定

- 分开记录 Android API、实际 WebView 提供方及完整版本、物理像素、系统逻辑密度、网页 innerWidth/innerHeight、devicePixelRatio 和可见视口。相同机型/Android/分辨率并不保证相同浏览器能力。
- 在授权设备可用时用 ADB 查询 wm size、wm density、dumpsys webviewupdate。普通用户可先提供型号和 Android 版本，能找到 WebView 详情时再补版本。不要因此要求 Root、清数据或改系统分辨率。
- 正确区分实际设备、AOSP 模拟器、浏览器视口模拟与静态代码分析。模拟同 Android 和分辨率并未复刻 MIUI 等厂商系统或用户的显示设置。

## 高度限制与兼容写法

基础规则使用目标最低内核支持的尺寸单位；动态视口作为能力增强。不要仅写 100dvh。下面只示意布局结构，尺寸按实际产品调整：

```css
.panel {
  display: flex;
  flex-direction: column;
  max-height: calc(100vh - 24px);
  overflow: hidden;
}
.panel-header, .panel-footer { flex-shrink: 0; }
.panel-body {
  flex: 1 1 auto;
  min-height: 0;
  min-width: 0;
  overflow-y: auto;
  touch-action: pan-y;
}
@supports (height: 100dvh) {
  .panel { max-height: calc(100dvh - 24px); }
}
```

- 实证：Android 12 / WebView 91 的一次面板中，最后一条 `max-height: calc(100dvh - max(...env(...)) - ...)` 在计算阶段失效，结果为 none；前置 vh 声明仍未兜住。用 @supports 把现代单位声明隔离后，才正确限制高度。因此不能只看 CSS.supports 对基础 vh 返回 true 或桌面删掉 dvh 的模拟通过，要查看 getComputedStyle 的最终 maxHeight 与元素边界。
- CSS var()/env() 可能推迟值的校验，失效不一定会回退到上一条声明。未知或较新单位、函数放进明确的特性分支，基础路径必须独立有效。
- 只有正文滚动，顶部返回/关闭常驻；正文必须允许收缩。外层无限增高时，正文即使 overflow:auto 也不会产生滚动范围。
- 若确实需要 JS 处理键盘/可见视口，用单一模型协调 visualViewport 与容器坐标；不要把 dvh、键盘高度和底部安全区重复扣减。无 visualViewport 时有基础布局。

## 触摸与游戏隔离

只在需要拖动的手柄/悬浮入口使用 touch-action:none；滚动正文保留 pan-y。检查游戏是否在 document/window 的捕获阶段 preventDefault，或有透明层遮住正文；不能为阻止点击穿透而全页面禁用默认触摸。

鼠标滚轮滚动、JS 设置 scrollTop 和原生触摸滑动是不同证据。桌面自动化不能代替 Android 实际手势。浮层手势不应穿透触发游戏移动或战斗，拖动与点击要分开验证。

## 最小验收矩阵

1. 默认展开状态与全部分类展开，长内容下确认 scrollHeight 大于 clientHeight，面板上下边界在视口内。
2. 用实际触摸上滑到最后的操作，再回到顶部；关闭、保存等需要点击的控件实际可达。不要只确认滚动条存在或截图前调用 scrollIntoView。
3. 一组当前内核、一组实际不支持新能力的旧内核。模拟不支持可辅助定位，至少一次真实旧内核验收应覆盖交付目标。
4. 目标物理分辨率至少检查一种正常显示密度和一种放大显示；补小视口、窗口缩小和横竖屏支持范围。只测试很矮的屏幕不能代替目标设备配置。
5. 需要输入时测试软键盘打开/关闭、页面已滚动、输入框和提交/关闭可达。没有实际测软键盘时不能把窗口高度变化测试称为键盘验收。
6. 验证游戏未受触摸穿透影响，折叠记忆、返回关闭与存档仍正常。

回归脚本使用项目的真实面板源码，断言可见边界、实际滚动、操作可达和事件隔离。把明确的旧内核失败场景保留下来，不能只测试新内核，也不能只匹配 CSS 字符串。

## 其他客户端能力与发布

小游戏已能运行不等于每个 SDK 分支兼容：广告回调中使用 Array.at、某个按钮才触发的新 API 等，可能直到用户点击才报错。对关键路径使用目标最低内核可用的写法或经过验证的窄范围兼容实现；不要大规模修改原游戏或伪造平台结果。

诊断构建可以在隔离测试设备开启 WebView 调试并读取布局，正式包关闭调试。发布前核对最终 APK 的源码、包名、递增版本号和签名；覆盖升级保留存档。记录实际验收环境及未测的厂商系统，不能承诺所有手机绝不再发生。
