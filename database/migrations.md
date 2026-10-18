# Database migrations

The server uses Alembic/Flask-Migrate with PostgreSQL. Back up an existing database, then run migrations in order. For a fresh database, `flask --app app db upgrade` applies all revisions. For a staged review, apply each revision individually:

| Order | Revision | Change |
| --- | --- | --- |
| 0 | `01e7c4a00000` | Original user, book, purchase, receipt, review, and admin-request tables for a fresh database |
| 1 | `4280997db745` | Media records |
| 2 | `b47f19d54a13` | Copies and loans |
| 3 | `c8ad12e960b0` | Existing constraints |
| 4 | `3d636ddd36b6` | Condition and activity |
| 5 | `5a24f7c7e001` | Archive and delete state |
| 6 | `6b35f8d8e002` | Legacy comments and stars |
| 7 | `7c46a9e9f003` | Authors, publishers, countries |
| 8 | `8d57bafa0004` | Rental workflow |
| 9 | `9e68cb0b0005` | Consolidated reviews |
| 10 | `af79dc1c0006` | Shared audit fields |
| 11 | `b8e0c71a0007` | Canonical user roles |
| 12 | `c9f1d82b0008` | Persistent permission, role, and group management |
| 13 | `d0a2e93c0009` | Payment transactions, receipt snapshots, loan policy, and session version |
| 14 | `e47a2c9d0010` | Soft retire the obsolete `review:edit.any` permission; only authors may edit reviews |

Run `flask --app app db upgrade REVISION` for each revision in the table. Afterward, `flask --app app db check` should report no pending schema changes. The review migration preserves legacy standalone entries as archived history. The access migration seeds the built-in permission keys and roles.

The baseline revision makes a new database migratable from zero. Databases already at a later revision keep their current history; apply the new head normally. A clean in-memory SQLite upgrade through all revisions was verified on 2026-10-17. On 2026-10-18, the local PostgreSQL database was backed up, upgraded from `d0a2e93c0009` to `e47a2c9d0010`, and passed `flask db check` with no new upgrade operations. Repeat the backup, migration, and drift check against the actual deployment database before release.

## Payment snapshot migration: d0a2e93c0009

Follows `c9f1d82b0008`. Creates transaction/event tables; adds immutable receipt snapshots, transaction references, loan policy version and user token version. Existing receipt amounts stay NULL because historical payment values cannot be reconstructed reliably. Upgrade and schema drift checks were exercised on a disposable PostgreSQL clone of the previous schema, including downgrade/re-upgrade. Downgrade refuses once financial transactions exist.

Back up first, then run `python -m flask --app app db upgrade` and `python -m flask --app app db check` in the server environment. Audit historical inventory: sales, active loans and pending payments now all consume copies. Do not stamp an existing database forward to skip schema changes.
