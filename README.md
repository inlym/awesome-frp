# Awesome FRP

> 快速部署和配置 FRP 服务的一站式解决方案

[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![FRP Version](https://img.shields.io/badge/FRP-v0.64.0-green.svg)](https://github.com/fatedier/frp/releases)

## 📖 项目简介

本项目旨在为需要快速配置 FRP（Fast Reverse Proxy）服务的用户提供一个即用型解决方案。通过预配置的文件模板，您可以直接克隆项目到服务器上，进行少量修改后即可快速部署 FRP 服务，无需从零开始编写配置文件。

## 🏗️ 项目结构

```text
awesome-frp/
├── server/                 # 服务端配置
│   ├── frps               # FRP 服务端二进制文件
│   ├── frps.toml         # 服务端配置文件
│   └── frps.service      # systemd 服务文件
├── client/                 # 客户端配置
│   ├── frpc               # FRP 客户端二进制文件
│   ├── frpc.toml         # 客户端配置文件
│   └── frpc.service      # systemd 服务文件
├── LICENSE                 # 开源协议
└── README.md              # 项目说明文档
```

## 🚀 快速开始

### 服务端部署

1. **准备工作**

   ```bash
   # 在服务器上创建 /app 目录
   sudo mkdir -p /app
   cd /app
   ```

2. **克隆项目**

   ```bash
   git clone https://github.com/inlym/awesome-frp.git awesome-frp
   cd awesome-frp
   ```

3. **配置文件修改**

   ```bash
   # 编辑服务端配置文件
   vim server/frps.toml

   # 根据实际需求修改以下配置项：
   # - bind_port: 服务端监听端口
   # - dashboard_port: Web 管理界面端口
   # - dashboard_user: 管理界面用户名
   # - dashboard_pwd: 管理界面密码
   ```

4. **设置可执行权限**

   ```bash
   chmod +x server/frps
   ```

5. **创建系统服务**

   ```bash
   # 创建软链接到 systemd 目录
   sudo ln -s /app/awesome-frp/server/frps.service /etc/systemd/system/

   # 重载 systemd 配置
   sudo systemctl daemon-reload

   # 启动服务
   sudo systemctl start frps

   # 设置开机自启
   sudo systemctl enable frps
   ```

6. **验证服务状态**

   ```bash
   sudo systemctl status frps
   ```

### 客户端部署

1. **准备工作**

   ```bash
   # 在客户端服务器上创建 /app 目录
   sudo mkdir -p /app
   cd /app
   ```

2. **克隆项目**

   ```bash
   git clone https://github.com/inlym/awesome-frp.git
   cd awesome-frp
   ```

3. **配置文件修改**

   ```bash
   # 编辑客户端配置文件
   vim client/frpc.toml

   # 根据实际需求修改以下配置项：
   # - server_addr: FRP 服务端地址
   # - server_port: FRP 服务端端口
   ```

4. **添加端口映射配置**

   如需新增端口映射，请在 `client/confd/` 目录中参照 `ssh.toml` 文件格式创建新的配置文件：

   ```bash
   # 查看示例配置
   cat client/confd/ssh.toml

   # 创建新的端口映射配置（如：HTTP 服务）
   vim client/confd/http.toml
   ```

   **注意**：无需修改 `client/frpc.toml` 文件，所有端口映射配置都放在 `confd/` 目录中。

5. **设置可执行权限**

   ```bash
   chmod +x client/frpc
   ```

6. **创建系统服务**

   ```bash
   # 创建软链接到 systemd 目录
   sudo ln -s /app/awesome-frp/client/frpc.service /etc/systemd/system/

   # 重载 systemd 配置
   sudo systemctl daemon-reload

   # 启动服务
   sudo systemctl start frpc

   # 设置开机自启
   sudo systemctl enable frpc
   ```

7. **验证服务状态**

   ```bash
   sudo systemctl status frpc
   ```

8. **验证连接**

   ```bash
   # 使用 SSH 测试 FRP 代理是否正常工作
   # 请根据您的实际配置修改端口号、用户名和主机地址
   ssh -o Port=6000 username@host
   ```

   **说明**：
   - `6000` 为您在 frpc.toml 中配置的 SSH 代理端口
   - `username` 为目标服务器的用户名
   - `host` 为 FRP 服务端的 IP 地址或域名

## 📋 配置说明

### 服务端配置 (frps.toml)

主要配置项说明：

- `bind_port`: 服务端监听端口，默认 7000
- `dashboard_port`: Web 管理界面端口，默认 7500
- `dashboard_user`: 管理界面登录用户名
- `dashboard_pwd`: 管理界面登录密码
- `token`: 客户端连接认证令牌

### 客户端配置 (frpc.toml)

主要配置项说明：

- `server_addr`: FRP 服务端 IP 地址
- `server_port`: FRP 服务端端口
- `token`: 与服务端保持一致的认证令牌
- 各种代理配置（HTTP、HTTPS、TCP、UDP 等）

## 🔧 常用命令

### 服务管理

```bash
# 启动服务
sudo systemctl start frps    # 服务端
sudo systemctl start frpc    # 客户端

# 停止服务
sudo systemctl stop frps     # 服务端
sudo systemctl stop frpc     # 客户端

# 重启服务
sudo systemctl restart frps  # 服务端
sudo systemctl restart frpc  # 客户端

# 查看服务状态
sudo systemctl status frps   # 服务端
sudo systemctl status frpc   # 客户端

# 查看服务日志
sudo journalctl -u frps -f   # 服务端
sudo journalctl -u frpc -f   # 客户端
```

### 配置更新

```bash
# 修改配置文件后重启服务
sudo systemctl restart frps  # 服务端
sudo systemctl restart frpc  # 客户端
```

## 📦 二进制文件说明

项目中包含的 `frpc` 和 `frps` 二进制文件来源于官方 FRP 项目：

- **来源**: [https://github.com/fatedier/frp/releases](https://github.com/fatedier/frp/releases)
- **版本**: v0.64.0
- **平台**: Linux AMD64
- **下载包**: `frp_0.64.0_linux_amd64.tar.gz`

### 版本更新

如需更新 FRP 版本，请按以下步骤操作：

1. 访问 [FRP 官方发布页面](https://github.com/fatedier/frp/releases)
2. 下载最新版本的 `frp_x.x.x_linux_amd64.tar.gz`
3. 解压缩后，将 `frpc` 和 `frps` 文件分别替换到对应的 `client/` 和 `server/` 目录
4. 更新本 README 中的版本号信息

## 🔒 安全建议

1. **修改默认端口**: 避免使用默认的 7000 和 7500 端口
2. **设置强密码**: 为 dashboard 设置复杂密码
3. **使用 Token**: 确保服务端和客户端使用相同的安全令牌
4. **防火墙配置**: 只开放必要的端口
5. **定期更新**: 及时更新 FRP 到最新版本

## 📄 许可证

本项目采用 [MIT 许可证](LICENSE)。

## 🤝 贡献

欢迎提交 Issue 和 Pull Request 来帮助改进这个项目！

## 📞 联系方式

如有问题或建议，请通过以下方式联系：

- 提交 Issue
- 发送 Pull Request

---

⭐ 如果这个项目对您有帮助，请给个 Star 支持一下！
