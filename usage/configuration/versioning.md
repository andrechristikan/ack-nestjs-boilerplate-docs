# Versioning

> Versioning is important thing to do in production. [You can read our reference](https://app.gitbook.com/s/TzVFtOmFht2SbvQi3eel/).

In NestJs there have 3 versioning types.

* URL Versioning
* Header Versioning
* Media Type Versioning

## Usage

> Versioning can on / off, by switch environment variable

ack-nestjs-boilerplate-mongoose only use `Url Versioning`. To use versioning, simply we just need to add other param in `@Controller`

```typescript
@Controller({
    version: '1', // <--- version 1
    path: 'user',
})
export class UserAdminController {}
```

After that, if environment variable `APP_VERSIONING` is `true` then our url will look like `v1/admin/user/*`, but if `APP_VERSIONING` is `false` then  our url look like `admin/user/*`
