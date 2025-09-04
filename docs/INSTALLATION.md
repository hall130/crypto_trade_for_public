# 📋 安装指南

本文档将指导您完成加密货币交易所自动跟单系统的完整安装过程。

## 📋 目录

- [🖥️ 系统要求](#️-系统要求)
- [📦 安装步骤](#-安装步骤)
- [🚀 启动系统](#-启动系统)
- [🔧 常见问题](#-常见问题)
- [📊 性能优化](#-性能优化)
- [🔒 安全配置](#-安全配置)
- [📝 部署检查清单](#-部署检查清单)
- [📞 获取帮助](#-获取帮助)
- [🔗 后续步骤](#-后续步骤)

---

## 🖥️ 系统要求

### 硬件要求
- **处理器**: 双核2.0GHz以上
- **内存**: 4GB RAM以上（推荐8GB）
- **存储**: 10GB可用空间
- **网络**: 稳定的互联网连接

### 软件要求
- **操作系统**: Windows 10+, Ubuntu 18.04+, macOS 10.15+
- **Python**: 3.8或更高版本
- **数据库**: MySQL 5.7+或MySQL 8.0+
- **浏览器**: Chrome 80+, Firefox 75+, Safari 13+

## 📦 安装步骤

### 1. 环境准备

#### Python环境
```bash
# 检查Python版本
python --version
# 应该显示Python 3.8.x或更高版本

# 如果需要安装Python
# Windows: 访问 https://python.org 下载安装包
# Ubuntu: sudo apt-get install python3.8 python3-pip
# macOS: brew install python@3.8
```

#### MySQL数据库
```bash
# Ubuntu安装
sudo apt-get update
sudo apt-get install mysql-server

# Windows: 下载MySQL Installer
# macOS: brew install mysql

# 启动MySQL服务
sudo systemctl start mysql  # Linux
# Windows: 通过服务管理器启动
```

### 2. 获取代码

```bash
# 联系开发者获取完整代码包
# 解压到目标目录
unzip crypto-trading-system.zip
cd crypto-trading-system
```

### 3. 安装依赖

```bash
# 安装Python依赖
pip install -r requirements.txt

# 或使用虚拟环境（推荐）
python -m venv venv
# Windows: venv\Scripts\activate
# Linux/macOS: source venv/bin/activate
pip install -r requirements.txt
```

### 4. 数据库配置

```sql
-- 1. 创建数据库
CREATE DATABASE trade_db CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

-- 2. 创建用户（可选）
CREATE USER 'trade_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON trade_db.* TO 'trade_user'@'localhost';
FLUSH PRIVILEGES;
```

```bash
# 3. 导入数据库结构
mysql -u root -p trade_db < trade_db.sql
mysql -u root -p trade_db < stop_loss_tables.sql
mysql -u root -p trade_db < limit_follow_tables.sql
```

### 5. 系统配置

```bash
# 复制配置文件模板
cp config_example.py config.py
```

编辑 `config.py` 文件：

```python
# 数据库配置
MYSQL_CONFIG = {
    'host': 'localhost',
    'user': 'trade_user',  # 或 'root'
    'password': 'your_mysql_password',
    'db': 'trade_db',
    'port': 3306,
    'charset': 'utf8mb4'
}

# OKX API配置
OKX_CONFIG = {
    'api_key': 'your_okx_api_key',
    'secret_key': 'your_okx_secret_key',
    'passphrase': 'your_okx_passphrase',
    'environment': 'sandbox'  # 测试环境，正式环境改为'live'
}

# 钉钉机器人配置（可选）
DINGTALK_CONFIG = {
    'enabled': True,
    'webhook_url': 'your_dingtalk_webhook',
    'secret': 'your_dingtalk_secret'
}
```

### 6. 获取API密钥

#### OKX API密钥申请
1. 登录 [OKX官网](https://www.okx.com)
2. 进入API管理页面
3. 创建新的API密钥
4. 设置IP白名单（重要）
5. 记录API Key, Secret Key, Passphrase

#### 权限设置
确保API密钥具有以下权限：
- ✅ 交易权限
- ✅ 查看权限
- ❌ 提现权限（建议关闭）

## 🚀 启动系统

### 测试启动

```bash
# 测试配置
python -c "import config; print('配置文件加载成功')"

# 测试数据库连接
python -c "from db import get_connection; print('数据库连接测试:', get_connection())"
```

### 启动服务

```bash
# 启动完整系统
python main.py

# 或分别启动各个模块
python main.py trade     # 仅交易模块
python main.py api       # 仅API服务
python main.py frontend  # 仅前端服务
```

### 验证安装

1. **访问Web界面**: 浏览器打开 `http://localhost:5000`
2. **检查日志**: 查看 `trades.log` 文件
3. **测试连接**: 在Web界面检查连接状态

## 🔧 常见问题

### 数据库连接失败
```bash
# 检查MySQL服务状态
sudo systemctl status mysql

# 重启MySQL服务
sudo systemctl restart mysql

# 检查端口占用
netstat -an | grep 3306
```

### Python依赖安装失败
```bash
# 升级pip
pip install --upgrade pip

# 使用国内镜像源
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple/

# 逐个安装依赖
pip install flask pymysql websocket-client requests
```

### API连接失败
1. 检查API密钥是否正确
2. 确认IP白名单设置
3. 验证网络连接
4. 检查防火墙设置

### 前端界面无法访问
```bash
# 检查端口占用
netstat -an | grep 5000

# 更改端口配置
# 在config.py中添加: WEB_PORT = 8080
```

## 📊 性能优化

### 数据库优化
```sql
-- 添加索引
ALTER TABLE trades ADD INDEX idx_created_at (created_at);
ALTER TABLE positions ADD INDEX idx_symbol_customer (symbol, customer_uid);

-- 配置MySQL参数
SET innodb_buffer_pool_size = 256M;
SET max_connections = 200;
```

### 系统优化
```bash
# 设置Python环境变量
export PYTHONPATH=$PYTHONPATH:/path/to/your/project

# 配置日志轮转
# 编辑 logger_config.py 调整日志级别和轮转策略
```

## 🔒 安全配置

### 防火墙设置
```bash
# Ubuntu UFW设置
sudo ufw allow 5000/tcp  # Web界面端口
sudo ufw allow 3306/tcp  # MySQL端口（仅限本地）
sudo ufw enable
```

### SSL证书（生产环境）
```bash
# 生成自签名证书（测试用）
openssl req -x509 -newkey rsa:4096 -nodes -out cert.pem -keyout key.pem -days 365

# 或使用Let's Encrypt（推荐）
sudo apt-get install certbot
sudo certbot certonly --standalone -d yourdomain.com
```

## 📝 部署检查清单

- [ ] Python 3.8+ 已安装
- [ ] MySQL服务正常运行
- [ ] 数据库已创建并导入表结构
- [ ] config.py 配置完成
- [ ] API密钥已配置并测试
- [ ] 依赖包安装完成
- [ ] 防火墙规则已设置
- [ ] 日志目录可写
- [ ] Web界面可正常访问
- [ ] 系统日志无错误

## 📞 获取帮助

如果在安装过程中遇到问题，请：

1. 查看系统日志：`tail -f trades.log`
2. 检查错误信息并搜索解决方案
3. 提交Issue: [GitHub Issues](https://github.com/hall130/crypto_trade_for_public/issues)
4. 发送邮件: saylas163@gmail.com

---

## 🔗 后续步骤

安装完成后，请继续阅读：

- [📖 用户手册](./USER_GUIDE.md) - 了解如何使用系统
- [🏗️ 系统架构](./ARCHITECTURE.md) - 了解技术架构  
- [🔌 API文档](./API_REFERENCE.md) - 查看接口文档
- [📱 前端文档](../frontend/README.md) - 前端开发指南

### 📚 相关文档

- [⬆️ 返回主文档](../README.md#-文档目录)
- [📝 查看更新日志](./CHANGELOG.md)
- [❓ 获取技术支持](../README.md#-技术支持) 