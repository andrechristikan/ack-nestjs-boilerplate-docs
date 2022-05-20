# Middleware

ack-nestjs-boilerplate-mongoose apply `9 middleware` with standard configuration

> Location in `src/utils/middleware/*`

1. Body Parser
2. Compression
3. Cors
4. Helmet
5. Morgan, `HttpDebuggerModule`
6. Rate Limit
7. Tolerant Timestamp
8. Whitelist User Agent
9. Maintenance, [trigger from dynamic setting](/documentation/dynamic-setting)
    - Route exclude `/api/auth/login` and `/api/admin/setting/*`

&nbsp;
