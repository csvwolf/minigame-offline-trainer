# 小游戏客户端逆向与离线化技能

`minigame-offline-trainer` 是一套供 AI 编程助手读取的工作流指令，用于平台小游戏的客户端研究、离线适配与本地修改器开发。它强调先建立证据，再还原启动链、定位业务接口、实施补丁，并通过实际操作和存档重启验证结果。

仓库包含技能正文与参考文档，不包含游戏包、游戏资源、用户存档、签名密钥或现成的通用修改器。安装技能后，仍需由助手结合具体游戏、可用工具和研究材料开展工作；不能保证任意游戏都可完整离线。

## 在线课件与口播稿

- [打开网页课件](https://csvwolf.github.io/minigame-offline-trainer/)：面向新手介绍安装、环境准备、快速使用、踩坑建议，并在后半部分解释实现原理与验证方法。
- [逐页口播稿](https://csvwolf.github.io/minigame-offline-trainer/narration.md) · [纯口播稿](https://csvwolf.github.io/minigame-offline-trainer/narration.txt) · [课件操作说明](https://csvwolf.github.io/minigame-offline-trainer/usage.txt)

课件单独存放在 `gh-pages` 发布分支。**安装技能请使用默认的 `main` 分支**；技能目录不附带课件、讲稿或网站文件。通过 Git 安装且希望只获取技能分支时，可在下方 `git clone` 命令加入 `--single-branch --branch main`。

## 适用场景

- 采集小游戏的客户端代码与资源，识别 Unity WebGL / IL2CPP、Cocos 等引擎线索。
- 分析宿主启动、模块加载、平台 API、远程资源与本地存档之间的依赖。
- 区分客户端可处理的玩法与服务器独占的状态、规则和验证。
- 在隔离副本中搭建离线兼容层，定位资源、生命、倍速、时间和关卡等修改入口。
- 按任务需要交付 H5、PWA 或 Android WebView APK，并验证移动端兼容、持久化与覆盖升级。

已有经验主要来自 Unity WebGL 与 Cocos 客户端适配；Laya 等方向需要针对样本重新调查。技能没有跨游戏通用的解包、解密或服务器还原保证。

## 工作原则与默认行为

1. **保留原版体验**：沿用原首页、美术、引导、地图、战斗、结算和返回流程，将必要补丁放在宿主、网络、奖励与存档边界。
2. **先验证离线基线**：确认资源加载、核心循环与保存重启，再逐项加入修改器。
3. **复用原业务接口**：优先调用原 getter/setter、事件与保存接口，避免只改显示或用万能成功回调掩盖缺口。
4. **修改能力默认可选**：不在普通新存档中自动赠送资源、跳教程或推进进度；有关卡的交付默认提供可选的解锁与快速跳关入口。仅采集、分析或明确限定范围时，按任务范围执行。
5. **交付要求明确**：默认去广告并接通已确认的本地奖励；应用入口使用经来源核实的原游戏官方图标。功能和图标的实际实现、验证范围分别记录。
6. **保护真实进度与隐私**：研究使用隔离存档，修改前备份；凭据、抓包、账号、设备信息和签名材料留在私有项目中。

## 安装

下面以 Codex 的本地技能目录为例。此仓库根目录就是一个完整技能目录，安装后应能在 `minigame-offline-trainer/SKILL.md` 找到入口，并保留整个 `references/` 文件夹。

### 方法一：安装到当前用户目录

macOS / Linux：

```bash
mkdir -p "$HOME/.agents/skills"
git clone --single-branch --branch main https://github.com/csvwolf/minigame-offline-trainer.git \
  "$HOME/.agents/skills/minigame-offline-trainer"
```

Windows PowerShell：

```powershell
New-Item -ItemType Directory -Force "$HOME/.agents/skills" | Out-Null
git clone --single-branch --branch main https://github.com/csvwolf/minigame-offline-trainer.git "$HOME/.agents/skills/minigame-offline-trainer"
```

需要预先安装 Git。如果目标目录已有同名技能，先核对其来源与本地修改，不要直接覆盖或重复安装。

### 方法二：仅安装到某个项目

在目标项目的根目录运行：

```bash
git clone --single-branch --branch main https://github.com/csvwolf/minigame-offline-trainer.git \
  .agents/skills/minigame-offline-trainer
```

该方法会在项目内创建一个独立的 Git 仓库。如需把技能文件纳入项目自身的版本管理，可以改为下载本仓库 ZIP，解压后将包含 `SKILL.md` 和 `references/` 的目录放到上述位置。

### 方法三：手动复制

从 GitHub 的 **Code → Download ZIP** 下载并解压，将文件夹命名为 `minigame-offline-trainer`，放入用户级或项目级技能目录。不要只复制 `SKILL.md`，它引用的参考文件也需要一起安装。

Codex 的本地发现目录包括用户级 `~/.agents/skills/` 和项目级 `.agents/skills/`，支持自动发现技能变化；若安装后未显示，可重启 Codex。CLI / IDE 扩展可使用 `/skills` 查找技能，或输入 `$` 选择技能。目录和调用方式以 [OpenAI 官方技能文档](https://learn.chatgpt.com/docs/build-skills)为准。其他支持 Agent Skills 的助手，请按其自身文档配置目录与调用入口。

### 更新

通过 Git 克隆安装且没有本地改动时，可以在技能目录运行：

```bash
git pull --ff-only
```

如果修改过署名或技能内容，先保存本地改动并处理与上游的差异；不要用强制重置覆盖个性化配置。ZIP 安装需要手动下载新版本，并在替换前备份本地修改。

## 使用方法

在有权研究的游戏项目或独立研究目录中打开助手，显式指定技能，并说明游戏来源、当前材料、设备环境和本次范围。以下示例中的游戏名称和路径需要自行替换。

### 先做可行性分析

```text
$minigame-offline-trainer
分析这款小游戏是否能够离线运行。材料在项目的 samples/ 目录。
本次只做采集与分析，不修改游戏、不安装 APK、不改真实存档。
请说明引擎证据、最短启动链、服务器依赖、缺失资源及下一步验证方法。
```

### 制作离线版与本地修改器

```text
$minigame-offline-trainer
基于项目内已采集的客户端材料制作个人学习用的离线版和本地修改器。
保留原版页面、引导、地图和结算，用隔离的新存档测试。
请先确认离线基线，再按实际存在的功能接入资源、倍速和关卡操作。
本次交付目标为 Android APK；安装到设备前再按我的设备安排执行。
分别报告已实现、Web/模拟器/真机已验证及未验证的部分。
```

### 排查已有适配问题

```text
$minigame-offline-trainer
现有离线版在领取在线奖励后，累计时长会重置；修改器长面板也无法滚到底。
请结合原客户端回调、存档与 WebView 实际布局定位问题，保留其他已有功能。
使用测试存档验证领奖、重启、原生触摸滚动及返回游戏。
```

提供材料时，优先说明：游戏与客户端版本、合法取得的样本位置、已有源码或适配工程、目标平台、可用测试设备，以及允许修改和必须保留的内容。不要把账号密码、Token、私钥或真实用户存档贴到公开 Issue。

实际研究可能需要 ADB / Android SDK、浏览器调试工具、引擎分析工具或 APK 构建工具链；这些工具不随技能提供，也不需要为了纯分析一次性安装全部工具。账号登录由使用者完成，设备与服务器访问权限需要另行具备。

## 更改署名和作者链接

仓库保留的默认交付署名为 **修改器与离线适配：敖天羽**，默认作者入口为：

- [博客](https://www.codesky.me/)
- [微博](https://weibo.com/dreamit)
- [B 站](https://space.bilibili.com/334908)

这些是原维护者的公开作者信息。其他使用者制作自己的适配项目时，可以按实际贡献者修改交付署名与链接，不应让默认信息造成作者身份误认。

### 只覆盖当前任务

调用技能时明确给出署名与链接，例如：

```text
本次项目的交付署名改为“修改器与离线适配：我的名字”。
作者链接仅保留我提供的个人主页，不使用技能中的默认博客、微博和 B 站链接。
请同步更新修改器关于页、README、使用说明及交付包中的作者信息，
并保留原游戏作者归属和免费声明。
```

请用真实姓名或昵称、实际主页替换示例内容；没有主页时可以明确要求不显示作者链接。技能已说明后续用户明确指定其他信息时按其要求修改。

### 长期修改本地副本或 Fork

同时检查以下位置，避免只改了一处：

| 文件 | 需要修改的内容 |
|---|---|
| [SKILL.md](SKILL.md) 的「修改器、作者声明与归档」 | 对默认署名和作者入口的描述 |
| [references/trainer-and-delivery.md](references/trainer-and-delivery.md) 的「署名与免费声明」 | 默认署名、博客、微博、B 站链接及相关表述 |
| 本 README 的「更改署名和作者链接」 | 如果要分发自己的 Fork，同步说明自己的默认设置与项目来源 |

修改安装目录中的实际文件后，检查是否还存在重复安装的同名技能。长期维护个性化版本时，可使用自己的 Fork 管理变更，并在合并上游更新时核对署名是否被覆盖。

**交付作品的作者署名与本技能的来源说明应分别表述。** 修改默认交付署名时，请如实说明贡献，不冒充原游戏作者，也不要把原技能工作流的全部内容描述为自己原创。免费声明仍按技能要求保留：**完全免费 · 仅供学习 · 禁止售卖**。仅替换姓名和链接不会改变该声明，也不会赋予游戏素材的再分发权。

## 文档导航

| 文档 | 内容 |
|---|---|
| [技能入口](SKILL.md) | 整体流程、默认行为和按需阅读入口 |
| [采集与启动还原](references/acquisition-and-runtime.md) | 设备、缓存、资源识别、模块加载和平台边界 |
| [代码与存档分析](references/code-and-state.md) | JS / WASM / IL2CPP 分析、状态交叉验证 |
| [原版流程与完整验收](references/original-flow-and-verification.md) | 首启、mock、累计奖励、真实操作链与存档验收 |
| [修改器与交付](references/trainer-and-delivery.md) | 控件、离线奖励、倍速、跳关、图标、署名和打包 |
| [移动 WebView 与滚动](references/mobile-webview.md) | 旧内核兜底、长面板、触摸隔离和移动端验收 |
| [客户端实现经验](references/client-techniques.md) | Unity 客户端案例中的经验与适用限制 |

## 使用边界

请在有权访问和研究的范围内使用。研究与补丁在隔离的本地副本中进行，不伪造或向线上平台提交广告、支付、账号及服务器验证凭证。缺失资源、服务器独占规则和未验证平台需要如实说明。

游戏原作、图标和素材归各自权利人所有。离线适配、作者声明或免费声明不等于获得上游内容的公开再分发许可。仓库目前未附独立的 `LICENSE` 文件；技能文档的使用许可与交付作品中的免费声明是两个不同问题。
