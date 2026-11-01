# Text Templates

Long-text templates provide reusable starting text for long-text property fields. Open a supported long-text field's **Edit Text** dialog to choose a workspace template or save one. These templates are separate from [Word and Excel print templates](print-templates-admin.md).

## Apply a template to a field

1. Open an editable long-text property.
2. Choose a named template from the picker, or **Manual Text** to type freely.
3. Review and edit the text.
4. Click **Apply** to return the text to the field.
5. Save the containing entity/editor to persist the field change.

The field stores the applied text itself, not a live reference to the template. Reusing or changing text in one entity does not update other entities. **Cancel** closes the text dialog without applying the text to the field.

**Switch to manual text** restores the manual text held before the template was selected; it does not necessarily keep the currently displayed template text.

## Save current text as a workspace template

1. Enter the reusable text in the long-text editor.
2. Choose **Actions → Save current text as template**.
3. In **Save as workspace template**, enter a unique name of 1–100 characters.
4. Save the template. It is immediately available in the workspace template picker.
5. Click **Apply** if you also want to use the text in this field, then save the entity.

Saving a template is a separate operation from applying a field value. Cancelling the naming prompt keeps the editor text. Cancelling the text editor after saving a template does not undo the template creation.

Templates are scoped to the workspace. Reading templates requires entity-read permission; saving a template requires entity-read and entity-update permission. Body text must contain 1–100,000 characters. Duplicate names are rejected: choose a different name. The current API supports listing and creation, rather than a general template edit/delete screen.

## Variables and text preservation

The editor offers **Name**, **State**, **Revision** and **Version** tokens (`{{Name}}`, `{{State}}`, `{{Revision}}`, `{{Version}}`) through **Select variable… → Insert variable**. Insertion appends the selected token to the text. Saved templates preserve whitespace, line breaks and tokens exactly.

The long-text template picker supplies starting text; selecting or saving a template does not itself establish runtime token expansion. Do not assume `{PD_Description}` placeholders, auto-description assignment, notification rendering or export filename generation are provided by this workflow. Use each consuming feature's documented syntax and behavior.

## Availability and troubleshooting

The long-text template workflow requires a compatible UI/API and the long-text template database migration. If templates cannot load, the dialog shows an error and still allows manual text editing. A failed save keeps your editor text available. If the actions are absent, check permissions, whether that field supports templates, and the deployed application version.

## Related

- [Properties](../entities/properties.md)
- [Property definitions](properties.md)
- [Print templates](print-templates-admin.md)
