
# Versioning

> Versioning is the important thing to do in production. [You can read our reference](https://restfulapi.net/versioning/).

NestJs provides 3 types of versioning.

* URL Versioning
* Header Versioning
* Media Type Versioning

ack-nestjs-boilerplate-mongoose only uses `Url Versioning`. To use versioning, we simply add other params in `@Controller`

## Usage

> Versioning can be on / off, by switching environment variable

```typescript
@Controller({
    version: '1', // <--- version 1
    path: 'user',
})
export class UserAdminController {}
```

After that, if the environment variable `APP_VERSIONING` is `true` then our URL will look like `v1/admin/user/*`, but if `APP_VERSIONING` is `false` then  our URL look like `admin/user/*`
