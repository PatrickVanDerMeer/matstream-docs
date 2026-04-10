# What is MatStream?

MatStream is a cloud-based **Product Lifecycle Management (PLM)** 
platform designed for engineering teams. It sits above your existing 
Product Data Management (PDM) tools — like Autodesk Vault — and adds 
the structured lifecycle control, traceability, and process management 
that PDM tools are not designed to provide.

## PDM vs PLM — what is the difference?

Understanding where MatStream fits starts with understanding the 
difference between PDM and PLM:

| | PDM | PLM |
|---|---|---|
| **Examples** | Autodesk Vault, SolidWorks PDM | MatStream, Autodesk Fusion PLM |
| **Focus** | Managing CAD files and design data | Managing the product lifecycle, structured data, and business processes around those files |
| **Primary users** | Designers and CAD engineers | Engineering, quality, procurement, and management |
| **Key questions answered** | Where is the file? What version is this? Who checked it out? | Is this part approved? Who released it? What assemblies use it? What changed between revisions? |

MatStream does not replace Vault or Inventor. Your designers continue 
working in Inventor and Vault exactly as they do today. MatStream adds 
the PLM layer on top — formal lifecycle states, structured properties, 
BOM traceability, approval workflows, and organisation-wide visibility 
into your product data.

## What does MatStream manage?

MatStream helps your team manage:

- **Parts, assemblies, and documents** — as structured entities with 
  configurable properties, revision history, and lifecycle states
- **Bills of Materials (BOMs)** — the relationships between assemblies 
  and their components, with full where-used traceability
- **Approval workflows** — controlled transitions between lifecycle 
  states, with permission gates at each step
- **Revision history** — a complete, immutable record of every formal 
  release of every item
- **Files** — drawings, specifications, and CAD files attached to 
  specific entity versions
- **Vault data** — parts and assemblies from Autodesk Vault can be 
  synchronised into MatStream automatically via the Vault integration plugin

## Who is MatStream for?

MatStream is built for engineering teams who need to:

- Move beyond file management into structured product lifecycle control
- Maintain a formal approval process for parts and assemblies
- Give non-CAD users (quality, procurement, management) visibility into 
  product data without needing Vault access
- Integrate their existing Vault workflow with a PLM layer without 
  replacing the tools their designers depend on

## Why not just use Autodesk Fusion PLM?

Autodesk Fusion PLM is a capable platform, but it comes with significant 
cost and complexity, and it is tightly coupled to the Autodesk ecosystem. 
MatStream is designed to be:

- **Affordable** — a SaaS model with no expensive on-premise 
  infrastructure or per-seat CAD licences
- **Flexible** — configurable categories, properties, lifecycle states, 
  and numbering schemes that match your process
- **Modern** — accessible from any browser, with no client software to 
  install or maintain
- **Practical** — built by people with firsthand experience of real 
  PLM migrations, not designed in a vacuum

## Core concepts

| Concept | What it is |
|---|---|
| **Workspace** | Your organisation's isolated environment in MatStream, with its own users, data, and configuration |
| **Entity** | Any item you manage — a part, assembly, drawing, or document |
| **Version** | An internal snapshot created every time an entity is saved — the complete audit trail |
| **Revision** | A formally labelled release of an entity (e.g. A, B, C or 1, 2, 3) |
| **Lifecycle state** | Where an entity currently sits in your approval process (e.g. Draft, In Review, Released) |
| **Category** | The type of entity, which defines its properties, display tabs, and lifecycle |
| **Property** | A structured data field on an entity, such as material, weight, or supplier |
| **Numbering scheme** | A configurable template that automatically generates unique entity numbers |

## How MatStream fits into your workflow

1. Your designers work in **Inventor and Vault** as normal — nothing changes 
   for them at the CAD level
2. Vault data is **synchronised into MatStream** automatically via the 
   Vault integration plugin
3. In MatStream, parts and assemblies move through **lifecycle states** — 
   Draft → In Review → Released — with controlled approval transitions
4. **Properties** are captured and structured at the PLM level — material, 
   supplier, lead time, and any other data your process requires
5. **BOM relationships** are tracked with full where-used traceability — 
   so you always know which assemblies are affected by a change to any part
6. Non-CAD users in quality, procurement, and management get **visibility 
   into released product data** without needing Vault access

!!! tip "New to PLM?"
    If you are new to Product Lifecycle Management, the best place to 
    start is the [Quick start](quick-start.md) guide. It walks you 
    through the key concepts with a practical example before you dive 
    into the details.

## Next steps

- [Workspaces](workspaces.md) — understand how workspaces are structured 
  and how users and groups work
- [Quick start](quick-start.md) — get up and running with a practical 
  walkthrough
- [Entities](../entities/overview.md) — dive into the core object model