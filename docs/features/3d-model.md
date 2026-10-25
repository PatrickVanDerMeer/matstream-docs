# 3D Model viewer

The **3D Model** tab shows an entity's geometry in the browser: rotate it, look at it from standard views, and measure between points — without opening a CAD system and without downloading anything.

The model is a **viewer file, not a document**. It is attached to the entity version directly: it does not appear in the Library, it has no category, no lifecycle and no revision of its own, and replacing it does not create a new version. STEP or native CAD files remain the engineering deliverables; the 3D model is a lightweight picture of them.

---

## What the tab shows

The tab renders a **GLB** (binary glTF) mesh. For any entity version it looks, in order, for:

1. The model attached to **this version** — or, if this version has none, the model of the nearest earlier version *as long as the attached file has not changed since*. A Part that gained a new version because a property was edited keeps its model; a Document whose CAD file was replaced does not show the old geometry.
2. The version's **attached file itself**, when that file is already a `.glb` or `.gltf`.
3. A **linked document's** model — for workspaces where a Part carries no file of its own and the CAD lives on Documents or Drawings linked from its Documents tab.

When none of these exist the tab explains how to add one.

---

## Navigating

| Action | How |
|---|---|
| Rotate | Drag with the left mouse button |
| Pan | Drag with the right mouse button (or Shift + drag) |
| Zoom | Scroll wheel |
| **Fit** | Frames the whole model |
| **Views** | Original, Isometric, Front, Back, Top, Bottom, Left, Right |
| **X / Y / Z** | Turn the model 90° around the selected world axis; repeat to step through orientations |
| **Save** | Save the orientation in the GLB for everyone opening this model |
| **Rotate left / right** | Turn the model 90° in screen space while keeping navigation upright |
| **Show / Hide edges** | Draws feature edges over the surfaces — usually clearer for machined parts |

To lay a vertically exported model horizontally, try **Z** or **X**, then orbit to inspect it. Axis turns return the camera to isometric and fit the model. They rotate the model about its bounds centre while keeping the navigation up direction fixed at Y. Four turns around the same axis restore that axis rotation.

Axis and Rotate left/right adjustments are temporary until **Save** is clicked. Save writes a scene-root transformation into the GLB, preserving geometry and other model data, then replaces the latest Viewer3D asset without creating an entity revision. Reopening and downloading that saved model use the new orientation. This is shared model data, not a user/browser preference. Save is available after an orientation change in editable latest-version views; history/read-only views cannot save. If the displayed model was inherited or came from a linked document, saving creates an explicit Viewer3D asset on this entity's latest version; it does not rewrite other entities' or older versions' files. Orbit, pan, and zoom move the camera and are not included in the saved model orientation. A later replacement upload or CAD publish can replace the saved orientation. Changing orientation (including Rotate left/right) clears measurements because their points and ΔX/ΔY/ΔZ are expressed in viewer coordinates. Standard views and new measurements use the adjusted orientation; lengths are preserved by rotation.

The readout in the bottom-left corner shows the model's overall size (X × Y × Z), the number of bodies and the triangle count.

---

## Measuring

1. Click **Measure**. The cursor becomes a crosshair and hovering shows where the pick will land.
2. Click a first point, then a second. The distance is drawn on the model and listed in the readout with its ΔX, ΔY and ΔZ components.
3. Repeat for more measurements. The ruler icon toggles measuring; its tooltip describes the action. Right-click or `Esc` cancels a half-placed measurement. Changing model orientation or reloading the model clears completed measurements.

Picks **snap to the nearest vertex** when one is close, so clicking near a corner measures from the corner — the usual intent on a CAD mesh. Dragging while in Measure mode still rotates; only a click that stays put places a point.

The unit menu (**mm** by default) switches between millimetres, centimetres, metres and inches for every figure on screen.

!!! note "Measurements are taken on the mesh"
    A GLB is a triangle mesh, not exact geometry. A distance between two corners is exact; the "diameter" of a hole is the distance between two polygon vertices, which sits a fraction of a percent inside the true circle. Use the tab for lengths, clearances and sanity checks — not for values you would put on a drawing.

---

## Adding or replacing a model

Two ways, both attaching to the entity's **latest version**:

- **From Inventor** — run the `MatStream_Publish3DModel` iLogic rule on the open part or assembly. It exports the GLB and hands it to MatStreamConnect, which attaches it to the entity with the same Name. See [Inventor 3D model](../integrations/inventor-3d-model.md).
- **From the tab** — **Upload GLB…** and choose a `.glb` file exported from any CAD system. **Remove** takes the model off the latest version.

Uploading does not require the entity to be in a modifiable lifecycle state: a viewer file is presentation, so attaching one to a released Part neither needs nor causes a revision.

---

## Versions and purging

Each version keeps the model that was attached while it was current, so the **History** tab shows a released revision as it looked then, and the carry-forward rule above gives property-only versions the model they inherited.

Purging a version does not lose a model that later versions were inheriting: the model moves to the next surviving version that carries the same file. A model nothing else was using is released with the version and its file is reclaimed by the stored-file sweep once nothing references it.

---

## Supported input

| Source | Notes |
|---|---|
| Inventor parts and assemblies | Via the iLogic rule; needs Inventor 2023 or newer (built-in glTF export) |
| Any `.glb` / `.gltf` | Via **Upload GLB…** — most CAD systems export glTF directly (SolidWorks, Fusion, FreeCAD, Blender) |
| STEP, IGES, native CAD | Not rendered directly; export a GLB from the CAD system first |

Models are expected in **metres**, as the glTF specification requires; Inventor's exporter does this automatically. Cosmetic threads in Inventor are not geometry and therefore do not appear in the model.

---

## Related

- [Display Tabs](../admin/display-tabs.md) — assign the 3D Model tab to a category
- [Inventor 3D model](../integrations/inventor-3d-model.md) — the iLogic rule and the `--publish-model` command
- [Files & documents](../entities/files.md) — how real CAD files and drawings are stored

### Restoring the original orientation

Choose **Views → Original** to reset temporary turns and saved MatStream orientation transforms and return to the initial isometric camera position with the whole model fitted. Original CAD node transforms are preserved. This also clears measurements. Click the Save icon afterward to persist the restored orientation for everyone. Selecting Original alone changes only the current viewer. This applies to rotations saved by MatStream; a replacement GLB with a different exported orientation becomes the new imported baseline.