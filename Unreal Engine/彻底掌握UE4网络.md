[课程地址](https://www.bilibili.com/video/BV1dT4y1N7de)

## 1 内容概要

### 1.1前言
- 明确我们所讲的网络
  - 实时（相对于弱联网）
  - 不涉及Http以及数据库（注册登录增删改查等）
  - 有“Session（会话）”概念。（CS里面的房间就是一个会话。弹幕说：房间不是会话，是一个频道。会话是一个房间客户端和服务器的交互）
  - 适用于LAN和WAN

### 1.2 内容概要
- UE4网络架构
- Replication网络复制（不是传统的复制，是服务器到客户端的复制）
- Ownership所有权
- Actor Role（三种角色）
- RPC（Remote Procedure Cal）
- 影响网络游戏流畅度的因素
- 相关性(优化）
- 创建和加入Session
- Travelling
- 项目实例
- 打包和测试

## 2 网络构架

### 2.1 Server-Client构架
1. 一个服务器，一个或多个客户端（服务器是数据中心）
2. 不能信任客户端，所有重要信息都需要通过服务器验证（防止玩家作弊，必须服务器验证）
3. Listen Server (等待其他玩家加入） & Dedicated Server 
