# Tavily MCP Load Balancer

一个支持多API密钥负载均衡的Tavily MCP服务器，可以自动轮询使用多个API密钥，提供高可用性和更高的请求限制。

## 功能特性

- 🔄 **负载均衡**: 自动轮询多个API密钥
- 🛡️ **故障转移**: 自动禁用失效的API密钥
- 📊 **统计监控**: 实时查看密钥使用状态
- 🌐 **SSE支持**: 通过supergateway提供SSE接口
- ⚡ **高性能**: 基于TypeScript和现代Node.js

## 快速开始

### 1. 安装依赖

```bash
cd tavily-mcp-loadbalancer
npm install
```

### 2. 配置环境变量

复制环境变量模板：
```bash
cp .env.example .env
```

编辑`.env`文件，添加你的API密钥：
```bash
# 多个密钥用逗号分隔（推荐）
TAVILY_API_KEYS=tvly-dev-key1,tvly-dev-key2,tvly-dev-key3

# 或者使用单个密钥
# TAVILY_API_KEY=tvly-dev-your-key
```

### 启动服务器

**方法1：使用npm脚本（推荐）**
```bash
npm run build-and-start
```

**方法2：使用bash脚本**
```bash
./start.sh
```

**方法3：分步执行**
```bash
npm run build
npm run start-gateway
```

服务器将在 `http://0.0.0.0:60002` 上启动，提供SSE接口。

## 使用方法

### 直接使用MCP（stdio）

```bash
node dist/index.js
```

### 通过SSE接口使用

启动后，你可以通过以下端点访问：
- SSE连接: `http://0.0.0.0:60002/sse`
- 消息发送: `http://0.0.0.0:60002/message`

### 可用工具

#### 1. tavily-search
搜索网络内容：
```json
{
  "name": "tavily-search",
  "arguments": {
    "query": "OpenAI GPT-4",
    "search_depth": "basic",
    "topic": "general",
    "max_results": 10
  }
}
```

#### 2. tavily-extract
提取网页内容：
```json
{
  "name": "tavily-extract",
  "arguments": {
    "urls": ["https://example.com/article"],
    "extract_depth": "basic"
  }
}
```

#### 3. tavily-crawl
爬取网站：
```json
{
  "name": "tavily-crawl",
  "arguments": {
    "url": "https://example.com",
    "max_depth": 2,
    "limit": 10
  }
}
```

#### 4. tavily-map
生成网站地图：
```json
{
  "name": "tavily-map",
  "arguments": {
    "url": "https://example.com",
    "max_depth": 1
  }
}
```

#### 5. tavily_get_stats
获取API密钥池统计信息：
```json
{
  "name": "tavily_get_stats",
  "arguments": {}
}
```

## 监控和管理

### 使用管理脚本（推荐）

```bash
# 查看API密钥池状态
./manage.sh stats

# 测试所有工具功能
./manage.sh test

# 显示帮助信息
./manage.sh help
```

### 直接使用Node.js脚本

```bash
# 查看详细统计信息
node check_stats_direct.cjs

# 运行工具测试
node test_tools_direct.cjs
```

### 监控输出示例
```
📊 获取API密钥池状态...
========================================
✅ 连接成功

📊 API密钥池统计信息:
========================================
总密钥数量: 3
活跃密钥数量: 2

📋 密钥详情:
----------------------------------------
🔑 密钥: tvly-dev-T...
   状态: 🟢 活跃
   错误次数: 0/5
   最后使用: 2024-01-15T10:30:00.000Z
   权重: 1

🔑 密钥: tvly-dev-Y...
   状态: 🔴 禁用
   错误次数: 5/5
   最后使用: 2024-01-15T10:25:00.000Z
   权重: 1
```

## 环境变量

| 变量名 | 描述 | 默认值 |
|--------|------|---------|
| `TAVILY_API_KEYS` | API密钥列表（逗号分隔） | - |
| `TAVILY_API_KEY` | 单个API密钥 | - |
| `SUPERGATEWAY_PORT` | 服务端口 | 60002 |
| `SUPERGATEWAY_BASE_URL` | 基础URL | http://0.0.0.0:60002 |
| `SUPERGATEWAY_SSE_PATH` | SSE路径 | /sse |
| `SUPERGATEWAY_MESSAGE_PATH` | 消息路径 | /message |

## 架构设计

### API密钥池管理
- 轮询调度算法
- 自动故障检测和恢复
- 错误计数和自动禁用
- 实时统计和监控

### 负载均衡策略
- 轮询（Round Robin）
- 故障转移（Failover）
- 健康检查

## 开发

### 开发模式运行
```bash
npm run dev
```

### 构建项目
```bash
npm run build
```

### 运行测试
```bash
npm test
```

## 故障排除

### 常见问题

1. **No available API keys**
   - 检查环境变量是否正确设置
   - 确保至少有一个有效的API密钥

2. **API密钥被禁用**
   - 检查API密钥是否有效
   - 查看错误日志确定问题原因

3. **连接超时**
   - 检查网络连接
   - 确认API服务是否正常

### 日志信息

服务器会输出详细的日志信息，包括：
- API密钥使用情况
- 错误信息和故障转移
- 请求统计

## 许可证

MIT License