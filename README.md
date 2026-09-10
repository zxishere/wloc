# wloc (personal mirror)

Apple 网络定位（WLOC）坐标修改模块的个人镜像副本，用于自己钉版本，避免上游消失或被改动。

## 来源

- 原作者 / 原项目：**Yu9191** — `github.com/Yu9191/wloc`（作者账号已于 2026-09 前后删除，原仓库现为 404）
- 本副本的文件取自镜像仓库 `ifflagged/Romeo`（`Modules/Surge/Yu9191/`、`Modules/Loon/Yu9191/`、`Modules/JavaScript/Yu9191/wloc/`）
- 相较上游只做了一处改动：`script-path` 指向本仓库自己的 `wloc.js` / `wloc-settings.js`，不再依赖第三方仓库

## 订阅地址

Surge / Shadowrocket / Stash：

```
https://raw.githubusercontent.com/zxishere/wloc/main/wloc.sgmodule
```

Loon：

```
https://raw.githubusercontent.com/zxishere/wloc/main/wloc.lpx
```

## 原理

MITM 解密 `gs-loc.apple.com` 等定位域名，在 `/clls/wloc` 的 protobuf 响应里把苹果算出的坐标替换成指定坐标。只影响**网络定位**（Wi-Fi / 基站），不影响 GPS 硬件定位。

坐标有三种写法，优先级：设备持久化存储（快捷指令 / 选点页写入）> 模块参数默认值。

- 模块参数：直接在模块里填经度 / 纬度 / 精度 / 扰动半径
- 快捷指令：<https://www.icloud.com/shortcuts/a82717d8fdad4e6280866fcf911173f7>（设置）、<https://www.icloud.com/shortcuts/f42632d406504f24a2cd163af4fe012f>（恢复）
- 选点页面：<https://wloc-pages.pages.dev/>

后两种是通过向 `gs-loc.apple.com/wloc-settings/save` 发请求写入的，该请求由 `wloc-settings.js` 在本地拦截，坐标不出设备。

## 脚本审计（2026-09-10）

对 `wloc.js`（41,180 B）与 `wloc-settings.js`（13,198 B）扫描结果：

- `$httpClient` / `$task.fetch` / `fetch()` / `XMLHttpRequest`：**0**
- `http://` 或 `https://` 字面量：**0**
- `$task` 仅出现在运行环境判断分支（用于识别 Quantumult X）
- 仅使用 `$persistentStore` 读写本地存储与 `$done` 返回

即脚本本身不具备联网能力。

## 注意

- 使用需要在代理工具中开启 HTTPS 解密并信任其根证书，请自行评估风险
- iOS 26+ 定位缓存较强，改完坐标通常需要重启设备才生效
