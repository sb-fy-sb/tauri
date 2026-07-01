## ADDED Requirements

### Requirement: OHOS Start event forwarded to Tauri
The system SHALL forward OHOS `onAbilityStart` as `RunEvent::Started`.

#### Scenario: App receives Started event
- **WHEN** OHOS calls `onAbilityStart`
- **THEN** `RunEvent::Started` SHALL be emitted to the app's event handler

### Requirement: OHOS SaveState event forwarded to Tauri
The system SHALL forward OHOS `onAbilitySaveState` as `RunEvent::SaveStateRequested`.

#### Scenario: App receives SaveStateRequested event
- **WHEN** OHOS calls `onAbilitySaveState`
- **THEN** `RunEvent::SaveStateRequested` SHALL be emitted to the app's event handler

### Requirement: OHOS ContentRectChange event forwarded to Tauri
The system SHALL forward OHOS `contentRectChange` as `RunEvent::ContentRectChanged`.

#### Scenario: App receives ContentRectChanged event
- **WHEN** the content rect changes (e.g. keyboard shown/hidden)
- **THEN** `RunEvent::ContentRectChanged { rect, reason }` SHALL be emitted with the new rect and reason

### Requirement: OHOS Pause event forwarded as Suspended
The system SHALL forward OHOS `onPause` as `RunEvent::Suspended`.

#### Scenario: App receives Suspended event
- **WHEN** OHOS calls the pause lifecycle callback
- **THEN** `RunEvent::Suspended` SHALL be emitted to the app's event handler

### Requirement: Resumed event bridge fix
The system SHALL bridge `tao::Event::Resumed` to `RunEvent::Resumed` in tauri-runtime-wry.

#### Scenario: Resumed reaches Tauri on all mobile platforms
- **WHEN** tao emits `Event::Resumed` (Android onResume / iOS applicationWillEnterForeground / OHOS SurfaceCreate)
- **THEN** `RunEvent::Resumed` SHALL be emitted to the app's event handler
