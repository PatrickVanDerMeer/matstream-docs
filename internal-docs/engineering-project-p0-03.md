# P0-03: controlled issue and revision preservation

Planning/source review only, 14 November 2026. No implementation or live tests.

Agreed disposition: keep P0-03 as validation only. No development is approved or assumed; add a fix only if a validation check demonstrates a failure.

## Exists and can be configured

Document Control seeds Controlled Document, Transmittal, Submittal and Document Issue Line categories; outgoing/incoming lifecycles; revision/numbering configuration; package/line response fields; Uses tabs, worklists and print templates. The outgoing Ready to Issue → Issued transition attaches Pin Document Package Contents.

The action reads package contents, rejects empty packages and unresolved document references, and pins line DefinitionLinks to their resolved document versions. Multi-line pinning refuses a partial pin result. This establishes document-link pinning, not proof that every later package edit is prevented or that issue-state changes and pinning always commit as one operation.

Administrator controls already include category properties, display tabs/allowed document categories, revision/numbering schemes, lifecycle transitions, criteria, permissions, attached actions, templates and version/retention configuration. Use these rather than add hardcoded drawing numbers, purpose labels or department rules.

## What is actually missing or unverified

1. **Drawing-model fit:** the sample attaches PDFs as Drawing entities. Verify whether these can be used directly as Document Issue Line definitions in the installed Document Control configuration, or whether a controlled-document category/assignment is needed. Do not assume every PDF attachment must be duplicated.
2. **Complete historical baseline:** confirm issued package membership, line metadata, definition-version links and attached file content remain historically resolvable after edits, revisions and reissue. Pinning the drawing link alone is not a blanket immutability guarantee.
3. **Issue enforcement/failure:** verify incomplete packages are blocked, permissions apply server-side, and pinning failure cannot leave a misleading successfully issued package. Existing run audit is not proof of transition rollback; inspect actual behavior.
4. **Retention:** verify referenced document/package versions and files cannot disappear under the configured retention/purge process. Determine existing protections before proposing development.
5. **Print/open consistency:** confirm internal opening and printed transmittals resolve the same pinned revisions after later drawing updates.

These are targeted validation gaps. No new document-control engine is justified by source review alone. Any failed check becomes a concrete development item, reusing existing lifecycle/version/retention mechanisms with administrator-configurable policy.

## Boundary with other P0 items

Client portal resolution/approval UI is P0-04. Client approval versus production authority and required build evidence are P0-05. They depend on P0-03's trustworthy baseline but should not be counted as missing pinning functionality.

## Acceptance scenario

Create a package containing ITM-00000 and ITM-00006 at revision 0, issue it, then create later drawing revisions and a second package. Original opening/printing must still resolve revision 0; the new issue resolves the selected later versions. Attempt unauthorised edits, an empty/unresolved issue, failed pinning and retention/purge. Record actual state/history and ensure no success claim hides failure. Preserve all expected evidence under the configured policy.

## Sources

`MatStream.Web.Manage/Features/Setup/Templates/Parts/DocumentControlPart.cs`; `EngineeringSampleCatalogue.cs`; `MatStream.API/MatStream.Application/Services/Tenant/FeatureAutomationDispatcher.cs`, HandlePinDocumentPackageContentsAsync; document-control, revisions and administrator automation help.
