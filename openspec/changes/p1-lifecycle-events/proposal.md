## Why

OHOS 平台的 3 个生命周期事件（`SaveState`、`Start`、`ContentRectChange`）在 tao 事件循环中被 `warn!("TODO")` 忽略，导致 Tauri 应用无法感知这些系统事件。这些事件对移动设备场景至关重要：SaveState 允许应用在系统杀死前保存状态，Start 通知应用已启动，ContentRectChange 通知内容区域变化（如键盘弹出）。

## What Changes

- **tao Event 枚举**：新增 3 个生命周期事件变体
- **tao OHOS platform_impl**：替换 warn/TODO 为实际转发
- **tauri-runtime RunEvent**：新增对应变体
- **tauri-runtime-wry**：桥接 tao → tauri 事件
- **tauri RunEvent**：新增对应变体，暴露给应用

## Capabilities

### New Capabilities
- `ohos-lifecycle-events`: 转发 OHOS 独有的 SaveState / Start / ContentRectChange 生命周期事件到 Tauri 应用

### Modified Capabilities

## Impact

- **受影响代码层**: openharmony-ability（已有，无需改动）→ tao（3 文件）→ tauri-runtime（1 文件）→ tauri-runtime-wry（1 文件）→ tauri（1 文件）
- **平台**: 仅 OHOS，其他平台不受影响
- **向后兼容**: 新增事件变体，不影响现有代码
