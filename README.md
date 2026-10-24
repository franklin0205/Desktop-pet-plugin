# dsh-pet-zoo

基于 [dsh-pet](https://github.com/PC2005-cloud/dsh-pet)（MIT）的独立桌宠插件。
内置一只**原创小狐狸**，并支持自由扩充更多动物形象。

## 特性

- 内置原创狐狸：**29 个透明动画**（VP9-alpha，约 2.5 MB）
- 多宠物同屏，右键「显示位置」可分别开关（仅浏览器 / 仅桌面 / 两者 / 都不显示）
- 支持 **pet pack**：往 `pet/` 放配置和素材即可新增动物，**无需改代码**
- 待机、点击、拖拽、移动、余额、碎碎念、工作状态 全套联动
- 默认 70% 待机、偶尔小动作，安静不吵

## 安装

| 方式 | 命令 |
|---|---|
| 桌面应用 | 插件管理页的安装框填 `dsh-pet-zoo` |
| `dsh web` | `dsh plugin --profile web add dsh-pet-zoo` |
| 本地 tarball | `dsh plugin --profile web add .\dsh-pet-zoo-0.2.2.tgz` |

> 桌面端 profile 由 Electron 应用独占管理，只能在应用内「插件管理」页安装。
> 装完 **重开应用**（`dsh web` 则重启）生效。

## 加一只新动物（pet pack）

在 `~/.dsh/dsh-pet-zoo/pet/` 下建两项：

1. `<种类>-config.jsonc` —— 宠物实例 + 动画映射
2. `<种类>-animation/` —— 放 `<动画名>.webm`，文件名要与配置里引用的名字**逐一对应**

最小示例 `mycat-config.jsonc`：

```jsonc
{
  "pets": [
    { "id": "mycat", "name": "我的猫", "size": 400, "display": "web",
       "position": { "corner": "bottom-right", "marginX": 24, "marginY": 0 } }
  ],
  "animations": {
    "idle": ["待机"],
    "turn": ["待机"],
    "drag": ["待机"],
    "clicks": ["待机"],
    "moves": { "default": {}, "actions": [{ "name": "待机" }] },
    "categories": [{ "id": "小动作", "weight": 20, "actions": ["待机"] }],
    "events": { "balance": ["待机"], "whisper": ["待机"],
                 "workStatus": ["待机","待机","待机","待机","待机","待机"] }
  },
  "animationWeights": { "idle": 80, "turn": 5, "move": 5 }
}
```

配套放 `mycat-animation/待机.webm`。

改完 **刷新页面**（桌面端右键宠物 →「重载配置」）即可。

### 素材要求

- **必须是带 alpha 通道的 VP9 `.webm`**，否则会渲染成黑底
- 建议 640×360（16:9），透明背景，2~4 秒可循环
- 只要 `<种类>-animation/` 目录存在，该宠物的素材就**只从这里取**，不会回退到内置素材；
  配置里引用的每个名字都必须有对应文件，否则那个动作会 404
- `events.workStatus` 是**按下标对应 6 个档位**（思考/工作/结果/等待/成功/错误），必须给满 6 项

## macOS

Safari / WKWebView 不认 webm 的 alpha（会黑底），需要另放 `.mov`（HEVC-alpha）到
`pet/<种类>-animation/mov/`。Chrome / Edge / Firefox 与桌面模式照常用 webm。

## 开发者：维护与发布

### 本机已做的配置（换电脑要重做）

- SSH 密钥：`~/.ssh/id_ed25519_dshpet`（公钥已加到 GitHub 账号）
- 本仓库已设置：

  ```powershell
  git config core.sshCommand "ssh -i C:/Users/<你>/.ssh/id_ed25519_dshpet -o IdentitiesOnly=yes -o StrictHostKeyChecking=accept-new"
  git remote set-url origin ssh://git@ssh.github.com:443/franklin0205/Desktop-pet-plugin.git
  ```

- **为什么走 443**：本机 22 端口被代理拦截（`Connection closed by 198.18.0.19`），
  GitHub 提供 `ssh.github.com:443` 作为替代。主机指纹应为
  `SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU`（GitHub 官方 ED25519 指纹）。

### 日常提交

```powershell
git add -A
git commit -m "说明"
git push
```

### 发布到 npm（让别人能 `dsh plugin add 包名` 一键装）

```powershell
npm login              # 只需一次
npm publish --access public
```

包名若被占用，改用 scoped 包：把 `package.json` 的 `name` 改成 `@franklin0205/dsh-pet-zoo`，
再 `npm publish --access public`。

> 本仓库已移除 `prepack` 构建链（`lib/` 是预构建产物），可以直接 publish，无需装 devDependencies。

### 修改包名 / 作者

脚手架脚本 `make_meta.py`（不在本仓库内）里的 `AUTHOR` 与包名改好后重跑，会同步更新
`package.json` 与 README 里的链接。

### 提交前务必确认

> ⚠️ **不要**把影视 / 动漫等他人版权角色转成的动画提交进仓库。本仓库内置的是原创形象；
> 个人私用的角色素材放在本机 `~/.dsh/<包名>/pet/`，不会进版本库。

## 授权

- 本包是 [dsh-pet](https://github.com/PC2005-cloud/dsh-pet) 的衍生物，原始版权归 **PC2005-cloud**（MIT），
  `LICENSE` 原样保留，修改与新增部分归本项目维护者。
- 内置的狐狸形象为本项目**原创**。
- 请勿把影视 / 动漫等他人版权角色转成的动画打包分发。

## License

MIT
