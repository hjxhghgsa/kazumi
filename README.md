# Kazumi —— 加速代理 + 源站规则集

本仓库把两个 Kazumi 相关项目合并在一起托管：

- **`proxy/`** —— Kazumi 本地加速代理（内存缓冲、零磁盘缓存、进度条任意拖动）
- **`rules/`** —— 可直接导入 Kazumi 的源站规则集（19 个 JSON）

两个子项目互相独立：`proxy` 只做本地流量中转，不含任何视频源；`rules` 只提供源站抓取配置。

---

## 一、项目要解决的问题

1. **播放体验差**：Kazumi 播放部分视频源时，源不支持 `Range` 导致进度条拖不动、反复白屏；播放器缓冲会写盘产生大缓存文件。
   → `proxy/` 提供一个纯本地、零缓存的代理层解决。
2. **源站配置麻烦**：Kazumi 本身不内置影视源，需用户自己配置源站规则才能搜番、播番。
   → `rules/` 把整理好的一批源站规则集中托管，方便一键导入 / 备份 / 分享。

---

## 二、目录结构

```
kazumi/
├── LICENSE
├── README.md
├── proxy/                # 本地加速代理（见下方「使用方法 A」）
│   ├── server.js         # 代理服务主程序
│   └── KazumiAccel-Proxy.bat  # Windows 一键启动
└── rules/                # 源站规则集（见下方「使用方法 B」）
    ├── index.json        # 规则索引
    ├── plugins.json      # 插件配置
    └── *.json            # 各源站抓取规则（共 19 个）
```

---

## 三、安装与使用方法

### 方法 A：本地加速代理（`proxy/`）

**环境要求**：已安装 [Node.js](https://nodejs.org/)（建议 18+），已安装 Kazumi 客户端。

```bash
git clone https://github.com/hjxhghgsa/kazumi.git
cd kazumi/proxy
node server.js
# 默认监听 http://127.0.0.1:8899
```

也可双击 `KazumiAccel-Proxy.bat`（脚本会自动切到自身目录后启动）。

**两种用法**：

- **重写模式（手动拼地址）**：把视频地址做 URL 编码后拼成
  `http://127.0.0.1:8899/p?u=<编码后的视频地址>`，在浏览器 / 下载工具 / 播放器打开即可走本代理（内存缓冲 + 任意 seek）。
- **全局代理模式（接进 Kazumi）**：先启动 `server.js`，再打开 Kazumi 设置 → 把「全局代理」填为 `http://127.0.0.1:8899`。

> 代理仅监听 `127.0.0.1`，不对外暴露；所有缓冲只在内存，超过 1.5GB 自动退化为透传以防 OOM。
> 想让 Kazumi 播放真正变快、零盘缓存、拖动流畅，关键是 MPV 配置：把 `mpv.conf` 放到 Kazumi 安装目录下的 `mpv-config\`（或 `%APPDATA%\mpv\mpv.conf`），或在 Kazumi 设置 → 播放器 → 自定义 MPV 参数里粘贴。

### 方法 B：导入源站规则（`rules/`）

**方式 A：直接放入规则目录**

把 `rules/` 下的 `.json` 文件复制到 Kazumi 规则目录，例如：

```
<你的用户目录>\AppData\Roaming\com.example\kazumi\plugins\v2\
```

重启 Kazumi 后规则自动加载。

**方式 B：从规则仓库导入**

打开 Kazumi → 我的 → 规则管理 → 从规则仓库导入，选择本仓库即可。

导入后在搜索框直接搜番名，即可看到来自这些源的聚合结果。某个源失效时，删除对应 `xxx.json` 即可禁用，不影响其它源。

---

## 四、许可证

- 本仓库代码与文档以 [MIT 许可证](./LICENSE) 开源。
- `rules/` 内的规则内容版权归各自源站 / 原作者所有，仅作聚合整理与备份；如规则失效，请到对应源站社区获取更新。
- `proxy/` 不内置任何视频源，视频源来自你自己的 Kazumi 配置。请遵守当地法律法规。
