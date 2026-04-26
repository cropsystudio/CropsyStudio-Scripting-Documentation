# Known Bugs And Blockers

## Environment Blockers

- `cmake` is not available on `PATH`.
- `cl`, `g++`, and `clang++` are not available on `PATH`.
- `python` is not available on `PATH`, so automated GLAD generation is not practical on this machine.
- Build and launch verification are possible on this machine by calling the installed CMake/Visual Studio toolchain with explicit absolute paths.

## Engine Implementation Status

- Assimp/Bullet/OpenAL integration should be treated as unverified until the first successful build and run.
- Audio playback infrastructure is currently a stubbed service and does not yet play clips.
- Model import hooks are not implemented yet, so the sample content uses built-in primitive meshes only.
- Physics currently uses a lightweight built-in gravity/collider step instead of a verified Bullet-backed simulation path.
- Texture importing is not implemented yet; materials currently render with solid tint colors.
- `ImageLabel` currently renders as a labeled placeholder box in the viewport overlay. Real texture-backed UI image rendering is still pending.
- UI editing is currently property-driven through the inspector and hierarchy. Direct viewport resize handles for UI elements are not implemented yet.
- Built-in behavior hooks currently support a tiny hand-written set of C++ behaviors. There is no Lua integration yet, and unknown behavior names are ignored safely.
- Attached `.crop` scripts currently support `Manual`, `OnPlayStart`, and `OnPlayUpdate`. The update mode is still intentionally simple and works best with small command scripts that use the `dt` token for frame-rate-aware movement.
- Script parameters now work per attached script slot, but they are currently stored as simple name/value strings rather than richer typed fields.
- Script auto-check now highlights multiple error lines, but the new multi-error summary still needs real hands-on QA for very large scripts and overlapping diagnostics on the same line.
- Content import currently copies files into project asset folders safely, but it does not yet generate thumbnails or perform deep asset processing.
- Script assets are now reusable across multiple objects, but there is no dependency/rename tracker yet for automatically updating references if files are moved outside the editor workflow.
- The script editor now uses a native Scintilla control instead of the old ImGui text box, but its new editing behavior still needs real hands-on QA for caret placement, popup positioning, and scrolling edge cases.
- The studio text scale is now intentionally capped at `200%` because the old `300%` path was unstable on this machine. Large saved dock layouts may still need manual resizing when switching between very different scales.
- The console now uses a native read-only Scintilla control instead of the old ImGui text box, but its new native behavior still needs real hands-on QA for focus, dock resizing, and long-log update behavior.
- Multiple native Scintilla controls now share safe registration, but the new native console view still needs real hands-on QA for startup focus, dock resizing, long-log update behavior, and wheel/zoom interaction under normal editing use.
- Runtime and script log spam is now capped in memory, but the native console still needs real hands-on QA under long play sessions with frequent logging to confirm there are no remaining update-path crashes.
- The play-mode crash investigation found a risky GLFW scroll callback chain in the editor. The recursive backend call has been removed, but play-mode still needs real hands-on QA after this input-path fix to confirm the stack-overflow crash is gone.
- `PlayerInteractable` now lets the player push opted-in dynamic bodies, but it still needs real hands-on tuning for feel, especially with stacked objects and very light bodies.
- Theme and studio text scale now use a deferred restart flow instead of hot-applying, but the new restart prompt still needs real hands-on QA for relaunch timing and unsaved-scene recovery behavior.
- Inspector color rows have been widened and stacked, but the new layout still needs real hands-on QA at different panel widths and studio text scales.
- Imported `.obj` models now render through a simple cached Assimp path, but they still need real hands-on QA with larger files, multiple submeshes, and unusual normal data.
- Audio import now creates `Sound` objects and supports editor preview for `.wav` and `.mp3`, but runtime scene playback is still not a fully integrated engine audio feature yet.
- The script editor now has a richer token palette, but the new entity-name and keyword colors still need real hands-on QA against larger scripts to make sure they stay readable in every theme and script style.
- The `Status` overlay is now intentionally non-dockable and non-resizable because docking it into ImGui dock targets could destabilize the editor layout path on this machine.
- The new sound timeline can seek the editor preview clip, but it still needs real hands-on QA for pause/resume edge cases and end-of-clip seeking behavior with both `.wav` and `.mp3`.
- Protected Content Browser rules are currently path-based and intentionally conservative. They still need real hands-on QA to confirm the protected set is strict enough to guard Studio-critical files without blocking normal project cleanup.
- Native script/console views now hide themselves while ImGui popups or tooltips are visible so overlays can appear on top, but this still needs real hands-on QA for flicker and focus behavior during heavy popup use.
- Native script/console overlap handling is now rectangle-based instead of global, but it still needs real hands-on QA to confirm popup/tooltip overlap feels natural without visible flicker near native controls.
- The console now colors warning/error lines in native Scintilla, but it still needs real hands-on QA with long logs, wrapping, and filtered refreshes to make sure styling stays aligned with the right lines.
- `PlayerInteractable` pushing now uses rigidbody mass and pre-collision player velocity, but it still needs real hands-on tuning to confirm the push feel is right for spheres, stacks, and very heavy bodies.
- Attached scripts now appear as hierarchy pseudo-children, but that still needs real hands-on QA for search/filter behavior and slot-removal behavior while a script is open in the editor.
- The viewport overlay now auto-sizes to its text instead of using a hard-coded tiny box, but it still needs real hands-on QA at larger studio scales and very narrow viewport widths.
- Background CPU usage is now modestly throttled when Studio is unfocused and not playing, but active focused CPU usage still needs profiling before claiming any broad optimization win.
- The sound preview timeline now scrubs via the progress bar itself and only seeks on release to avoid lag, but it still needs real hands-on QA for drag feel and end-of-track behavior.
- A previously broken full-reset layout file can still remain on disk until the running Studio instance is closed, because Windows keeps the live layout file locked while the app is open. The risky live DockBuilder startup recovery has been removed, so manual layout-file cleanup is still the safest fallback if a past bad layout persists.
- Default layout recovery now uses a static `imgui_layout.ini` template instead of live dock rebuilding, but the template still needs real hands-on QA at different window sizes and monitor setups.
- The `Status` window is now flagged non-dockable, but it still needs real hands-on QA to confirm old saved layouts cannot force it back into a bad dock state.

## Planned Validation Checklist

- Editor launches
- Docked layout renders
- Viewport framebuffer renders a scene
- Camera moves in the viewport
- Scene save/load round-trips
- Play/stop preserves edit scene state
- Physics simulates falling rigidbodies
- Example project browser loads local JSON entries
2026-04-25
- Content Browser deletion should now fail loudly instead of silently, but it still needs hands-on QA with open/locked assets like active audio previews and currently open script files.
- The Status window is now forced to the front each frame, but it still needs real QA to confirm it doesn't steal focus in annoying ways while editing.
2026-04-25
- The new UI `Button` object is a first-pass editor/runtime UI element, not a full interactive click-event system yet.
- Content Browser fast delete now bypasses confirmation on shift, so real hands-on QA is still needed to confirm it feels safe enough with locked or externally modified files.
- Protected core assets are now sorted to the top and tinted, but that ordering still needs hands-on QA with very large asset lists.
- The viewport UI dragger is a first-pass direct-move tool and does not yet have resize handles, rotation handles, or dedicated undo granularity separate from the general dirty-state flow.
- The new 10-bar audio preview meter is transport-synced and pause/stop-aware, but it is not yet a true sample-amplitude analyzer because the current Windows MCI preview path does not expose real decoded loudness data.
