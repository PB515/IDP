# Runbook — Migrations

*The #1 gap across all four builds. The runner (`tooling/migrate`) makes schema changes versioned, tracked, and reversible — SQL files + drift detection, not an ORM.*

## Daily commands

```bash
npm run migrate:status   # what's applied vs pending
npm run migrate:up       # apply all pending (or: npm run migrate up 1 — the next one only)
npm run db:check         # drift detection — non-zero exit if db and files disagree (CI gate)
npm run migrate:down     # roll back the last one (or: npm run migrate down 2)
npm run db:types         # regenerate lib/supabase/database.types.ts from the live schema
```

`up` also regenerates the row types, so they're generated, never hand-written.

## Writing a migration

Create `template/db/migrations/NNNN_name.sql` (next zero-padded number):

```sql
-- migrate:up
alter table orders add column refunded_at timestamptz;

-- migrate:down
alter table orders drop column refunded_at;
```

- `down` is the exact inverse of `up` — so a rollback is clean.
- One logical change per file. Lexical order (the `NNNN` prefix) is apply order.

## The rules

1. **Never edit a migration that's already applied.** The runner stores a checksum; editing an applied file shows as `MODIFIED` drift. To change applied schema, write a *new* migration.
2. **`db:check` is a pre-deploy gate.** It catches three drifts: `pending` (a file not applied), `modified` (an applied file was edited), `orphan` (db_meta row with no file). Wire it into CI.
3. **Changing schema is a frozen-doc change** (Safety Rail 7) — update doc 06 (schema) first, commit separately, then write the migration.
4. **Test down before you rely on it.** `up` then `down` on the local stack, confirm the table/column state, before trusting a production rollback.

## Connection

`DATABASE_URL` in `.env.local`; defaults to the local Supabase Postgres (`...54322`) if unset. Run migrations against the local stack first, then the deployed DB.

## Schema-level drift

`db:check` compares the migration *files* to the applied state. For true schema drift (the DB structure vs your SQL), use `supabase db diff --local`.
