# Merge notes: upstream v3.2.12

## Result

- Base: wxWidgets v3.2.12
- Tag: `bambu-3.2.12-1`
- Merge strategy: **single** merge of `v3.2.12` into `merge/upstream-v3.2.12` (from bambulab `master`)
- Residual vs upstream: ~23 files (Bambu product deltas only)

## Kept Bambu deltas

- **WebView Edge** `SetUserDataPathOption` (`include/wx/webview.h`, `include/wx/msw/webview_edge.h`, `src/msw/webview_edge.cpp`)
- **WebView OSX** `SetUserDataPathOption`, `SetNonPersistentWebsiteDataStore`, data-store helper, and async `AddScriptMessageHandler(name, runScriptSync)` (`include/wx/osx/webview_webkit.h`, `src/osx/webview_webkit.mm`, `interface/wx/webview.h`)
- **`wxApp::OSXStoreOpenFiles` virtual** so Studio `GUI_App` can override (`include/wx/osx/app.h`)
- **`wxToolTip::GetToolTipCtrl()` public** for MSW dark-mode tooltip HWND theming (`include/wx/msw/tooltip.h`)
- **gstplayer forced OFF** in cmake (BambuStudio#3279) (`build/cmake/init.cmake`)
- **BBS grid**: multiplexed column resize via `DoGridDragResize(..., CursorMode)` and select-on-first-click (`include/wx/generic/grid.h`, `src/generic/grid.cpp`)
- **Per-window DPI helpers**: `wxGetDisplaySize(window)` / `wxDisplaySize(window, …)` and MSW fractional font size with `wxWindow*` (`include/wx/gdicmn.h`, `src/common/gdicmn.cpp`, `include/wx/fontutil.h`, `src/msw/font.cpp`, `src/msw/window.cpp`, `src/osx/cocoa/settings.mm`)
- **MSW menu separator height 0** (`src/msw/menuitem.cpp`)
- **Combo popup vertical anchor / multi-monitor positioning** residual (`src/common/combocmn.cpp`)
- **`wxDataViewCtrlBase::GetSelection` empty-safe** (`src/common/datavcmn.cpp`)

## Dropped Bambu deltas (reason)

- **webkit2gtk-4.1** cmake/find glue — upstream 3.2 already finds 4.1 then falls back to 4.0
- **Win ARM64 `WEBVIEW2_ARCH` manual branch** — superseded by upstream `wx_set_webview2_arch()` / `wxPLATFORM_ARCH`
- **WebView2 SDK pin 1.0.1418.22** — took upstream **1.0.3485.44**
- **`wxImage::ShrinkBy` MSVC optimizer workaround** — present upstream
- **OSX `drawRect` / `clipsToBounds` Sonoma hack** — superseded by upstream `ClipsToBounds` for user panes
- **macOS Spaces modal disappear** — already in upstream 3.2.12 (no residual)
- **macOS 26 app-not-shown until input** — already in upstream 3.2.12
- **Vojtech OSX DataView keyDown/mouseMoved accelerator hacks** — dropped; prefer upstream 3.2 `doCommandBySelector` path (Studio QA: object-list shortcuts)
- **Win32 locale decimal assert `#if 0` mute** — assert already removed upstream
- **BitmapComboBox inline recreate / WNDPROC** — took upstream `MSWRecreate()`
- **`configure` / `configure.in` regenerations** — took upstream wholesale
- **OpenGL EGL cmake Bambu branch** — took upstream EGL target handling

## API compatibility for BambuStudio

- `SetUserDataPathOption`: **yes**
- `SetNonPersistentWebsiteDataStore`: **yes**
- `AddScriptMessageHandler(name, runScriptSync)`: **yes** (OSX)
- `OSXStoreOpenFiles` virtual: **yes**
- `GetToolTipCtrl` public: **yes**
- DataView macOS accelerators: **upstream OK** (Bambu keyDown hack dropped; QA required)
- Win ARM64 WebView2 loader: **yes** via upstream `wxPLATFORM_ARCH`

## Build smoke

- Platform: macOS arm64 (AppleClang), Xcode
- CMake flags: `-DwxBUILD_SHARED=OFF -DwxBUILD_SAMPLES=OFF -DwxBUILD_TESTS=OFF -DwxBUILD_DEMOS=OFF -DwxUSE_WEBVIEW=ON -DwxUSE_OPENGL=ON -DwxUSE_AUI=ON -DwxUSE_MEDIACTRL=ON -DwxUSE_EXCEPTIONS=OFF -DCMAKE_BUILD_TYPE=Release`
- Result: **pass** (configured as wxWidgets 3.2.12 osx_cocoa static; built core + webview libs)
- Note: init git submodules before cmake (`git submodule update --init --recursive`)

## Follow-ups for BambuStudio agents

- Pin `deps/wxWidgets/wxWidgets.cmake` `GIT_TAG` to **`bambu-3.2.12-1`** (stop floating on `master`)
- Expected Studio compile/port work: BitmapBundle, DPI/font APIs if Studio still uses custom overloads, any 3.1.5-only symbols
- QA focus: Filament Manager WebView, WipeTower / MultiNozzle webviews (async handler), object-list keyboard shortcuts on macOS, multi-monitor DPI, Linux mediactrl without gstplayer
- Open fork PRs still out of scope: #5 IME, #10 Wayland/EGL
- Ongoing sync: `git merge v3.2.X` → tag `bambu-3.2.X-1` (bump `-N` for fork-only hotfixes)

## Handoff blurb

```text
wx fork merge done: bambulab/wxWidgets tag bambu-3.2.12-1 (upstream v3.2.12 + residual Bambu deltas).
See MERGE_NOTES.md. Next: point BambuStudio deps/wxWidgets/wxWidgets.cmake at that tag and port/fix Studio for wx 3.2.
```
