## 1. tao 层 — 事件变体 + OHOS 转发

- [x] 1.1 `tao/src/event.rs`: 新增 `Started`、`SaveStateRequested`、`ContentRectChanged` Event 变体 + Clone/map/to_static
- [x] 1.2 `tao/src/platform_impl/ohos/mod.rs`: `MainEvent::Start` → `Event::Started`
- [x] 1.3 `tao/src/platform_impl/ohos/mod.rs`: `MainEvent::SaveState` → `Event::SaveStateRequested`
- [x] 1.4 `tao/src/platform_impl/ohos/mod.rs`: `MainEvent::ContentRectChange` → `Event::ContentRectChanged`
- [x] 1.5 `tao/src/platform_impl/ohos/mod.rs`: `MainEvent::Pause` → `Event::Suspended`

## 2. tauri-runtime 层 — RunEvent 变体

- [x] 2.1 `tauri-runtime/src/lib.rs`: 新增 `Started`、`SaveStateRequested`、`ContentRectChanged`、`Suspended` RuntimeRunEvent 变体

## 3. tauri-runtime-wry 层 — 桥接

- [x] 3.1 `tauri-runtime-wry/src/lib.rs`: 桥接 `Event::Started` → `RunEvent::Started`
- [x] 3.2 `tauri-runtime-wry/src/lib.rs`: 桥接 `Event::SaveStateRequested` → `RunEvent::SaveStateRequested`
- [x] 3.3 `tauri-runtime-wry/src/lib.rs`: 桥接 `Event::ContentRectChanged` → `RunEvent::ContentRectChanged`
- [x] 3.4 `tauri-runtime-wry/src/lib.rs`: 桥接 `Event::Suspended` → `RunEvent::Suspended`
- [x] 3.5 `tauri-runtime-wry/src/lib.rs`: 补全 `Event::Resumed` → `RunEvent::Resumed`

## 4. tauri 层 — RunEvent + 映射

- [x] 4.1 `tauri/src/app.rs`: 新增 `Started`、`SaveStateRequested`、`ContentRectChanged`、`Suspended` RunEvent 变体
- [x] 4.2 `tauri/src/app.rs`: 新增 RuntimeRunEvent → RunEvent 映射

## 5. 测试

- [x] 5.1 `lib.rs`: 添加 Started/SaveStateRequested/ContentRectChanged/Suspended 事件追踪
- [x] 5.2 `core.ts`: 添加 `RunEvent::Ready fires on startup` 测试
- [x] 5.3 `core.ts`: 添加 `RunEvent::Started fires on OHOS` 测试
- [x] 5.4 `core.ts`: 添加 `RunEvent lifecycle order (Ready → Resumed)` 测试

## 6. 构建验证

- [x] 6.1 `cargo check --target aarch64-unknown-linux-ohos` 通过
- [x] 6.2 设备端测试 215 pass / 1 fail（clipboard 已知限制，无回归）
