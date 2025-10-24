# PiggyCell 自动签到和游戏机器人

这是一个用于PiggyCell网站的自动签到和游戏机器人，支持多账户、定时签到、自动游戏和代理配置。

## 功能特点

- ✅ 多账户支持
- ✅ 定时自动签到
- ✅ 自动游戏功能
- ✅ 代理支持
- ✅ 详细日志记录
- ✅ 配置化管理
- ✅ 错误重试机制
- ✅ Token有效性检测

## 安装依赖

```bash
pip install -r requirements.txt
```

## 文件结构

```
piggycell/
├── config/
│   ├── config.json         # 主配置文件
│   └── accounts.json       # 账户信息
├── proxy.txt              # 代理配置
├── piggycell_bot.py       # 主程序
├── start.py               # 启动脚本
├── requirements.txt       # 依赖包
└── README.md             # 使用说明
```

## 配置说明

### 1. 配置文件 (config/config.json)

```json
{
    "sign_in_time": "09:00",
    "api_url": "https://app.piggycell.io/api/trpc/attendance.attend?batch=1",
    "headers": {
        "accept": "*/*",
        "user-agent": "Mozilla/5.0 ...",
        ...
    },
    "log_level": "INFO",
    "retry_times": 3,
    "retry_delay": 5
}
```

### 2. 账户配置 (config/accounts.json)

推荐使用JSON格式，更直观且功能更丰富：

```json
{
    "accounts": [
        {
            "name": "account1",
            "session_token": "你的session_token",
            "cookies": "你的cookie信息",
            "enabled": true,
            "description": "主账户"
        },
        {
            "name": "account2",
            "session_token": "另一个session_token", 
            "cookies": "另一个cookie信息",
            "enabled": false,
            "description": "备用账户"
        }
    ]
}
```

**字段说明：**
- `name`: 账户名称
- `session_token`: 会话令牌（必需）
- `cookies`: 其他cookie信息（可选）
- `enabled`: 是否启用该账户（可选，默认true）
- `description`: 账户描述（可选）

### 3. 代理配置 (proxy.txt)

```
http://username:password@proxy.example.com:8080
socks5://127.0.0.1:1080
```

每行一个代理地址，程序会随机选择代理进行请求。

## 使用方法

### 签到功能

#### 方法1: 使用启动脚本（推荐）
```bash
python start.py
```

#### 方法2: 直接运行
立即执行一次签到：
```bash
python piggycell_bot.py --once
```

启动定时任务：
```bash
python piggycell_bot.py
```

### 游戏功能

#### 方法1: 使用游戏启动脚本（推荐）
```bash
python game_start.py
```

#### 方法2: 直接运行
立即执行一次游戏：
```bash
python piggy_game_bot.py --once
```

启动定时任务：
```bash
python piggy_game_bot.py
```

## 获取Session Token

1. 打开浏览器，登录PiggyCell网站
2. 按F12打开开发者工具
3. 切换到Network标签页
4. 执行一次手动签到
5. 在请求中找到包含`__Secure-authjs.session-token`的Cookie
6. 复制完整的Cookie值到accounts.txt文件中

## Token有效性检测

程序会在执行签到前自动检测token的有效性：

- **自动解析JWT token**：解析token中的过期时间信息
- **提前预警**：如果token在30分钟内过期，会显示警告并跳过该账户
- **显示剩余时间**：显示每个token的剩余有效时间
- **智能处理**：如果无法解析token，程序会继续运行（兼容不同格式的token）

## 游戏代码处理

游戏机器人使用以下方式处理游戏代码：

- **动态获取**：每次从API响应中获取最新的游戏代码
- **自动解析**：解析API响应中的`{json: {gameCode: "7HNH4I"}}`格式
- **配置化管理**：如果无法解析，使用配置的默认代码
- **智能回退**：确保游戏能够正常进行

### 游戏代码流程

1. **调用API**：请求`getExtraChargeCount`获取游戏信息
2. **解析响应**：从响应中提取`gameCode`字段
3. **使用代码**：使用解析到的动态游戏代码
4. **回退机制**：如果解析失败，使用默认代码

### 游戏代码配置

在`config.json`中设置默认代码（仅作为备用）：
```json
{
    "game_settings": {
        "default_game_code": "834KJY"
    }
}
```

## 注意事项

- 请确保账户信息的准确性
- 建议在测试环境中先验证配置
- 定期检查日志文件确保正常运行
- 遵守网站的使用条款
- Token过期前请及时更新session_token

## 日志文件

程序运行时会生成`piggycell_bot.log`文件，记录详细的运行日志。

## 故障排除

1. **签到失败**: 检查session_token是否过期
2. **网络错误**: 检查代理配置或网络连接
3. **配置文件错误**: 检查JSON格式是否正确
4. **权限问题**: 确保有文件读写权限
# piggycell_bot
