# AI Assistant (MatStream AI)

MatStream AI is a built-in chat assistant that answers questions about MatStream features, configuration, and your workspace data. It is available on every page in MatStream and is context-aware — it knows which area of the application you are working in.

---

## Opening the assistant

The AI button is offered only when your groups supply the `ai.use` grant. The system
Administrators group has every grant; the Setup Wizard does not give `ai.use` to ordinary role
groups. A workspace administrator must assign it deliberately.

Click the floating **MatStream AI** button (✦ icon) near the lower-right corner. A panel slides in from the right side of the screen without navigating away from your current page.

Close the panel by clicking the ✕ button or clicking outside it. Your conversation is preserved for the session — reopening the panel shows the same conversation thread.

---

## Asking questions

Type your question in the input field at the bottom of the panel and press Enter or click Send. The assistant responds with an answer and, where possible, cites the source documentation or configuration data it used.

**Example questions:**

- "How do I set up a revision scheme for manufacturing parts?"
- "What permissions does a user need to release an entity?"
- "How does the Lucene search index work?"
- "What is the difference between a system template and a workspace template?"
- "How do I trigger an import from iLogic?"

---

## Context awareness

The assistant knows which page and component you are currently using. When you open the panel from the Import Definitions page, it knows you are working on imports. From the Automations editor, it knows you are writing an automation script.

This context is shown as a hint at the top of the empty chat state:

> *I know you are working in **Import Definitions**.*

Use this to ask more specific questions like "What does the key field option do here?" without needing to specify which feature you mean.

---

## Sources

Each assistant response includes a **Sources** section listing the documentation pages or configuration data the answer was drawn from. Click a source to open the relevant documentation.

This helps you verify the answer and learn more about the topic.

---

## What the assistant knows

The assistant has access to:

- All MatStream documentation (the docs you are reading now)
- Workspace configuration: your categories, properties, lifecycle definitions, numbering schemes, and import definitions
- General MatStream feature knowledge

The assistant does **not** have access to:

- Entity data (it cannot read your parts, assemblies, or documents)
- User personal data
- External systems

---

## Limitations

- The assistant may occasionally give incomplete answers for very new or uncommonly used features.
- Complex multi-step configuration questions may need follow-up clarification.
- If an answer seems wrong, verify it against the linked source documentation and contact support if needed.

---

## Related

- [Getting started](../getting-started/quick-start.md) — workspace setup guide
- [Configuration overview](../admin/configuration.md) — all admin configuration areas
