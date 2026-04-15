# [Feature Name]

## Overview
_What this feature does and why it exists in MatStream. 1-3 sentences._

## User Guide

### [Action 1, e.g. Creating a Category]
1. Step one
2. Step two
3. Step three

### [Action 2, e.g. Editing a Category]
1. Step one
2. Step two

> **Note:** Any important gotchas or tips for the user.

## Business Rules
_List any rules that govern this feature's behavior:_

- Rule 1 (e.g. A category must have a unique name within a workspace)
- Rule 2
- Rule 3

## Permissions
_Which workspace group permissions affect this feature:_

| Permission | Effect |
|---|---|
| View | Can see the entity/item |
| Edit | Can modify fields |
| ... | ... |

## Technical Reference

### Key Files
| Layer | File | Purpose |
|---|---|---|
| API Controller | `EntityDefinitionController.cs` | Handles HTTP requests |
| App Service | `EntityService.cs` | Business logic |
| Repository | `SqlEntityRepository.cs` | DB access |
| Frontend Service | `EntityService.cs` (Blazor) | HTTP client |
| UI Component | `EntityDefinitionEditDialog.razor` | Edit dialog |
| DTO | `EntityUpsertDto.cs` | API contract |

### Database Tables
| Table | Purpose |
|---|---|
| `tbl_Entity` | Entity header row |
| `tbl_EntityVer` | Versioned entity data |

### Key DB Views
| View | Purpose |
|---|---|
| `vw_EntityCurrent` | Latest version of each entity |

### API Endpoints
| Method | Route | Description |
|---|---|---|
| `GET` | `/api/wid/{wid}/entities/definitions/{id}` | Get entity by ID |
| `POST` | `/api/wid/{wid}/entities/definitions` | Create entity |
| `PUT` | `/api/wid/{wid}/entities/definitions/{id}` | Update entity |

## Known Limitations
_Things that are not yet implemented or partially working:_

- Limitation 1
- Limitation 2

## Planned Improvements
_Things on the roadmap:_

- Improvement 1
- Improvement 2

## Changelog
| Date | Change |
|---|---|
| 2026-04-14 | Fixed: lifecycle/revision fields no longer clobbered on edit save |