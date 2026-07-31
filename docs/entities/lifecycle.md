# Lifecycle

The lifecycle of an entity defines the stages it passes through from 
initial creation to formal release and eventual retirement. MatStream's 
lifecycle system is fully configurable — your administrator designs the 
states, transitions, and permissions that match your engineering process.

!!! tip "Coming from Autodesk Vault?"
    Lifecycle states in MatStream are equivalent to Vault's lifecycle 
    states. The key difference is that MatStream gives administrators 
    full control over states, transitions, and who can trigger them — 
    without needing a separate lifecycle editor tool.

## States

A lifecycle is made up of a set of named **states**. Each state 
represents a stage in the entity's journey. Common states include:

| State | Typical meaning |
|---|---|
| **Draft** | The entity is being created or edited. Not yet ready for review. |
| **In review** | The entity has been submitted for approval and is awaiting a decision. |
| **Released** | The entity has been formally approved and released. It is the official current revision. |
| **Obsolete** | The entity has been retired and is no longer in active use. |

!!! note "Your states may differ"
    The exact states in your workspace depend on how your administrator 
    has configured the lifecycle definition for each category. The 
    examples above are illustrative, not fixed.

## Transitions

A **transition** is a permitted move from one state to another. Not 
every state change is allowed — your administrator explicitly configures 
which transitions exist.

For example:

- Draft → In review ✅
- Draft → Released ❌ (not permitted — must go through review first)
- In review → Released ✅
- In review → Draft ✅ (sent back for rework)
- Released → Obsolete ✅

This controlled transition model prevents accidental or unauthorised 
state changes that could compromise your approval process.

## Changing state

Select one or more entities and choose **Change State**. The dialog lists 
each entity with its current state and lets you pick the new one.

- The **New State** dropdown offers only states you are permitted to move to 
  from where the entity is now. If a state you expected is missing, either no 
  transition is configured for it or your group does not have permission.
- **Assign to** optionally creates a task for the person who should act next.
- **Comment** is recorded against the change.

You can change several entities at once — use the **new state** dropdown at 
the top to apply the same state to all of them.

!!! note "Changing state usually creates a new version"
    Depending on the category's version policy, moving an entity to a new 
    state creates a new version rather than editing the current one, with the 
    reason recorded as the state change. This is what keeps the history 
    complete.

### Switching to a different lifecycle

The **lifecycle** dropdown at the top of the Change State dialog moves an 
entity onto an entirely different lifecycle — for example handing a design 
from an engineering process to a procurement one. The entity starts in the 
new lifecycle's initial state.

Only switches your administrator has configured for the category are offered, 
and a switch can optionally create a task for whoever picks the work up next.

## Who can trigger a transition?

Each transition can be restricted to specific user groups. For example:

- Any engineer can submit an entity from **Draft → In review**
- Only the Quality team can approve **In review → Released**
- Only administrators can move an entity to **Obsolete**

If a state you expect is not offered, your user group does not have 
permission for that transition. Contact your workspace administrator if you 
believe this is incorrect.

!!! note "Conditions are not checked yet"
    Your administrator can describe conditions on a transition — such as 
    "all linked items must be Released" — but MatStream does not currently 
    enforce them. A transition will succeed even if its conditions are not 
    met. Permissions are the only enforced control today.

## Lifecycle state permissions

The lifecycle state an entity is in can also control what users are 
allowed to do with it:

| Permission | Example |
|---|---|
| **Read** | Only certain groups can view a Released entity |
| **Modify** | A Released entity is read-only to most users |
| **Delete** | Only administrators can delete entities in any state |

This means the same user group may have different rights on the same 
entity depending on its current state — for example, an engineer can 
edit a Draft entity but not a Released one.

## Transition history

Every state change is permanently recorded in the entity's transition 
history, capturing:

- Who triggered the transition
- Which state the entity moved from and to
- The date and time of the transition

This gives you a complete, tamper-proof audit trail of every approval 
and rejection the entity has passed through.

## Lifecycle and revisions

Lifecycle states and revisions work closely together. A typical pattern is:

1. Entity starts in **Draft** at revision **A**
2. It moves through review and reaches **Released** — still revision **A**
3. A change is needed — a new revision **B** is created and the entity 
   returns to **Draft**
4. Revision **B** moves through the lifecycle independently of revision **A**
5. When revision **B** is released, revision **A** may be automatically 
   moved to **Obsolete**

Your administrator configures exactly how revisions and lifecycle states 
interact for each category.

## Lifecycle definitions

A lifecycle definition is a reusable configuration that can be assigned 
to multiple categories. This means your administrator can define a 
single **Standard Engineering Workflow** and apply it to all mechanical 
part categories, rather than configuring each category separately.

If you need a different lifecycle for a specific category — for example, 
a simpler two-state workflow for reference documents — your administrator 
can create a separate lifecycle definition for that purpose.

!!! tip "Requesting lifecycle changes"
    If your current lifecycle states or transitions do not match your 
    team's actual process, speak to your workspace administrator. 
    Lifecycle definitions can be adjusted without affecting entities 
    that have already been released.

## Next steps

- [Revisions](revisions.md) — understand how revision labels are 
  assigned and advanced
- [Properties](properties.md) — understand how property editing is 
  controlled by lifecycle state
- [Overview](overview.md) — back to the entities overview