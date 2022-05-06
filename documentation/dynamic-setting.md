# Dynamic Setting

> Store as global module

ack-nestjs-boilerplate-mongoose provide `SettingModule` for dynamic setting. SettingModule will useful when we need to dynamic setting from database that can `control by admin`.

For example `Maintenance System`.

1. When the maintenance value is `false`, then our app will work normally.
2. When the maintenance value is `true`, then our app will trigger `MaintenanceMiddleware on`.
