# A股只读行情核验页

Cloudflare Worker 固定读取 560390、516350、516080、563360、上证指数与创业板指，并对新浪和腾讯两个公开行情源做价格与时间戳交叉核验。

- 盘中双源一致且时间戳不超过 120 秒时标记 `LIVE_VERIFIED`。
- 收盘后双源一致且时间戳为当日 14:57 以后时标记 `CLOSE_VERIFIED`。
- 任一路缺失、价格冲突或时间戳过旧时标记 `NOT_EXECUTION_GRADE`，不得据此新增或撤销风险仓。
- 只读固定白名单，不接触账户、持仓、订单或交易功能。

线上入口：https://cn-market-bridge.1773471257.workers.dev/latest

结构化数据：https://cn-market-bridge.1773471257.workers.dev/latest.json；健康检查：https://cn-market-bridge.1773471257.workers.dev/health。

Worker 在北京时间工作日 14:42–14:50 每分钟、15:05 和 18:40 生成快照，并写入公开 GitHub 仓库 `liuchengyann-bit/etf-market-bridge` 的 `data/latest.json`。即使数据源失败，也会写入不可执行状态，避免旧快照被误认为实时数据。

GitHub 写入凭证仅作为 Cloudflare 加密 Secret 保存，权限限制为该仓库的 `Contents: Read and write`；代码和仓库中不保存密钥。
