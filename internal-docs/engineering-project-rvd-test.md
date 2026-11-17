# Project-specific RVD test — 15 November 2026

Local workspace 108 (cwt3qp), isolated project 16002. No external sharing/email/deployment.

| Item | Project position | MR | PO / line | Result |
|---|---:|---:|---|---|
| Motor | 16003 | 16009 | PO-2026.0003 (16030) / 16031 | Four included: add GA Drawing, exclude FAT with reason |
| Pump | 16014 | 16022 | PO-2026.0004 (16036) / 16037 | Six included: add Pressure Test, exclude spare-parts list with reason |

Browser verified full MR summary, successful two-order creation without warnings, both PO checklists, and editable administrator policy. Screenshots 145–149 are referenced in the public workflow guide.

Native repository/service checks verify exact definition-version pins and Association provenance, no source files copied, repeat synchronization/idempotent copy, and unchanged original Library/PO counts (motor four, pump six). A later motor pressure-test addition does not enter the frozen MR/PO. A temporary file pointer survives synchronization; this probe rolls back. Missing exclusion reason rejects MR creation and rolls back all records. A fresh MR stores a compact snapshot without redundant upload placeholders; repeated PO copy produces exactly six rows. Copied baseline metadata from another position does not suppress defaults in a new position. Transient probes roll back completely.

Policy mappings and defaults live in the administrator-editable ProjectRvd automation parameters and normal property/category configuration. Seed guards preserve installed configuration. No new schema or bigint keys. Installed policy missing/disabled or invalid mappings block rather than silently falling back.

Limitations: no retrospective amendment/refresh command for frozen MRs or POs; no Quality acceptance, supplier upload or payment approval test in this slice. Early local fixtures 16009/16022 were created before redundant MR placeholders were removed; the fresh transactional test verifies final behavior.

Further regression and supplier-upload findings: [continued local tests](engineering-project-continued-tests-2026-11-15.md).
