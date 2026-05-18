# Sacra Alert System

Sacra Alert System is a scheduled TypeScript worker for monitoring Sacra RPC,
subgraph, and proxy-subgraph health. It stores metric snapshots in Postgres and
sends Telegram alerts when monitored services drift or fail.

## What It Checks

- RPC target/source block agreement.
- Subgraph indexed block versus RPC block.
- Subgraph response time.
- Proxy subgraph pawnshop item responses.
- Periodic health-check messages.

## Configuration

Create a local `.env` from `.env.pub`:

```bash
cp .env.pub .env
```

Important variables:

- `TELEGRAM_BOT_TOKEN` and `TELEGRAM_CHAT_ID`.
- `PING_USERS` for users to mention on alerts.
- `RPC_TARGET`, `RPC_SOURCE`, `RPC_NAME`.
- `SUBGRAPH_TARGET`, `SUBGRAPH_SOURCE`, `SUBGRAPH_NAME`.
- `PROXY_SUBGRAPH_TARGET`, `PROXY_SUBGRAPH_NAME`.
- `JOB_INTERVAL`.
- `DB_HOST`, `DB_USERNAME`, `DB_PASSWORD`, `DB_NAME`.

Do not commit real Telegram tokens or database credentials.

## Thresholds

- RPC comparison uses `DIFFERENCE_THRESHOLD` from the environment, defaulting
  to 70 blocks.
- Subgraph block drift currently alerts above 15 blocks.
- Subgraph response-time alerts above 10000 ms.
- Proxy subgraph checks alert when the pawnshop item response is empty.

## Commands

```bash
yarn install
yarn build
yarn migration:run
yarn start
```

For development:

```bash
yarn dev
```

## Source Layout

- `src/main.ts` - scheduled monitoring job.
- `src/service/rpc.service.ts` - RPC block checks.
- `src/service/subgraph.service.ts` - subgraph block, latency, and proxy checks.
- `src/service/telegram.service.ts` - Telegram alert formatting and sending.
- `src/entity/` and `src/migration/` - TypeORM state persistence.

## Operational Notes

- Real and Fantom endpoints are present in the public env example. Confirm
  whether Sonic monitoring should be added before treating this as complete
  production coverage.
- Keep alert routing and ping policy documented with the operator runbook.
