# Family Service Index Panel (家庭服务索引面板)

这是一个基于 **Node.js**、**MySQL** 和 **Nginx** 构建的本地化家庭服务管理系统。它提供了一个现代化的 Web 界面，用于集中管理、监控和访问家庭局域网内的各种服务（如 NAS、路由器后台、智能家居控制台等）。

系统采用 **Windows 批处理脚本** (`.bat`) 实现了“一键启动”和“一键停止”，非常适合在 Windows 服务器或主力 PC 上运行。

---

## ✨ 功能特性

* **服务聚合**: 集中管理所有局域网服务的 URL。
* **多地址支持**: 每个服务可关联多个访问地址（如内网 IP、域名、备用 IP）。
* **状态标记**: 自动或手动记录服务地址的在线状态（需二次开发探针，目前预留字段）。
* **数据持久化**: 使用 MySQL 数据库存储数据。
* **CSV 导入/导出**: 支持批量导出配置备份，或通过 CSV 批量导入服务。
* **反向代理**: 集成 Nginx，作为静态资源服务器及 API 反向代理，解决跨域问题。

---

## 🛠️ 技术栈

* **前端**: HTML5, CSS3 (Dark Mode),原生 JavaScript (无框架依赖)
* **后端**: Node.js + Express
* **数据库**: MySQL
* **服务器**: Nginx (Windows版)
* **部署**: Windows Batch Scripts

---

## 📋 环境准备

在运行之前，请确保您的电脑已安装：

1. **Node.js**: (建议 v14.0 以上)
2. **MySQL**: (建议 v5.7 或 v8.0)

---

## 🚀 快速开始

### 1. 数据库初始化

请在 MySQL 中创建一个名为 `family_services` 的数据库，并执行以下 SQL 语句创建表：

```sql
CREATE DATABASE IF NOT EXISTS family_services;
USE family_services;

-- 创建服务主表
CREATE TABLE IF NOT EXISTS services (
  id INT AUTO_INCREMENT PRIMARY KEY,
  type VARCHAR(50) NOT NULL,
  description VARCHAR(255)
);

-- 创建地址关联表
CREATE TABLE IF NOT EXISTS addresses (
  id INT AUTO_INCREMENT PRIMARY KEY,
  service_id INT NOT NULL,
  name VARCHAR(100),
  url VARCHAR(255) NOT NULL,
  status VARCHAR(20) DEFAULT 'unknown',
  FOREIGN KEY (service_id) REFERENCES services(id) ON DELETE CASCADE
);

```

### 2. 配置文件

打开根目录下的 `config.json`，根据您的实际环境修改数据库连接信息：

```json
"database": {
  "host": "localhost",
  "port": 3306,
  "user": "root",        <-- 修改为您的数据库用户名
  "password": "12345",   <-- 修改为您的数据库密码
  "database": "family_services"
}

```

### 3. 安装依赖

在项目根目录下打开命令行（CMD 或 PowerShell），运行：

```bash
npm install

```

*(这将安装 express, mysql2, cors 等依赖)*

### 4. 启动系统

双击运行目录下的 **`run.bat`**。

* 脚本会自动启动 Nginx (端口 8080)。
* 脚本会自动启动 Node.js 后端 (端口 3000)。

### 5. 访问

打开浏览器访问： `http://localhost:8080`

---

## 📁 项目结构

```
nginx-1.29.4/
├── conf/
│   ├── nginx.conf          # Nginx 配置
├── html/
│   ├── index.html          # 前端界面
│   ├── 50x.html            # 错误页面
│   └── family-service-backend/
│       ├── server.js       # Node.js 后端服务
│       ├── config.json     # 配置文件
│       ├── package.json    # Node.js 依赖
│       └── README.md       # 后端说明文档
├── logs/                   # 日志文件目录
├── temp/                   # 临时文件目录
├── run.bat                 # 一键启动脚本
├── stop.bat                # 一键停止脚本
├── nginx.exe               # Nginx 可执行文件
└── README.md               # 本说明文档
```

## ⚙️ 常见操作

### 停止服务

双击运行 **`stop.bat`**。

* 它会优雅地停止 Nginx 进程。
* 它会查找占用 3000 端口的 Node 进程并强制结束，防止端口占用。

### 修改端口

如果 8080 端口被占用：

1. 修改 `nginx.conf` 中的 `listen 8080`。
2. 修改 `config.json` 中的 proxy 配置。

### 备份数据

在网页界面点击右上角的 **"导出 CSV"** 按钮，即可下载当前所有服务配置的备份文件。

---

## ⚠️ 注意事项

1. **路径问题**: `run.bat` 脚本中假设了特定的目录结构（如 `html\family-service-backend`）。如果您调整了文件位置，请务必用文本编辑器打开 `.bat` 文件并修正 `BACKEND_DIR` 的路径。
2. **编码**: 批处理脚本已包含 `chcp 65001` 以支持 UTF-8 中文输出。
3. **安全性**: 当前配置允许所有 IP 访问 (`server_name _;`)，仅建议在受信任的家庭局域网环境中使用。

---

## 🤝 贡献与支持

如果有任何问题，请检查 `logs/` 目录下的日志文件，或在控制台查看报错信息。
