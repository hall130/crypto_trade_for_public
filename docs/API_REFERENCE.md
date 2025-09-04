# 🔌 API接口文档

本文档提供了加密货币交易所自动跟单系统的完整API接口说明。

## 📋 目录

- [🔗 接口概览](#-接口概览)
- [🔐 认证说明](#-认证说明)
- [👥 客户管理API](#-客户管理api)
- [📡 信号源管理API](#-信号源管理api)
- [⚙️ 策略管理API](#️-策略管理api)
- [📈 交易记录API](#-交易记录api)
- [📊 数据统计API](#-数据统计api)
- [⚙️ 系统管理API](#️-系统管理api)
- [🚨 错误码说明](#-错误码说明)
- [📝 请求示例](#-请求示例)

## 🔗 接口概览

### 基础信息

- **接口基址**: `http://localhost:5000/api/v1`
- **数据格式**: JSON
- **字符编码**: UTF-8
- **请求方法**: GET, POST, PUT, DELETE
- **响应格式**: 统一响应格式

### 统一响应格式

```json
{
  "code": 200,
  "message": "success",
  "data": {},
  "timestamp": "2024-01-15T10:30:00Z"
}
```

## 🔐 认证说明

### 请求头设置

```http
Content-Type: application/json
Authorization: Bearer {access_token}
X-API-Version: v1
```

### 认证流程

1. 获取访问令牌
2. 在请求头中携带token
3. 服务器验证token有效性
4. 返回相应数据或错误信息

## 👥 客户管理API

### 获取客户列表

```http
GET /api/v1/customers
```

**查询参数**:
- `page`: 页码 (默认: 1)
- `limit`: 每页数量 (默认: 20)
- `search`: 搜索关键词
- `status`: 客户状态 (active/inactive)

**响应示例**:
```json
{
  "code": 200,
  "message": "success",
  "data": {
    "customers": [
      {
        "id": 1,
        "name": "客户A",
        "api_key": "okx_api_key",
        "status": "active",
        "balance": 10000.00,
        "created_at": "2024-01-15T10:30:00Z"
      }
    ],
    "total": 25,
    "page": 1,
    "limit": 20
  }
}
```

### 添加客户

```http
POST /api/v1/customers
```

**请求体**:
```json
{
  "name": "客户名称",
  "api_key": "OKX API密钥",
  "secret_key": "OKX Secret密钥",
  "passphrase": "OKX API密码",
  "initial_balance": 10000.00,
  "max_leverage": 10
}
```

### 更新客户信息

```http
PUT /api/v1/customers/{customer_id}
```

### 删除客户

```http
DELETE /api/v1/customers/{customer_id}
```

### 获取客户详情

```http
GET /api/v1/customers/{customer_id}
```

## 📡 信号源管理API

### 获取信号源列表

```http
GET /api/v1/signals
```

**响应示例**:
```json
{
  "code": 200,
  "data": {
    "signals": [
      {
        "id": 1,
        "name": "信号源A",
        "exchange": "okx",
        "status": "connected",
        "last_signal": "2024-01-15T10:25:00Z",
        "signal_count_today": 15
      }
    ]
  }
}
```

### 添加信号源

```http
POST /api/v1/signals
```

### 更新信号源

```http
PUT /api/v1/signals/{signal_id}
```

### 删除信号源

```http
DELETE /api/v1/signals/{signal_id}
```

## ⚙️ 策略管理API

### 获取策略列表

```http
GET /api/v1/strategies
```

### 创建策略

```http
POST /api/v1/strategies
```

**请求体**:
```json
{
  "name": "策略名称",
  "signal_source_id": 1,
  "follow_ratio": 0.1,
  "max_leverage": 10,
  "symbols": ["BTC-USDT-SWAP", "ETH-USDT-SWAP"],
  "status": "active"
}
```

### 绑定客户到策略

```http
POST /api/v1/strategies/{strategy_id}/customers
```

**请求体**:
```json
{
  "customer_ids": [1, 2, 3]
}
```

## 📈 交易记录API

### 获取交易记录

```http
GET /api/v1/trades
```

**查询参数**:
- `start_date`: 开始日期
- `end_date`: 结束日期
- `customer_id`: 客户ID
- `symbol`: 交易对
- `status`: 交易状态

**响应示例**:
```json
{
  "code": 200,
  "data": {
    "trades": [
      {
        "id": 1,
        "customer_id": 1,
        "symbol": "BTC-USDT-SWAP",
        "side": "buy",
        "amount": 0.1,
        "price": 45000.00,
        "status": "filled",
        "created_at": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
```

### 手动交易

```http
POST /api/v1/trades/manual
```

**请求体**:
```json
{
  "customer_id": 1,
  "symbol": "BTC-USDT-SWAP",
  "side": "buy",
  "amount": 0.1,
  "order_type": "market"
}
```

## 📊 数据统计API

### 获取仪表盘数据

```http
GET /api/v1/dashboard/stats
```

**响应示例**:
```json
{
  "code": 200,
  "data": {
    "total_customers": 25,
    "active_customers": 20,
    "today_trades": 156,
    "active_strategies": 8,
    "total_profit": 12500.00,
    "system_status": "正常"
  }
}
```

### 获取图表数据

```http
GET /api/v1/dashboard/charts
```

**查询参数**:
- `type`: 图表类型 (profit/volume/trades)
- `period`: 时间周期 (1d/7d/30d)

## ⚙️ 系统管理API

### 获取系统状态

```http
GET /api/v1/system/status
```

### 系统设置

```http
GET /api/v1/system/settings
PUT /api/v1/system/settings
```

### 日志查询

```http
GET /api/v1/system/logs
```

## 🚨 错误码说明

| 错误码 | 说明 | 解决方案 |
|--------|------|----------|
| 200 | 请求成功 | - |
| 400 | 请求参数错误 | 检查请求参数格式 |
| 401 | 未授权 | 检查认证信息 |
| 403 | 禁止访问 | 检查权限设置 |
| 404 | 资源不存在 | 检查请求路径 |
| 429 | 请求过于频繁 | 降低请求频率 |
| 500 | 服务器内部错误 | 联系技术支持 |

### 错误响应格式

```json
{
  "code": 400,
  "message": "参数错误",
  "error": "customer_id字段不能为空",
  "timestamp": "2024-01-15T10:30:00Z"
}
```

## 📝 请求示例

### JavaScript请求示例

```javascript
// 获取客户列表
const response = await fetch('/api/v1/customers', {
  method: 'GET',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer your_token_here'
  }
});

const data = await response.json();
console.log(data);
```

### Python请求示例

```python
import requests

url = 'http://localhost:5000/api/v1/customers'
headers = {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer your_token_here'
}

response = requests.get(url, headers=headers)
data = response.json()
print(data)
```

### cURL请求示例

```bash
# 获取客户列表
curl -X GET \
  http://localhost:5000/api/v1/customers \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer your_token_here'

# 添加客户
curl -X POST \
  http://localhost:5000/api/v1/customers \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "测试客户",
    "api_key": "your_api_key",
    "secret_key": "your_secret_key",
    "passphrase": "your_passphrase"
  }'
```

## 📚 相关文档

- [📋 安装指南](./INSTALLATION.md) - 系统安装和配置
- [📖 用户手册](./USER_GUIDE.md) - 完整操作指南
- [🏗️ 系统架构](./ARCHITECTURE.md) - 技术架构说明
- [📱 前端文档](../frontend/README.md) - 前端技术文档
- [📝 更新日志](./CHANGELOG.md) - 版本更新记录

## 🔧 SDK和工具

### JavaScript SDK

```javascript
class OKXTradingAPI {
  constructor(baseURL, token) {
    this.baseURL = baseURL;
    this.token = token;
  }
  
  async getCustomers() {
    return await this.request('/customers');
  }
  
  async createCustomer(customerData) {
    return await this.request('/customers', 'POST', customerData);
  }
}
```

### Python SDK

```python
class OKXTradingAPI:
    def __init__(self, base_url, token):
        self.base_url = base_url
        self.token = token
    
    def get_customers(self):
        return self._request('/customers')
    
    def create_customer(self, customer_data):
        return self._request('/customers', 'POST', customer_data)
```

## 📞 技术支持

如有API使用问题，请通过以下方式获取帮助：

- **GitHub Issues**: [提交问题](https://github.com/hall130/crypto_trade_for_public/issues)
- **邮箱支持**: saylas163@gmail.com
- **文档中心**: [查看更多文档](../README.md#-文档目录)

---

> **更新时间**: 2024-01-15  
> **文档版本**: v1.0.0  
> **API版本**: v1 