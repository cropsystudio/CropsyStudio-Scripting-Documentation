# Devlog

## 2026-04-18

### Phase 1

- Reframed the repository into a native C++ engine/editor project.
- Added architecture, dependency, roadmap, and workflow documentation.
- Created engine/editor/runtime/assets/docs/third-party/example-game folder structure.
- Logged the current environment blocker: no accessible compiler or CMake toolchain on this machine.

### Planned Next Validation

- Configure the CMake project.
- Build the editor executable.
- Launch the editor and verify docked UI, viewport, and play-mode shell.

### Phase 2-8 Bootstrap Implementation

- Added a modular `AuroraEngine` static library with core utilities, scene/component storage, JSON scene serialization, asset scanning, local project catalog service, simple physics, and render systems.
- Added a dockable `CropsyStudio` desktop application with menu bar, toolbar, hierarchy, inspector, content browser, console, project browser, viewport, and status bar.
- Added a `AuroraRuntime` play-session layer that clones the edit scene for play mode and keeps runtime state separate from authoring state.
- Added sample shaders, a sample scene JSON file, and local project browser metadata.
- Added a minimal self-contained OpenGL loader in `ThirdParty/glad` to keep the repository relocatable without requiring Python-based GLAD generation.

## 2026-04-20

### Group 3 UI Basics

- Added simple scene-based UI components for `ScreenGui`, `Frame`, `TextLabel`, and `ImageLabel`.
- Added a lightweight `UIRenderer` that builds ordered 2D overlay draw commands from scene UI components.
- Integrated UI rendering directly into the viewport after the 3D scene so UI objects can be seen in edit mode and play mode without redesigning the editor.
- Added UI selection in the viewport, basic UI selection outline, UI serialization, UI duplication support, and UI hierarchy creation entries.
- Extended the inspector so UI objects can edit position, size, anchor point, rotation, z-index, frame colors, text values, and image path placeholders.

### Group 4 Beginner-Friendly Behavior Hooks

- Added a small built-in C++ behavior system with `Behavior`, `BehaviorRunner`, and a serializable `BehaviorComponent`.
- Added the first built-in example behavior: `SpinBehavior`.
- Integrated behavior startup and update into play mode only, so edit mode stays unchanged and safe.
- Added inspector support for assigning a behavior name and enabling or disabling the behavior component.

### Validation

- Rebuilt `CropsyStudio` successfully in Visual Studio 2026.
- Launched the editor successfully after the Group 3 and Group 4 changes.

## 2026-04-22

### Scripting And Project Workflow Pass

- Added an engine-side `CommandScriptRuntime` so command script parsing and execution no longer live only in the editor layer.
- Kept the beginner-friendly `.crop` command language as the main scripting path for now instead of jumping to a heavier Lua integration.
- Refactored the editor `ScriptEngine` to use the shared engine runtime for validation and execution.
- Extended `ScriptComponent` with `enabled` and `runMode` so scripts can be attached to normal scene objects and configured safely.
- Integrated attached script execution into play mode with `OnPlayStart` support.
- Added `FolderComponent` and editor creation support for beginner-friendly hierarchy organization.
- Extended asset scanning with typed asset classification so the content browser can show safer type labels.
- Added a simple import pipeline that copies external files into project-relative asset folders based on asset type.
- Improved inspector script workflow so objects can add, remove, create, attach, open, and configure script components without changing the editor layout.
- Fixed a scripting workflow bug where deleting an object would also delete the backing script file from disk.

### Validation

- Rebuilt `CropsyStudio` successfully after the scripting and workflow changes.
- Launched the editor successfully after the new engine/editor/runtime integration.

### Script System Upgrade

- Upgraded scene script components from a single attached script to a list of script asset references per object.
- Added `On Play Update` script execution mode in addition to `Manual` and `On Play Start`.
- Added engine-side support for the `dt` token inside numeric script commands so update scripts can move or rotate in a frame-rate-aware way.
- Improved the inspector so objects can manage multiple scripts, attach existing scripts, reuse the currently open script, and remove individual script slots.
- Exposed script assets in the content browser and wired them to open directly in the script editor.
- Added a quick `Attach To Selected` workflow from the script editor.
- Fixed deletion behavior so deleting an entity no longer deletes the shared script file from disk.
- Added `Folder` object creation to make beginner project organization easier without changing the editor layout.

### Script Parameters And Editor Validation Pass

- Added per-script-slot parameters so attached `.crop` scripts can read simple inspector-defined values like `$speed` or `$targetName`.
- Extended script serialization and play-mode runtime setup so script parameters save, load, and execute consistently in edit mode and play mode.
- Upgraded command-script validation to return more specific error messages, including missing parameter names, bad numeric arguments, unsupported commands, and missing required components.
- Added timed script auto-checking in the script editor so open scripts revalidate automatically about once per second after edits.
- Added stronger script autocomplete by combining built-in commands with scene object names and currently attached script parameters.
- Added red diagnostic underlines directly in the editor pane as well as the preview pane so script errors are easier to spot.
- Improved global studio text scaling by scaling ImGui layout sizes together with font scale, which reduces clipping at larger text sizes like `150%`.

### Validation

- Rebuilt `CropsyStudio` successfully after the script parameters, autocomplete, diagnostics, and UI scaling changes.
- Launched the editor successfully after this pass.

### Script Editor Autocomplete Popup

- Reworked script autocomplete from a docked list into a floating popup anchored near the text cursor.
- Added richer suggestion metadata so entries now show a kind/type label, a short usage line, and a short definition.
- Improved `$parameter` token detection so script-slot parameters autocomplete more reliably.
- Updated keyboard behavior so `Tab` accepts the currently selected suggestion in the popup.

## 2026-04-23

### Native Script Editor Replacement

- Replaced the fragile ImGui multiline script editing path with a native Scintilla editor control embedded inside the existing Script Editor panel.
- Vendored the official Scintilla source into `ThirdParty/ScintillaSrc/scintilla` and built it as a local static library so the project stays self-contained.
- Kept the current editor layout intact by hosting the Scintilla child window inside the same docked Script Editor panel instead of redesigning the UI.
- Moved script text editing, undo/redo, cursor focus, zoom hotkeys, inline diagnostics, and autocomplete acceptance onto the Scintilla control.
- Implemented simple container-style syntax coloring for the current `.crop` command language so comments, commands, numbers, strings, and `$parameters` can be styled by a real editor widget.

### Script Editor Basics Pass

- Added selection-aware `Tab` and `Shift+Tab` indentation through Scintilla so script blocks can be indented or outdented naturally.
- Added `Ctrl+/` line comment toggling and `Ctrl+D` duplicate line or duplicate selection support inside the script editor.
- Added a simple find/replace strip inside the Script Editor panel with `Find Next`, `Replace`, `Replace All`, and optional match-case searching.
- Upgraded autocomplete suggestions so common commands now insert useful starter snippets like `move 0 0 0` instead of only the bare command name.
- Extended script diagnostics from a single marked line to multiple marked lines so more than one script error can be highlighted at the same time.
- Updated the script panel summary area so it lists several current validation errors instead of only the first one.

### Toolbar Text Scaling Fix

- Fixed the top toolbar so it sizes itself from the active font scale instead of relying on small fixed-width controls.
- Scaled the transform tool buttons, scale mode buttons, and numeric snap input width so `150%` text scale no longer clips those labels as easily.
- Forced the toolbar window height to match the current frame/text padding so larger UI scales do not leave the row vertically cut off inside the saved docked window.

### Large UI Scale Fix

- Reworked the global editor scale path so ImGui style sizing now resets from a clean base style before each scale change instead of compounding old scaled values.
- Rebuilt the ImGui font atlas at the requested UI size instead of relying on extreme `FontGlobalScale` values, which makes very large scales like `300%` render more consistently.
- Raised the minimum docked window size based on the active UI scale so panels are less likely to collapse into unusable widths when the text is very large.

### Studio Text Scale Safety Cap

- Removed `300%` from the Studio text scale options and capped the editor UI scale at `200%` to avoid the crash path the user hit at the extreme size.
- Added preference clamping on load and save so stale saved values above `200%` are corrected automatically before the UI is rebuilt.

### Console Zoom Input Pass

- Improved console zoom rendering by scaling the console text field padding together with the console text scale so the read-only log view behaves more like the script editor.
- Added `Ctrl + mouse wheel` support for panel zoom changes when the script editor or console has focus.

### Panel Zoom Regression Fix

- Fixed the wheel-zoom regression by stopping the raw GLFW scroll callback from swallowing wheel input before panels could use it normally.
- Moved `Ctrl + mouse wheel` panel zoom handling into the per-frame shortcut pass so regular scrolling still works while focused panels can still zoom intentionally.
- Made the panel zoom shortcut path use the native Scintilla focus state for the script editor so `Ctrl +/-` remains reliable there while the console keeps using the shared shortcut path.

### Scroll Callback Restore Pass

- Restored ImGui's built-in GLFW scroll callback inside the editor scroll hook so console and other ImGui panels receive wheel input again.
- Moved console zoom shortcut handling into the console panel draw path so `Ctrl +/-` and `Ctrl + mouse wheel` use the current frame's real focus state instead of stale focus flags from the previous frame.
- Kept the native Scintilla editor on its own keyboard zoom path while adding a script-panel wheel zoom fallback through the live panel state.

### Native Panel Zoom Ownership Fix

- Fixed a script editor bug where native Scintilla zoom requests were being consumed before the panel had marked itself focused, which caused the shared zoom helper to quietly do nothing.
- Switched both the script editor and the console to update their own zoom preferences directly inside their panel code instead of routing those changes through the shared panel zoom helper.
- This keeps panel zoom decisions local to the panel that actually owns the current focus and avoids depending on stale cross-panel focus flags.

### Native Console View Pass

- Replaced the console's ImGui multiline text box with a native read-only Scintilla view so scrolling and zoom behavior use a real text control instead of widget-level shortcut glue.
- Upgraded the script editor's Scintilla child window so `Ctrl +/-` and `Ctrl + mouse wheel` change zoom inside the native control immediately, then sync the preference back into the editor settings.
- Kept the existing editor layout intact by hosting both native controls inside their current ImGui panels instead of redesigning the UI.

### Native Console Startup Crash Fix

- Fixed a startup regression where the new native console view registered Scintilla first and the existing script editor then failed its own startup path by trying to register the same Scintilla window classes again.
- Added a shared "already registered" check in both native Scintilla wrappers so multiple native text controls can coexist safely in the editor.

### Native Console Control Tightening

- Tightened the native console host rectangle so the Scintilla console view sits inside a direct host region instead of relying on a looser child-window placeholder.
- Fixed the native console zoom order so zoom changes are consumed and written back to preferences before the panel reapplies any saved zoom value on the next frame.
- Enabled explicit Scintilla scroll bars and small left/right margins for the console view to make its scrolling behavior more predictable in long logs.

### Script Token Palette Expansion

- Expanded the native Scintilla script editor from a small generic palette into more specific token groups: transform commands, output commands, color commands, selection commands, name commands, parameters, entity names, booleans, keywords, and the `dt` runtime token.
- Updated the scripting documentation so the same token categories have matching color meanings in the HTML docs, including a new script color legend section.

### Doc Hover Card And Status Window Cleanup

- Widened the documentation hover cards and tightened their example code block sizing so hover examples are less likely to look cut off or leave awkward empty space.
- Locked the editor `Status` window into an always-auto-sized, non-resizable overlay so the FPS/status panel no longer keeps unnecessary extra height and is less likely to enter a bad resize state.

### Doc Hover Token Styling Pass

- Reworked the documentation hover windows and function popup windows so example snippets use bright text-only syntax colors instead of boxed type-chip pills inside code.
- Added more specific hover-window coloring for function names, type names, strings, numbers, keywords, and runtime script tokens so the popup examples read more like real code and less like UI tags.

### Runtime Log Safety Pass

- Added a hard cap to the in-memory log history so runaway runtime or script logging cannot grow the editor log buffer forever during play mode.
- Hardened the native Scintilla console text refresh path by clamping caret and visible-line restoration after the console text is replaced, which makes log truncation and long-play updates safer.

### GLFW Scroll Callback Recursion Fix

- Removed the manual `ImGui_ImplGlfw_ScrollCallback(...)` call from the editor's custom GLFW scroll callback.
- The editor was already letting `ImGui_ImplGlfw_InitForOpenGL(..., true)` install and own the backend callback chain, so manually calling back into the ImGui GLFW backend from the previous user callback created a plausible recursive stack-overflow path during runtime input handling.

### PlayerInteractable Physics Property

- Added a new `PlayerInteractable` rigidbody property to scene data and scene serialization.
- Exposed `PlayerInteractable` in the Properties panel as a simple colored toggle that defaults to off.
- The player can now push dynamic bodies that explicitly opt into `PlayerInteractable`; anchored bodies still do nothing even if the toggle is enabled.

### Restart-Required Settings Flow

- Replaced live application of high-impact studio settings like theme and studio text scale with a restart-required workflow.
- Added a `Studio Restart Required` popup with `Restart` and `Restart Later` options.
- Theme and studio text scale changes now save immediately but stay pending until the next restart if the user chooses `Restart Later`.

### Inspector Color Row Cleanup

- Reworked inspector color properties from the cramped default inline ImGui layout into a stacked layout with the label on its own line and the color editor taking the full available width.
- Applied the cleanup consistently to part colors, UI frame colors, text colors, and image tint so narrow inspector panels scale more cleanly.

### Import And Sound Workflow Pass

- Added `File > Import > Import Object` and `File > Import > Import Audio` to the top menu.
- Added an `Import` submenu to the hierarchy right-click create popup with the same object/audio import entries.
- Added a new `Sound` object type backed by `AudioSourceComponent`.
- Added a saved `AudioSourceType` field with `Sound`, `Ambient`, and `Music` values for audio objects.
- Added a studio setting for `Max Import Triangles`, defaulting to `3,000,000` and clamped to a hard max of `25,000,000`.
- Added real `.obj` import validation using Assimp triangle counts before import.
- Added renderer support for imported model meshes referenced through `meshAssetPath`, so imported `.obj` scene objects render as actual geometry instead of fake placeholders.
- Added Windows-native editor audio preview controls for imported `.wav` and `.mp3` clips with `Play`, `Pause`, and `Stop` in the inspector.
- Added a draggable sound playback timeline in the inspector that can seek within the currently previewing clip.
- Added a simple segmented playback meter and clearer playback percentage text for sound preview progress.
- Fixed hierarchy hover labels so audio objects now report their real audio subtype (`Sound`, `Ambient`, or `Music`) instead of always saying `Sound`.
- Added hierarchy pseudo-children for attached scripts, shown as `Script/<name>`, with direct open/remove actions from the hierarchy.
- Added protected-content rules for core asset folders such as `Assets/Shaders`, `Assets/Editor`, and the sample startup scene, and blocked deletion of those items from the Content Browser.
- Added Content Browser right-click actions with protected-item status shown in gold as `(Core - Protected)`.
- Switched the native console to styled log lines so warnings render yellow, errors render red, and log lines now format timestamps as `[time]: ...`.
- Added temporary native-view suppression while ImGui popups or tooltips are visible so hover cards and context menus can overlap the native script editor and native console correctly.
- Tuned player pushing for `PlayerInteractable` bodies to respect rigidbody mass and use pre-collision player velocity so light balls/crates react more reliably.
- Changed viewport/entity selection from the old blue box to a more Roblox-style orange overlay box and matched the marquee selection colors.
- Made the viewport status overlay size itself from its text content instead of using a fixed tiny box, which prevents cut-off tool/snap labels at larger UI scales.
- Added a small background editor frame cap when Studio is unfocused and not playing, to shave idle CPU usage without affecting active play/edit responsiveness.
- Refined native-view popup overlap handling so the console/script editor only hide when a popup or tooltip actually overlaps their native rectangle, instead of disappearing on unrelated hover.
- Replaced the separate sound scrub slider with a draggable progress bar timeline so the yellow playback bar itself is now the seek control.
- Replaced the old segmented sound preview meter with a simpler LED-style animated meter inspired by a small audio level display.
- Blocked protected scripts from opening directly out of the Content Browser.
- Fixed `Complete Studio Reset` so it no longer queues the risky live DockBuilder recovery path. It now clears the current in-memory layout state and saved settings without forcing a docking rebuild during startup.
- Added a static default `imgui_layout.ini` recovery path. Studio now writes a sane dock layout file when the saved layout is missing or obviously broken, and `Complete Studio Reset` restores that default layout file instead of leaving docking empty.
- Marked the `Status` window as explicitly non-dockable so it cannot be dropped into ImGui dock targets.

### Validation

- Rebuilt `CropsyStudio` successfully after integrating Scintilla.
- Launched the editor successfully after the native script editor replacement.
- Rebuilt `CropsyStudio` successfully after the script editor basics pass.
- Launched the editor successfully after the new indentation, comment toggle, find/replace, snippet autocomplete, and multi-error diagnostics changes.
- Rebuilt `CropsyStudio` successfully after the toolbar scaling fix.
- Launched the editor successfully after the toolbar scaling fix.
- Rebuilt `CropsyStudio` successfully after the large UI scale fix.
- Launched the editor successfully after the large UI scale fix.
- Rebuilt `CropsyStudio` successfully after the studio text scale cap change.
- Launched the editor successfully after the studio text scale cap change.
- Rebuilt `CropsyStudio` successfully after the console zoom input pass.
- Launched the editor successfully after the console zoom input pass.
- Rebuilt `CropsyStudio` successfully after the panel zoom regression fix.
- Launched the editor successfully after the panel zoom regression fix.
- Rebuilt `CropsyStudio` successfully after the scroll callback restore pass.
- Launched the editor successfully after the scroll callback restore pass.
- Rebuilt `CropsyStudio` successfully after the native panel zoom ownership fix.
- Launched the editor successfully after the native panel zoom ownership fix.
- Rebuilt `CropsyStudio` successfully after the native console view pass.
- Launched the editor successfully after the native console view pass.
- Rebuilt `CropsyStudio` successfully after the native console startup crash fix.
- Launched the editor successfully after the native console startup crash fix.
- Rebuilt `CropsyStudio` successfully after the native console control tightening pass.
- Launched the editor successfully after the native console control tightening pass.
- Rebuilt `CropsyStudio` successfully after the script token palette expansion.
- Launched the editor successfully after the script token palette expansion.
- Rebuilt `CropsyStudio` successfully after the doc hover card and status window cleanup.
- Launched the editor successfully after the doc hover card and status window cleanup.
- Rebuilt `CropsyStudio` successfully after the audio timeline, protected content browser, native popup overlap, physics push, console styling, and hierarchy script-child pass.
- Launched the editor successfully after the audio timeline, protected content browser, native popup overlap, physics push, console styling, and hierarchy script-child pass.
- Rebuilt `CropsyStudio` successfully after removing the live complete-reset dock recovery path.
- Launched the editor successfully after removing the live complete-reset dock recovery path.
- Rebuilt `CropsyStudio` successfully after adding the static default layout-file recovery path.
- Launched the editor successfully after adding the static default layout-file recovery path.
2026-04-25
- Fixed Content Browser deletion so it uses the asset database's real absolute path, removes directories/files with `remove_all`, and logs clear success/failure messages instead of silently doing nothing when nothing was removed.
- Forced the Status window to the front of the ImGui display order each frame so it stays above the main docked editor windows.
2026-04-25
- Split hierarchy and create flow more cleanly into Objects, Lighting, User Interface, and Scripts, including a new undeletable virtual `[U] User Interface` section for UI roots.
- Renamed editor-facing UI type labels to `Screen`, `Text`, and `Image`, and added a first-pass `Button` UI object with rendering, inspector editing, and scene serialization.
- Added Content Browser fast delete support with `Shift+Delete` / shift-held context delete, centered the delete confirmation popup, sorted protected core assets to the top, and added detailed colored asset-type hover metadata.
- Added settings for detailed asset hover metadata and whether `Viewport Camera Active` is shown in the Status window during play mode.
- Added a first-pass viewport UI dragger so selected UI objects can be moved directly by dragging them in the viewport.
- Renamed `.ini` asset tooltip labeling to `Initialization/Configuration Script`, locked `Screen` objects against viewport dragging and inspector position editing, and changed UI layering to a numbered `Layer` property with `1` as the lowest layer.
- Reworked the audio preview meter into a single 10-bar row that follows playback transport and cleanly stops updating when preview audio is paused or stopped.
