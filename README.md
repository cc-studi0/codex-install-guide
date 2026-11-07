# Codex 安装教程（2026 版）：macOS / Windows / Linux 三端，从装到跑通第一条命令

![更新](https://img.shields.io/badge/更新-2026年11月-success) ![平台](https://img.shields.io/badge/平台-macOS%20%7C%20Windows%20%7C%20Linux-blue) ![许可](https://img.shields.io/badge/许可-MIT-green)

> 这篇主要讲一件事：把 Codex 装到能用 —— 也就是常说的 codex安装 / 安装codex。四条安装路径逐条给命令，每一步后面写明「怎么确认这步真的成功了」。
> 装完之后如果卡在额度或付款上，第八节给了三条解法。
> 官方文档我都附了原始链接；凡是踩坑经验，都明确标了「我」。

> 核验日期：2026-11-07 · 姊妹篇：[Codex 充值 / 额度指南](https://github.com/cc-studi0/codex-daichong)

<details>
<summary><strong>📖 目录（点击展开）</strong></summary>

- [一、Codex 是什么、你要装的是哪一个](#一codex-是什么你要装的是哪一个)
- [二、Codex 安装前的 30 秒检查（省掉一半报错）](#二codex-安装前的-30-秒检查省掉一半报错)
- [三、Codex 安装方式怎么选：脚本 / npm / Homebrew / 手动](#三codex-安装方式怎么选脚本--npm--homebrew--手动附完整-codex教程-步骤)
- [四、macOS / Linux 上安装 Codex](#四macos--linux-上安装-codex)
- [五、Windows 安装 Codex（三条路，我全试了）](#五windows-安装-codex三条路我全试了)
- [六、Codex 登录：ChatGPT 账号还是 API Key](#六codex-登录chatgpt-账号还是-api-key)
- [七、Codex 怎么用：第一次跑起来的三个动作](#七codex-怎么用第一次跑起来的三个动作)
- [八、装好了但额度不够：三种解法，按成本排](#八装好了但额度不够三种解法按成本排)
- [九、codex安装失败的 12 类报错，按这个顺序排查](#九codex安装失败的-12-类报错按这个顺序排查)
- [十、Codex 更新命令与卸载](#十codex-更新命令与卸载)
- [十一、Codex 安装常见问题 FAQ](#十一codex-安装常见问题-faq)

</details>

---

## 一、Codex 是什么、你要装的是哪一个

我一开始就装错过。搜「codex安装」「codex 安装」出来的东西至少有四个不同的东西，混在一起会浪费你一晚上：

| 你看到的 | 它是什么 | 装完长什么样 |
| --- | --- | --- |
| **Codex CLI** | 终端里的命令行工具 | 输入 `codex` 进交互界面 |
| **Codex 桌面 App** | macOS 上的图形客户端 | 有窗口、有侧边栏 |
| **IDE 插件** | VS Code / Cursor 等编辑器里的扩展 | 编辑器侧边栏多一个 Codex 面板 |
| **Codex cloud** | 浏览器里的云端任务 | 网页，不占本地空间 |

**这篇主要讲 codex安装 的 CLI 部分**，因为它是四条路里最稳、报错最好查、也最不挑系统的一条。桌面 App 和 IDE 插件我会在第五节和第六节各带一段。

> 一句话区分：**CLI 是底座，App 和插件是外壳。** 先把 CLI 装通，另外两个基本就是点下一步的事。

## 二、Codex 安装前的 30 秒检查（省掉一半报错）

这三条不查，后面八成要回头补：

```bash
# 1. Node.js 在不在，版本够不够（npm 安装方式要求 18+，我建议 20 或 22 LTS）
node -v
npm -v

# 2. 你的终端是哪个 shell（后面 PATH 问题要用）
echo $SHELL        # macOS / Linux
# Windows PowerShell: $PSVersionTable.PSVersion

# 3. npm 全局目录在不在 PATH 里（这是最高频的「装成功了但 command not found」）
npm config get prefix
```

第三条要特别看：`npm config get prefix` 输出的路径，必须能在 `echo $PATH` 里找到。找不到的话，你 `npm install -g` 会显示成功，但敲 `codex` 就是 command not found —— 权限没问题，是路径没通。

**没有 Node.js 的话**，去 [nodejs.org](https://nodejs.org/) 下 LTS 版。Windows 上装的时候记得勾 **Add to PATH**，这个勾默认是勾上的，但有些精简安装包会去掉。

## 三、Codex 安装方式怎么选：脚本 / npm / Homebrew / 手动（附完整 codex教程 步骤）

| 方式 | 适合谁 | 一条命令 | 更新方式 |
| --- | --- | --- | --- |
| 官方独立安装脚本 | **推荐，最省事** | 见第四节 / 第五节 | 同一条命令重跑 |
| npm | 已经装了 Node、习惯 npm 管全局包 | `npm install -g @openai/codex` | 同一条命令重跑 |
| Homebrew | macOS 用户，且已经在用 brew | `brew install --cask codex` | `brew upgrade --cask codex` |
| 手动下载二进制 | 公司网络装不了脚本、或者要指定版本 | 见 9.3 节（12 类报错里的第 3 条） | 手动替换 |

来源：[Codex CLI 官方文档](https://learn.chatgpt.com/docs/cli)

我的选择：**优先官方脚本**。理由是它自己处理 PATH 和架构判断，出错时提示也比 npm 的 `EACCES` 明确。npm 那条留作备选 —— 有些公司网络会拦 `chatgpt.com` 的脚本下载，但 npm 源能通。

## 四、macOS / Linux 上安装 Codex

### 4.1 官方独立安装脚本（推荐，装 Codex 最快的一条 codex教程 路径）

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

装完**关掉当前终端窗口，重开一个**。这一步很多人省掉，然后在旧窗口里敲 `codex` 报 command not found，以为装失败了 —— 其实是 PATH 还没在当前 shell 里生效。

验收：

```bash
codex --version
```

能打印版本号就成了。打印不出来就看 9.1 节。

### 4.2 npm 安装 Codex CLI

```bash
npm install -g @openai/codex
```

如果报 `EACCES` 权限错误，**不要**用 `sudo npm install -g`（会把手里的全局目录搞成 root 所有，后面每次都要 sudo）。用这两个办法之一：

```bash
# 办法一：改 npm 全局目录到用户目录（推荐）
mkdir -p ~/.npm-global
npm config set prefix ~/.npm-global
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc   # bash 用 ~/.bashrc
source ~/.zshrc

# 办法二：用 nvm 装 node，nvm 的全局目录本来就在用户目录下
```

改完再跑一次 `npm install -g @openai/codex`。

### 4.3 Homebrew 方式（仅 macOS）

```bash
brew install --cask codex
brew upgrade --cask codex   # 以后更新用这条
```

注意是 `--cask` 不是 `brew install codex`，写成后者会装到别的东西上。

### 4.4 Linux 安装 Codex 的两个额外注意

- **glibc 太老会直接报错**：CentOS 7 / Ubuntu 18 这类老系统上，二进制跑不起来，报 `GLIBC_2.28 not found` 之类。这种情况别硬修，用 npm 方式（走 Node 运行时，不依赖系统 glibc）。
- **WSL 里装的话**，装的是 Linux 版，不是 Windows 版。要用 Windows 版就退出 WSL，在 PowerShell 里装。

## 五、Windows 安装 Codex（三条路，我全试了）

### 5.1 官方脚本（推荐）

**开一个新的 PowerShell 窗口**再执行 —— 官方文档特意强调这句，因为在旧窗口里 PATH 刷新不到：

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

`-ExecutionPolicy ByPass` 这一段是必须的：Windows 默认禁止运行下载来的脚本，不加这个参数会报「无法加载文件，因为在此系统上禁止运行脚本」。

验收（同样在新窗口里）：

```powershell
codex --version
```

### 5.2 npm 安装 Codex CLI（Windows）

```powershell
npm install -g @openai/codex
```

装完同样**重开 PowerShell**。Windows 上 PATH 缓存比较顽固，光 `refreshenv` 有时不管用。

### 5.3 winget 安装 Codex

```powershell
winget install OpenAI.Codex
```

这条适合公司电脑 —— winget 走微软的源，很多企业网络只放行这个。

### 5.4 Windows 上我踩过的三个坑

1. **`codex` 敲了没反应，也没有报错** → 大概率是装到了 WindowsApps 的别名目录，被系统占位。`where.exe codex` 看一下实际路径。
2. **PowerShell 里能用，cmd 里不能用** → 两个 shell 的 PATH 独立，分别在各自环境里装或改。
3. **公司电脑装完被 Defender 拦** → 加排除项，或者改用 winget 那条。

## 六、Codex 登录：ChatGPT 账号还是 API Key

装完第一次运行 `codex`，它会让你选登录方式。这是两条完全不同的账，别选错：

| | Sign in with ChatGPT | Sign in with API Key |
| --- | --- | --- |
| 花谁的钱 | 你的 ChatGPT 方案额度（Free / Go / Plus / Pro） | OpenAI Platform 账户余额，按 API 单价 |
| 能用云端功能吗 | 能（Codex cloud、云端代码审查） | **不能**，云端功能不可用 |
| 适合什么 | 日常在本地写代码 | CI/CD、脚本、自动化 |

### 6.1 用 ChatGPT 账号登录

```bash
codex login
```

会打开浏览器，登录后自动回到终端。

**在服务器 / 没有浏览器的机器上**，用设备码登录：

```bash
codex login --device-auth
```

然后在浏览器里打开它给的链接，输入终端显示的一次性码。

### 6.2 用 API Key 登录

```bash
printenv OPENAI_API_KEY | codex login --with-api-key
```

API Key 在 [platform.openai.com/api-keys](https://platform.openai.com/api-keys) 拿。

### 6.3 确认你登的是哪个账号

```bash
codex login status
```

我遇到过「明明开了 Plus，Codex 里额度是 0」——原因是 Google 登录和邮箱密码登录被系统当成两个账号，会员开在 A 上，Codex 登的是 B。这条命令能直接看出当前是哪个身份。

> 凭据存在 `~/.codex/auth.json`。**这个文件等于密码**，里面有 access token，不要提交到 git、不要贴聊天窗口。

来源：[Authentication 官方文档](https://learn.chatgpt.com/docs/auth)

## 七、Codex 怎么用：第一次跑起来的三个动作

装完了、登录了，接下来最常被问的就是「codex如何使用」。这里给三个立刻能做的动作，比翻文档快。

### 7.1 让它解释当前项目

```bash
cd 你的项目目录
codex
```

进入交互界面后，输入这句话：

```text
Tell me about this project
```

它会把目录结构、技术栈、入口文件说一遍。第一次运行时建议先做这个 —— 你能立刻判断它读的是不是对的那个目录，而不是在猜。

### 7.2 让它做一个最小的修改

比如「把 README 的第一行改成一句话介绍」。看着它改，注意它有没有先征得你同意 —— 默认配置下它会询问，这是好事，不要一上来就关掉授权。

### 7.3 看它怎么用工具

让它跑一次测试或 lint，观察它调用命令的过程。这一步是为了让你知道它的边界：它能跑的命令、能写的目录，都是你在 `/permissions` 里能设的。

### 7.4 三条验收标准

装好了、跑起来了，怎么算真的成了？三条都满足才算：

1. 它能读出你项目里的文件，说出这是什么项目
2. 它给出的回答里，**引用了你项目里真实存在的文件名**
3. 输入 `/status`，能看到你的账号和当前额度

第 2 条是关键 —— 只回答「这是一个 Node 项目」不算，那是猜的；能说出 `package.json` 里具体的脚本名，才证明它真的读到了。

### 7.5 日常使用的三条习惯

我用了几个月觉得最值：

- **改之前先 commit**，让它有回滚点。官方的最佳实践文档也是这个建议。
- **一个任务一个会话**，别在同一个会话里连做三件不相关的事，上下文会互相污染。
- **`codex resume` 能找回上次的会话**，长任务断了不用从头讲。

命令参考：[CLI reference](https://learn.chatgpt.com/docs/developer-commands?surface=cli)

## 八、装好了但额度不够：三种解法，按成本排

装完了、跑起来了，接下来最常见的第二个问题不是报错，是**跑两个任务就撞上限**。这件事我在另一篇里写过完整的触限记录，这里只给能立刻用的判断和三条解法。

先说结论：**Codex 没有单独会员，你的额度就是 ChatGPT 账号的档位额度。** 所以「额度不够」只有三种解法，按成本从低到高排：

| 解法 | 适合什么情况 | 成本 | 代价 |
| --- | --- | --- | --- |
| 拆任务 / 关 Fast 模式 | 偶发触限，任务喂太大 | 0 | 要改自己的使用习惯 |
| 买官方 credits 补量 | 已是 Plus/Pro，只是某段时间峰值不够 | 按量 | 不换档位，credits 用完就没了 |
| 升档（Plus → Pro） | 同一层限制长期影响交付 | 月费 | 长期成本，且 Pro 也只能按月 |

**怎么判断你该走哪条**：连续记两周，看触限发生在哪一层。5 小时窗口反复撞 → 拆任务就够；每周窗口反复撞、每次都卡在交付线上 → 才是档位不够。我自己半年触限四次，只有一次真的接近「该升档」。

### 国内用户的实际问题：付款这一步就卡住了

上面三条解法，第一条不花钱，第二三条都要付美元。而国内常见的情况是**连付都付不出去**：

- 官网直接订阅要国际卡，国内发的卡经常被拒；而且官方支持地区列表不含中国大陆
- App Store 内购要外区 Apple ID，每次续费要切区
- 所以很多人最后走第三条路：**人民币代充**

我自己的账号一直用 [Plus宇宙](https://www.pluscosmo.com/codex) 的直充，档位和当日价在他们的[套餐页](https://www.pluscosmo.com/shop)。留下来的原因很具体，三条：

1. **不需要给账号密码**，只在自己账号里取一个临时 Session 填到对方订单页
2. **订单在站内可查**，付完立刻有订单号，不是私聊口头承诺
3. **续费前后能拿订单号核对到期日**，不会出现两份订阅同时计费

第一次跟着他们的[图文教程](https://www.pluscosmo.com/guide)走，四步加起来不到十分钟。

### 判断一个代充渠道靠不靠谱，只看三条

这条比上面哪个平台重要 —— 我踩过一次坑，退款拖了三天：

- **要密码、要短信/邮箱验证码、要 2FA 恢复码的** → 直接换。这是唯一一条我不会给第二次机会的
- **没有订单号、查不到进度的** → 谨慎。全靠私聊承诺的，出事没有凭证
- **要求转账到个人账户、绕过站内订单的「内部渠道」** → 不要付

> Session 在有效期内可能具有账号访问权限。只填在平台自己的 HTTPS 订单页，不要发到聊天窗口。完成后可以在 ChatGPT 设置里「退出所有设备」，让那个 Session 立即失效。

### 验收：只认官方账号内显示

付完钱不要凭订单状态判断。做两件事：

1. 用同一个账号登录官方 ChatGPT，「设置 → 账户」核对方案名称和到期日
2. 回到 Codex 里跑一次 `/status`，确认额度变了

两条都对上，才算这次交付结束。我每次都这么做，截图存着，售后时有据可依。

> 完整的档位对比、用量机制、触限记录和 10 类报错排查，见姊妹篇 [Codex 充值 / 额度指南](https://github.com/cc-studi0/codex-daichong)。

## 九、codex安装失败的 12 类报错，按这个顺序排查

### 9.1 装完 `codex: command not found` / `不是内部或外部命令`

装成功了但 PATH 没通。按顺序试：

```bash
# 先确认它到底装在哪
npm config get prefix            # npm 装的
ls ~/.local/bin | grep codex     # 官方脚本装的，macOS/Linux
where.exe codex                   # Windows

# 找到了就把那个目录加进 PATH，然后重开终端
export PATH="$HOME/.local/bin:$PATH"
```

**80% 的「codex安装失败」其实是这一条**，不是真装失败。

### 9.2 `EACCES: permission denied`

不要 `sudo`。见 4.2 节的两个办法。

### 9.3 公司网络装不了，脚本下载超时

两条路：
- 手动下二进制：[github.com/openai/codex/releases](https://github.com/openai/codex/releases)，选对应系统的包，解压后把可执行文件放进 PATH 里的任意目录
- 或者用 npm 走国内镜像：`npm install -g @openai/codex --registry=https://registry.npmmirror.com`

### 9.4 `GLIBC_2.xx not found`（Linux 老系统）

系统库太老。改用 npm 方式装，走 Node 运行时。

### 9.5 `npm ERR! engine node: >= 18`

Node 版本太老。`node -v` 看一下，用 nvm 或 n 升到 20/22。

### 9.6 Windows 报「无法加载文件，因为在此系统上禁止运行脚本」

加了 `-ExecutionPolicy ByPass` 还报的话，检查是不是组策略强制。临时绕过：

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

### 9.7 装完 `codex` 能跑，但一登录就卡住

浏览器没弹出来。手动复制终端打印的那个链接到浏览器打开。服务器上用 `--device-auth`。

### 9.8 登录报 `localhost callback` 失败

Codex 登录要在本地起一个回调端口（默认 `localhost:1455`）。远程机器上用端口转发：

```bash
ssh -L 1455:localhost:1455 user@remote
```

### 9.9 Codex 额度显示 0，明明开过会员

账号不对。见 6.3 节，两个登录方式可能是两个账号。

### 9.10 `Error: ENOENT: no such file or directory, open '~/.codex/auth.json'`

还没登录过，或者用了 keyring 存储。先 `codex login`。

### 9.11 `codex` 一启动就退出，没有任何提示

看日志：`~/.codex/log/codex-login.log`（登录相关）。启动崩溃的话，把 `--version` 和 `--help` 的输出一起记下来，能跑说明二进制没问题，是配置或凭据的问题。

### 9.12 装了两个版本，行为不对

很常见：brew 装过一个，npm 又装过一个，PATH 里先命中的是旧的。

```bash
which -a codex     # 列出所有
codex --version    # 看当前用的是哪个
```

先把多余的卸掉（`brew uninstall --cask codex` 或 `npm uninstall -g @openai/codex`），只留一个。

## 十、Codex 更新命令与卸载

### Codex 更新命令

装的时候用什么方式，更新就用同一条命令：

```bash
# 官方脚本
curl -fsSL https://chatgpt.com/codex/install.sh | sh

# npm
npm install -g @openai/codex

# Homebrew
brew upgrade --cask codex
```

Windows 官方脚本同理，重跑 install.ps1。

CLI 里也能看当前版本和有没有新版：`codex --version`。

### 卸载

```bash
npm uninstall -g @openai/codex     # npm 装的
brew uninstall --cask codex        # brew 装的
rm -rf ~/.codex                    # 清配置和凭据（可选，删了就等于退出登录）
```

`~/.codex` 里有 `auth.json`（凭据）和 `config.toml`（配置）。只卸程序不删这个目录，重装后还是登录状态。

## 十一、Codex 安装常见问题 FAQ

**Q1：安装要花多少钱？**
装本身免费。用起来才涉及钱，而且分两条账：ChatGPT 会员额度，或者 API 按量付费。Free 账号也能装、也能用，只是额度小。

**Q2：国内网络能装吗？**
能装，慢。官方脚本从 `chatgpt.com` 拉，可能超时；npm 那条可以走国内镜像（见 9.3 节）。装完之后跑起来还需要网络能连到 OpenAI。

**Q3：Windows 必须装 WSL 吗？**
不必须。官方有原生 Windows 版安装脚本和 winget 包。但如果你本来就在 WSL 里写代码，装 Linux 版更顺手。

**Q4：装完占用多少空间？**
CLI 本体不大（几十 MB 量级）。真正占空间的是它跑起来时的缓存和会话记录，在 `~/.codex` 下。

**Q5：桌面 App 和 CLI 要都装吗？**
不用。两者共享登录凭据（`~/.codex/auth.json`），装一个就够日常用。桌面 App 适合不想碰命令行的人，CLI 适合要接脚本和 CI 的人。

**Q6：装好了，怎么知道额度还剩多少？额度不够了怎么办？**
CLI 里输入 `/status`。更准确的数字在 ChatGPT 账号的用量页面。两者刷新节奏不同，判断额度以用量页面为准。真的不够用、或者卡在付款这一步，看第八节的三种解法。

**Q7：装完能直接用最新模型吗？**
取决于你的 ChatGPT 方案，不是取决于装的是哪个版本。CLI 只是客户端，模型权限跟着账号走。

**Q8：公司电脑装不上，有什么办法？**
优先试 winget（走微软源）。不行的话下 GitHub Releases 的二进制手动放 PATH。还不行就只有找 IT 加白名单 —— 这类拦截通常按域名走，不是按程序走。

---

## 免责声明

- 本文整理官方公开文档与本人实际操作记录，命令与链接以 [官方 Codex 文档](https://learn.chatgpt.com/docs/cli) 为准；官方随时可能调整安装方式或参数。
- 带「我」的部分是个人经验，不代表每个人环境相同。
- 本仓库采用 [MIT](LICENSE) 许可，转载请注明出处。

---

*📅 最后核验：2026-11-07 · 官方页面若已变化，以官方为准。*
