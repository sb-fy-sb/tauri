## Why

OHOS 有 4 个生命周期事件（Start、SaveState、ContentRectChange、Pause）在 tao 层被 warn/TODO 拦截，未转发到 Tauri 应用。同时 `Event::Resumed` 在 tauri-runtime-wry 中缺失桥接，导致 Android/iOS/OHOS 的 Resumed 事件都无法到达 Tauri。

## What Changes

- **tao/src/event.rs**: 新增 `Started`、`SaveStateRequested`、`ContentRectChanged` 3 个 Event 变体
- **tao/src/platform_impl/ohos/mod.rs**: 替换 4 个 warn/TODO 为实际转发（Start→Started, SaveState→SaveStateRequested, ContentRectChange→ContentRectChanged, Pause→Suspended）
- **tauri-runtime/src/lib.rs**: 新增 `Started`、`SaveStateRequested`、`ContentRectChanged`、`Suspended` 4 个 RuntimeRunEvent 变体
- **tauri-runtime-wry/src/lib.rs**: 桥接 5 个事件（含补全 `Event::Resumed` 和 `Event::Suspended`）
- **tauri/src/app.rs**: 新增对应 RunEvent 变体 + RuntimeRunEvent→RunEvent 映射
- **tauri/examples/api**: 事件追踪 + 3 个自动测试用例

## Capabilities

### New Capabilities
- `ohos-lifecycle-events`: OHOS 生命周期事件转发到 Tauri RunEvent

### Modified Capabilities

## Impact

- **受影响代码层**: tao → tauri-runtime → tauri-runtime-wry → tauri（4 层）
- **修改文件数**: 5 个核心文件 + 2 个测试文件
- **跨平台影响**: `Event::Resumed` 桥接修复同时惠及 Android/iOS
- **事件触发时机**: `Started` 在 `app.run()` 之前触发，EventTracker 无法捕获（已知限制）
