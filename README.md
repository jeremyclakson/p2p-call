# 📹 P2P 通话 · GitHub Pages 静态站

基于 **WebRTC** 的点对点音视频通话网站，纯静态、**无需后端**，可直接部署到 GitHub Pages。

- ✅ 音视频在双方之间**直连**（P2P），不经服务器存储
- ✅ 任意网络下可用的房间码 + 分享链接
- ✅ 静音 / 关摄像头 / 屏幕共享 / 小窗预览
- ✅ 通话中文字聊天（WebRTC DataChannel）
- ✅ 手机 / 桌面浏览器自适应

## 🔧 工作原理

| 环节 | 用到什么 | 说明 |
|------|---------|------|
| 信令（交换连接信息） | PeerJS 公共云 `0.peerjs.com` | 免费，仅用于建立连接，不中转媒体 |
| 打洞（NAT 穿透） | Google / Twilio / Cloudflare 公共 STUN | 绝大多数家庭、办公网络可直连 |
| 媒体传输 | WebRTC DTLS/SRTP | 端到端加密，点对点直传 |

> 房间码会作为 PeerJS 的 peer id（加了 `p2pcall-` 前缀避免冲突）。
> 两人在同一房间码上即可互相发现并直连。

## 🚀 部署到 GitHub Pages

### 方式一：网页操作（最简单）

1. 在 GitHub 新建一个仓库，例如 `p2p-call`（公开仓库）。
2. 上传 `index.html`（本仓库唯一的必需文件）。
3. 进入仓库 **Settings → Pages**。
4. 在 **Source** 选择 `Deploy from a branch`，分支选 `main`，目录选 `/ (root)`，保存。
5. 等一两分钟，访问 `https://<你的用户名>.github.io/p2p-call/` 即可。

### 方式二：命令行

```bash
git init
git add index.html README.md
git commit -m "feat: P2P calling site"
git branch -M main
git remote add origin https://github.com/<你的用户名>/p2p-call.git
git push -u origin main
```

然后按方式一的第 3–5 步开启 Pages。

> 仓库里已含 `.github/workflows/deploy.yml`。如果你更想用 GitHub Actions 部署，
> 在 Settings → Pages 的 Source 改成 `GitHub Actions` 即可，它会自动发布。

## 📖 使用方法

1. 打开网站，点 **🎥 创建房间**，会生成一个形如 `ABCD-1234` 的房间码。
2. 把房间码或「复制链接」发给对方。
3. 对方打开链接（或在网站输入房间码点 **加入房间**）。
4. 浏览器首次会询问摄像头/麦克风权限，**允许**后即开始通话。

> 用分享链接打开会自动加入对应房间。

## 🔒 隐私说明

- 音视频**不经过任何服务器存储**，双方直连、端到端加密。
- 房间码是随机生成的，只要不泄露，外人无法加入。
- 若需更高安全性，可自行部署信令与 TURN（见下）。

## ⚠️ 已知限制

- **严格企业网络 / 部分运营商 NAT** 下可能无法直连（对称 NAT）。
  此时需要自建 **TURN 中继**：在 `index.html` 里 `iceServers` 数组中加入你的 TURN 地址和凭据。
- PeerJS 公共信令偶尔不稳定。生产环境建议自建 [PeerJS Server](https://github.com/peers/peerjs-server)，
  把 `new Peer(id, {...})` 改成 `new Peer(id, { host:"你的域名", port:443, path:"/", secure:true, config:{...} })`。
- 必须 **HTTPS** 或 `localhost` 才能调用摄像头（GitHub Pages 天然满足）。
- 同一浏览器标签里同一时刻只能有一个身份，测试时请用两台设备或一个普通窗口 + 一个无痕窗口。

## 📁 文件

```
index.html                      # 全部功能都在这一个文件里
README.md
LICENSE
.github/workflows/deploy.yml    # 可选的 GitHub Actions 自动部署
```

## 📄 许可

MIT — 见 [LICENSE](LICENSE)
