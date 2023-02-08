# Relayer for Sable Cash

## Run locally

1. `yarn`
2. `cp .env.example .env`
3. Modify `.env` as needed
4. `yarn start`
5. Go to `http://127.0.0.1:8000`
6. 为了执行提款请求，您可以运行以下命令

```bash
curl -X POST -H 'content-type:application/json' --data '<input data>' http://127.0.0.1:8000/relay
```

中继器应返回 交易哈希 
在这种情况下，您需要自己添加 https 终止，因为具有默认设置的浏览器会阻止 https, 通过 http 连接提交请求的 sable.cash UI

## Run geth node

强烈建议您使用自己的 RPC 节点。有关如何使用 `geth` 运行完整节点的说明。

## Architecture

1. TreeWatcher 跟踪 帐户树的更改 并将 实际状态缓存在 Redis，并向 Redis 发布/订阅通道发出 `treeUpdate` 事件
2. Server 是接受 http 请求的 Express.js 服务实例
3. Controller 包含服务器端点的处理程序。它验证输入数据并将作业添加到队列。
4. Queue module 被Controller用来从队列中放置和获取Job（bull wrapper）
5. Status module 包含获取作业状态的处理程序。它被 UI 用于拉取更新
6. Validate 包含所有端点的验证逻辑
7. Worker 是从队列中获取作业并处理它的主要模块
 