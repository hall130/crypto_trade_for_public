# 📋 Installation Guide

This document will guide you through the complete installation process of the cryptocurrency exchange auto copy trading system.

**Language**: [🇨🇳 中文](./INSTALLATION.md) | 🇺🇸 English

## 📋 Table of Contents

- [🖥️ System Requirements](#️-system-requirements)
- [📦 Installation Steps](#-installation-steps)
- [🚀 Start System](#-start-system)
- [🔧 Common Issues](#-common-issues)
- [📊 Performance Optimization](#-performance-optimization)
- [🔒 Security Configuration](#-security-configuration)
- [📝 Deployment Checklist](#-deployment-checklist)
- [📞 Get Help](#-get-help)
- [🔗 Next Steps](#-next-steps)

---

## 🖥️ System Requirements

### Hardware Requirements
- **Processor**: Dual-core 2.0GHz or higher
- **Memory**: 4GB RAM or higher (8GB recommended)
- **Storage**: 10GB available space
- **Network**: Stable internet connection

### Software Requirements
- **Operating System**: Windows 10+, Ubuntu 18.04+, macOS 10.15+
- **Python**: 3.8 or higher
- **Database**: MySQL 5.7+ or MySQL 8.0+
- **Browser**: Chrome 80+, Firefox 75+, Safari 13+

## 📦 Installation Steps

### 1. Environment Setup

#### Python Environment
```bash
# Check Python version
python --version
# Should display Python 3.8.x or higher

# If need to install Python
# Windows: Visit https://python.org to download installer
# Ubuntu: sudo apt-get install python3.8 python3-pip
# macOS: brew install python@3.8
```

#### MySQL Database
```bash
# Ubuntu installation
sudo apt-get update
sudo apt-get install mysql-server

# Windows: Download MySQL Installer
# macOS: brew install mysql

# Start MySQL service
sudo systemctl start mysql  # Linux
# Windows: Start through Service Manager
```

### 2. Get Code

```bash
# Contact developer to get complete code package
# Extract to target directory
unzip crypto-trading-system.zip
cd crypto-trading-system
```

### 3. Install Dependencies

```bash
# Install Python dependencies
pip install -r requirements.txt

# Or use virtual environment (recommended)
python -m venv venv
# Windows: venv\Scripts\activate
# Linux/macOS: source venv/bin/activate
pip install -r requirements.txt
```

### 4. Database Configuration

```sql
-- 1. Create database
CREATE DATABASE trade_db CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;

-- 2. Create user (optional)
CREATE USER 'trade_user'@'localhost' IDENTIFIED BY 'your_password';
GRANT ALL PRIVILEGES ON trade_db.* TO 'trade_user'@'localhost';
FLUSH PRIVILEGES;
```

```bash
# 3. Import database structure
mysql -u root -p trade_db < trade_db.sql
mysql -u root -p trade_db < stop_loss_tables.sql
mysql -u root -p trade_db < limit_follow_tables.sql
```

### 5. System Configuration

```bash
# Copy configuration file template
cp config_example.py config.py
```

Edit `config.py` file:

```python
# Database configuration
MYSQL_CONFIG = {
    'host': 'localhost',
    'user': 'trade_user',  # or 'root'
    'password': 'your_mysql_password',
    'db': 'trade_db',
    'port': 3306,
    'charset': 'utf8mb4'
}

# OKX API configuration
OKX_CONFIG = {
    'api_key': 'your_okx_api_key',
    'secret_key': 'your_okx_secret_key',
    'passphrase': 'your_okx_passphrase',
    'environment': 'sandbox'  # Test environment, change to 'live' for production
}

# DingTalk bot configuration (optional)
DINGTALK_CONFIG = {
    'enabled': True,
    'webhook_url': 'your_dingtalk_webhook',
    'secret': 'your_dingtalk_secret'
}
```

### 6. Get API Keys

#### OKX API Key Application
1. Login to [OKX Official Website](https://www.okx.com)
2. Go to API management page
3. Create new API key
4. Set IP whitelist (important)
5. Record API Key, Secret Key, Passphrase

#### Permission Settings
Make sure API key has the following permissions:
- ✅ Trading permission
- ✅ View permission
- ❌ Withdrawal permission (recommended to disable)

## 🚀 Start System

### Test Startup

```bash
# Test configuration
python -c "import config; print('Configuration file loaded successfully')"

# Test database connection
python -c "from db import get_connection; print('Database connection test:', get_connection())"
```

### Start Service

```bash
# Start complete system
python main.py

# Or start modules separately
python main.py trade     # Trading module only
python main.py api       # API service only
python main.py frontend  # Frontend service only
```

### Verify Installation

1. **Access Web Interface**: Open browser to `http://localhost:5000`
2. **Check Logs**: View `trades.log` file
3. **Test Connection**: Check connection status in Web interface

## 🔧 Common Issues

### Database Connection Failed
```bash
# Check MySQL service status
sudo systemctl status mysql

# Restart MySQL service
sudo systemctl restart mysql

# Check port usage
netstat -an | grep 3306
```

### Python Dependencies Installation Failed
```bash
# Upgrade pip
pip install --upgrade pip

# Use domestic mirror source
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple/

# Install dependencies individually
pip install flask pymysql websocket-client requests
```

### API Connection Failed
1. Check if API key is correct
2. Confirm IP whitelist settings
3. Verify network connection
4. Check firewall settings

### Frontend Interface Inaccessible
```bash
# Check port usage
netstat -an | grep 5000

# Change port configuration
# Add in config.py: WEB_PORT = 8080
```

## 📊 Performance Optimization

### Database Optimization
```sql
-- Add indexes
ALTER TABLE trades ADD INDEX idx_created_at (created_at);
ALTER TABLE positions ADD INDEX idx_symbol_customer (symbol, customer_uid);

-- Configure MySQL parameters
SET innodb_buffer_pool_size = 256M;
SET max_connections = 200;
```

### System Optimization
```bash
# Set Python environment variables
export PYTHONPATH=$PYTHONPATH:/path/to/your/project

# Configure log rotation
# Edit logger_config.py to adjust log level and rotation strategy
```

## 🔒 Security Configuration

### Firewall Settings
```bash
# Ubuntu UFW settings
sudo ufw allow 5000/tcp  # Web interface port
sudo ufw allow 3306/tcp  # MySQL port (local only)
sudo ufw enable
```

### SSL Certificate (Production Environment)
```bash
# Generate self-signed certificate (for testing)
openssl req -x509 -newkey rsa:4096 -nodes -out cert.pem -keyout key.pem -days 365

# Or use Let's Encrypt (recommended)
sudo apt-get install certbot
sudo certbot certonly --standalone -d yourdomain.com
```

## 📝 Deployment Checklist

- [ ] Python 3.8+ installed
- [ ] MySQL service running normally
- [ ] Database created and table structure imported
- [ ] config.py configuration completed
- [ ] API keys configured and tested
- [ ] Dependencies installation completed
- [ ] Firewall rules set
- [ ] Log directory writable
- [ ] Web interface accessible normally
- [ ] System logs error-free

## 📞 Get Help

If you encounter problems during installation, please:

1. View system logs: `tail -f trades.log`
2. Check error information and search for solutions
3. Submit Issue: [GitHub Issues](https://github.com/hall130/crypto_trade_for_public/issues)
4. Send email: saylas163@gmail.com

---

## 🔗 Next Steps

After installation is complete, please continue reading:

- [📖 User Manual](./USER_GUIDE_EN.md) - Learn how to use the system
- [🏗️ System Architecture](./ARCHITECTURE_EN.md) - Understand technical architecture  
- [🔌 API Documentation](./API_REFERENCE_EN.md) - View interface documentation
- [📱 Frontend Documentation](../frontend/README_EN.md) - Frontend development guide

### 📚 Related Documents

- [⬆️ Back to Main Documentation](../README_EN.md#-documentation)
- [📝 View Changelog](./CHANGELOG_EN.md)
- [❓ Get Technical Support](../README_EN.md#-technical-support) 