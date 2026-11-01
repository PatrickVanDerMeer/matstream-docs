# AI Assistant (MatStream AI)

MatStream AI answers questions about how to use and configure MatStream using the published help documentation. It can receive the name of the screen you are using to help select relevant guidance. It does not query or change workspace records or configuration.

## Opening the assistant

The AI button requires the `ai.use` grant and AI must be enabled on the server. The system Administrators group has every grant; ordinary role groups need this grant assigned deliberately by an administrator.

Click the floating **MatStream AI** button to open the chat panel. Ask a question and press Enter or Send. Close and reopen the panel to continue while the same panel instance remains loaded. Use **Clear conversation** to start again. Do not rely on the conversation surviving a browser reload or a new session.

## What the assistant can answer

Ask how to perform a task, configure a feature, or understand a message. For example:

- "How do I attach an automation to a lifecycle transition?"
- "What is the difference between a version and a revision?"
- "How do I search text inside a Word attachment?"
- "How do I save long text as a reusable template?"

The assistant retrieves relevant sections of the help and uses those sections to answer. It cannot inspect your actual categories, property definitions, users, entity data, stock balances or orders. For workspace-specific troubleshooting, describe the relevant settings or message in your question.

## Screen context

The application can supply the current help page and component name. When supplied, a hint such as *I know you are working in Import Definitions* appears in the empty chat. This context helps find the right documentation; it does not give the assistant access to the screen's records or configuration values.

## Sources and verification

When the answer cites retrieved documentation, **Sources** contains links to the corresponding pages on MatStream Help. Sources appear only when the answer includes a citation; an answer without citations may have no Sources section. Open the source to check prerequisites, settings and limitations before following instructions.

## Workspace data and actions

Questions such as "How many parts are in our workspace?" or "Who approved this order?" are workspace data queries. These are not supported by the current assistant. Use the appropriate Library, Explorer, History or feature view to inspect records. The assistant cannot save settings, execute scripts, change lifecycle states or perform other actions on your behalf.

## Documentation freshness and limitations

Help answers depend on the documentation version configured on the server. A local documentation edit is not immediately available to the assistant: the configured GitHub branch must receive the change and the server's documentation cache must refresh. Website publishing must also complete for source links to show the same content.

The assistant may not find every relevant section. If the help does not establish an answer, supply more detail or consult an administrator; do not treat an inferred capability as a supported feature. New local code may also be newer than the deployed application.

## Related

- [Quick start](../getting-started/quick-start.md)
- [Configuration overview](../admin/configuration.md)
- [Automations](automations.md)
