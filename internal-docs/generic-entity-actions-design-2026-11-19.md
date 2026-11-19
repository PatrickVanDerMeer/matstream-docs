# Removed category action engine experiment

The extra category action engine was removed at the user's request. It expanded the original task beyond replacing six production/Engineering tables with existing MatStream storage.

The executor, API endpoint, scheduler, scripting command, shared action DTO, editor and engine-specific tests are removed. No new actions can run through that engine. Any previously saved engine definitions are disabled; their JSON, snapshots, ledger rows, stock balances and checkpoints are preserved. Historical records remain readable and protected.

The current scope, retained architecture, migration order and validation are documented in [Production table storage consolidation](production-table-storage-consolidation-2026-11-19.md). The earlier experiment's validation logs remain historical artifacts; they do not describe an available product feature.
