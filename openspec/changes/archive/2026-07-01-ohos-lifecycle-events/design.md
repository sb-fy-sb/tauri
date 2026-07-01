## Context

OHOS 应用生命周期事件由 `openharmony-ability` 的 `lifecycle.rs` 通过 NAPI 回调接收，转为 `MainEvent` 发送给 tao 事件循环。tao 的 OHOS `platform_impl` 负责将 `MainEvent` 映射为 `tao::event::Event`，再由 `tauri-runtime-wry` 桥接为 `RuntimeRunEvent`，最终在 Tauri 层表现为 `RunEvent`。

在此次修复前，4 个事件被 warn/TODO 拦截，1 个事件（Resumed）在 wry 桥接层缺失。

## Goals / Non-Goals

**Goals:**
- 将 Start、SaveState、ContentRectChange、Pause 4 个 MainEvent 正确转发到 Tauri RunEvent
- 补全 Event::Resumed 和 Event::Suspended 在 tauri-runtime-wry 中的桥接
- 添加自动测试验证事件链路

**Non-Goals:**
- 不修改 `Started` 的触发时机（它在 `app.run()` 之前触发是 OHOS 架构决定的）
- 不为 SaveState 实现实际的状态序列化（留给用户应用）
- 不处理 `ContentRectChange` 的 reason 枚举映射（传递原始 u32 值）

## Decisions

### D1: 事件命名

- `Start` → `Started`（过去时，与 `Resumed`/`Suspended` 一致）
- `SaveState` → `SaveStateRequested`（系统请求保存，不是已保存）
- `ContentRectChange` → `ContentRectChanged`（过去时）
- `Pause` → `Suspended`（复用已有的 tao Event 变体）

### D2: ContentRectChanged 数据格式

使用 `(i32, i32, i32, i32)` 元组表示 `(left, top, width, height)`，`u32` 表示 reason。不使用 `openharmony_ability::Rect` 类型，避免 tao 依赖 openharmony-ability 的内部类型。

### D3: cfg 门控

- `Started`、`SaveStateRequested`、`ContentRectChanged` 用 `#[cfg(target_env = "ohos")]` 门控
- `Suspended` 不用门控（Android/iOS 也需要）

### D4: Event::Resumed 桥接修复

`Event::Resumed` 在 tao 中已有定义，Android/iOS 也会发出，但 tauri-runtime-wry 中缺少桥接（只有 `NewEvents(Poll)` → `Resumed`）。添加 `Event::Resumed` → `RunEvent::Resumed` 的直接映射。

## Risks / Trade-offs

| 风险 | 缓解 |
|------|------|
| `Started` 在 tracker 初始化前触发 | 测试改为验证 Resumed（证明桥接工作） |
| `SaveState` 触发时机不可控 | 仅添加追踪，不写自动断言 |
| `ContentRectChange` 需要键盘弹出触发 | 仅添加追踪，手动验证 |
| Pause → Suspended 可能影响现有逻辑 | 测试验证无回归 |
