---
layout: post
title: 【实践】Java开发基于Spring AI Alibaba玩转MCP：从发布、调用到 Claude 集成
tags: [Java, Spring AI, MCP, Claude]
categories: 技术文章
---

## 0 前言

参考链接：[Java开发基于Spring AI Alibaba玩转MCP：从发布、调用到 Claude 集成 - Spring AI Alibaba](https://java2ai.com/blog/spring-ai-alibaba-mcp/)

项目“企业级分布式 MCP 服务部署与调用方案与实现”要求“将 Spring Cloud Alibaba 应用发部位 MCP 服务并实现 MCP 地址、工具等信息的注册；其次，由于 Spring Cloud Alibaba 应用存在分布式多节点部署的特点，框架需要支持智能体快速接入MCP服务并实现流量在多节点间的均衡分布。”

本文基于参考文章进行实践，记录注意事项及收获，同时为上述目标做开发规划。

## 1 模型上下文协议（Model Context Protocol）入门

2024年11月，Anthropic设计了Model Context Protocol（模型上下文协议，简称MCP），为AI和各类数据之间建立了标准化的桥梁。

可以通过awesome-mcp-servers、mcp.so获取MCP服务。

常见的应用场景：

- 使用百度/高德地图分析旅线计算时间
- 接 Puppeteer 自动操作网页
- 使用 Github/Gitlab 让大模型接管代码仓库
- 使用数据库组件完成对 Mysql、ES、Redis 等数据库的操作
- 使用搜索组件扩展大模型的数据搜索能力

### 1.1 在 Claude Desktop 中体验 MCP

注意：Claude Desktop安装网站限定英美地区，需配置对应区域代理。

Windows中，Claude配置文件的路径是`%APPDATA%\Roaming\Claude\desktop_config.json`（初始时文件不存在，需要创建）。

申请GitHub token：Settings - Developer settings - Personal access tokens - Tokens (classic)。

按照参考文章实践，成功利用GitHub MCP工具使AI自动创建仓库：

![](../images/2025-06-06-【实践】Java开发基于Spring%20AI%20Alibaba玩转MCP：从发布、调用到%20Claude%20集成/1%20自动创建GitHub仓库-1.png)

![](../images/2025-06-06-【实践】Java开发基于Spring%20AI%20Alibaba玩转MCP：从发布、调用到%20Claude%20集成/2%20自动创建GitHub仓库-2.png)

## 1.2 MCP 的架构

- 客户端：一般指的是**大模型应用**，比如 Claude、通过 Spring AI Alibaba、Langchain 等框架开发的 AI 应用
- 服务端：连接各种**数据源**的服务和工具

![](../images/2025-06-06-【实践】Java开发基于Spring%20AI%20Alibaba玩转MCP：从发布、调用到%20Claude%20集成/3%20MCP的架构.png)
即：Server对接各种数据源和服务，并通过MCP协议提供给Client。

## 2 在 Spring AI 中使用 Mcp Server

### 2.1 Spring AI MCP 的介绍

![](../images/2025-06-06-【实践】Java开发基于Spring%20AI%20Alibaba玩转MCP：从发布、调用到%20Claude%20集成/4%20Spring%20AI%20MCP架构.png)

### 2.2 使用 Spring AI MCP 快速搭建 MCP Server

两种快速搭建MCP的方式：
- 基于 stdio 的进程间通信传输（轻量级）
- 基于 SSE（Server-Sent Events） 进行远程服务访问（重量级）

链接应为(https://github.com/springaialibaba/spring-ai-alibaba-examples/tree/main/spring-ai-alibaba-mcp-example/spring-ai-alibaba-mcp-starter-example)。后续可考虑提Issue。

#### 2.2.1 基于 stdio 的 MCP 服务端实现

##### 1 测试方法

参考文档：mcp-webflux-server-example下的README

在mcp-webflux-server-example下编译项目：

```bash  
mvn clean package -DskipTests
```

作为 STDIO 服务器启动（注意修改版本号）：

```sh
java -jar .\target\mcp-webflux-server-example-1.0.0.jar
```

测试结果见3.1节。
##### 2 文档改进

文档中说添加spring-ai-mcp-server-spring-boot-starter依赖，但参考代码Ctrl+Shift+F全局搜索不到，可能有变。mcp-webflux-server-example支持stdio，所以使用此服务端进行测试。

配置文件为.yml格式，文档中为application.properties格式。

版本号可以改为1.0.0。

jar所在路径要改。

（该步骤原命令报错）：
```
(base) PS D:\Sync\Codes\6-2025\spring-ai-alibaba-examples\spring-ai-alibaba-mcp-example\spring-ai-alibaba-mcp-starter-example\server\mcp-webflux-server-example> java -Dspring.ai.mcp.server.stdio=true \
错误: 找不到或无法加载主类 .ai.mcp.server.stdio=true
原因: java.lang.ClassNotFoundException: /ai/mcp/server/stdio=true
```

实际上，相关配置已经在mcp-servers-config.json中配置好，无需各种参数，直接执行即可。

#### 2.2.2 基于 SSE 的 MCP 服务端实现

仍使用mcp-webflux-server-example项目，改为如下方式启动：

```
mvn spring-boot:run
```

服务端将在 [http://localhost:8080](http://localhost:8080/?spm=0.29160081.0.0.46a720f6wK8L9y) 启动。测试结果见3.2节。

### 2.3 在 Claude 中测试 mcp 服务

在添加weather相关的配置，补充jar包路径：
```json
    "weather": {
        "command": "java",
        "args": [
            "-Dspring.ai.mcp.server.stdio=true",
            "-Dspring.main.web-application-type=none",
            "-Dlogging.pattern.console=",
            "-jar",
            "D:\\Sync\\Codes\\6-2025\\spring-ai-alibaba-examples\\spring-ai-alibaba-mcp-example\\spring-ai-alibaba-mcp-starter-example\\server\\mcp-webflux-server-example\\target\\mcp-webflux-server-example-1.0.0.jar"
        ],
        "env": {}
    }
```

在Claude Desktop中可以查看自定义的MCP天气服务，并能成功调用查询：

![](../images/2025-06-06-【实践】Java开发基于Spring%20AI%20Alibaba玩转MCP：从发布、调用到%20Claude%20集成/5%20天气查询.png)

## 3 在 Spring AI Alibaba 中集成 Mcp Client

### 3.1 基于 stdio 的 MCP 客户端实现

配合2.2.1节基于 stdio 的 MCP 服务端测试。运行服务端。

在mcp-stdio-client-example中配置mcp-servers-config.json的路径：

```json
// 二选一
// 相对路径（同项目测试可以使用）
"spring-ai-alibaba-mcp-example/spring-ai-alibaba-mcp-starter-example/server/mcp-webflux-server-example/target/mcp-webflux-server-example-1.0.0.jar"
// 绝对路径
"D:\\Sync\\Codes\\6-2025\\spring-ai-alibaba-examples\\spring-ai-alibaba-mcp-example\\spring-ai-alibaba-mcp-starter-example\\server\\mcp-webflux-server-example\\target\\mcp-webflux-server-example-1.0.0.jar"
```

启动client（确保API_KEY环境变量已配置且IDEA已重启），运行正常：

```sh
>>> QUESTION: 北京的天气如何？
>>> ASSISTANT: 当前北京的天气如下：

- 温度: 25.0°C (体感温度: 23.6°C)
- 天气状况: 多云
- 风向与风速: 南风 (7.6 km/h)
- 湿度: 36%
- 降水量: 0.0 毫米
...
```

### 3.2 基于 SSE 的 MCP 客户端实现

配合2.2.1节基于 stdio 的 MCP 服务端测试。运行服务端。

运行mcp-webflux-client-example，即可得到结果：

```sh
>>> QUESTION: 上海的天气如何？
>>> ASSISTANT: 上海当前的天气情况如下：

- 温度: 23.6°C (体感温度: 28.4°C)
- 天气状况: 阵雨
- 风向与风速: 东南风 (3.6 km/h)
- 湿度: 94%
- 降水量: 0.3 毫米
...

>>> QUESTION: 将 user 转为大写
>>> ASSISTANT: 将 "user" 转为大写后得到的是 "USER"。
```

## 4 在 Spring AI Alibaba 的 Open Manus 中体验 MCP

百度地图AK尚在申请中。

## 5 总结

总体而言，MCP服务端的实现包括5部分，分别是添加依赖、配置MCP服务端、实现MCP工具、注册MCP工具和运行服务端。客户端则为添加依赖、配置MCP服务器。
