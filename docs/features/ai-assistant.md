# AI Assistant (MatStream AI)

MatStream AI answers questions about how to use and configure MatStream using the published help documentation. It can receive the name of the screen you are using to help select relevant guidance. With a workspace selected, it can also search readable entity metadata and version history when the workspace administrator enables data queries. It cannot change workspace records or configuration.

## Opening the assistant

The AI button requires the `ai.use` grant and AI must be enabled on the server. The system Administrators group has every grant; ordinary role groups need this grant assigned deliberately by an administrator.

Click the floating **MatStream AI** button to open the chat panel. Ask a question and press Enter or Send. Close and reopen the panel to continue while the same panel instance remains loaded. Use **Clear conversation** to start again. Do not rely on the conversation surviving a browser reload or a new session.

## What the assistant can answer

Ask how to perform a task, configure a feature, or understand a message. For example:

- "How do I attach an automation to a lifecycle transition?"
- "What is the difference between a version and a revision?"
- "How do I search text inside a Word attachment?"
- "How do I save long text as a reusable template?"

The assistant retrieves relevant sections of the help and uses those sections to answer. Documentation answers use the help pages. Workspace data answers use a separate read-only query path described below; they do not inspect configuration definitions, stock balances, or document contents.

## Screen context

The application can supply the current help page and component name. When supplied, a hint such as *I know you are working in Import Definitions* appears in the empty chat. This context helps find the right documentation. A current entity ID can also identify the entity for a version-history query; the server still checks your access before reading its records.

## Sources and verification

When the answer cites retrieved documentation, **Sources** contains links to the corresponding pages on MatStream Help. Sources appear only when the answer includes a citation; an answer without citations may have no Sources section. Open documentation sources to check prerequisites, settings and limitations before following instructions. Workspace answers show entity IDs in Sources so you can find the records in MatStream.

## Workspace data and actions

With a workspace selected, you can ask questions such as:

- "Show me released parts."
- "Find projects named gas dome."
- "Show the version history of entity 123."
- "Who created this entity?" when the current screen supplies its entity ID.

The server translates the question into an allowed search or history query and reads through the normal entity services. You need both `ai.use` and entity-read permission; entity access restrictions still apply. Only permitted metadata is supplied to the configured AI provider: names, categories, lifecycle states, versions, revisions, version authors and dates, and version reasons. Custom property values and file contents are excluded.

Workspace administrators can configure **Allow AI workspace data queries** and **Maximum records per AI answer** under **Workspace Defaults**. Data queries default to enabled for users who already have the required grants. The default result limit is 25, configurable from 1 to 100. Existing administrator choices survive setup and migration reruns. Disable data queries if workspace records should not be sent to the configured AI provider; documentation questions remain available.

Answers cover the returned readable records, not a guaranteed workspace-wide total. A limited result includes a notice; narrow the name, category, or lifecycle state for a more specific result. An empty result does not prove that records do not exist elsewhere in the workspace.

Version authors are not proof of approval. Approval audit logs, stock balances, task planning, custom-property analysis, document contents, and date-range aggregates are outside this query path. Use the corresponding feature view to inspect those records. The assistant cannot save settings, execute scripts, change lifecycle states or perform other actions on your behalf.

## Documentation freshness and limitations

Help answers depend on the documentation version configured on the server. A local documentation edit is not immediately available to the assistant: the configured GitHub branch must receive the change and the server's documentation cache must refresh. Website publishing must also complete for source links to show the same content.

The assistant may not find every relevant section. If the help does not establish an answer, supply more detail or consult an administrator; do not treat an inferred capability as a supported feature. New local code may also be newer than the deployed application.

## Related

- [Quick start](../getting-started/quick-start.md)
- [Configuration overview](../admin/configuration.md)
- [Automations](automations.md)
