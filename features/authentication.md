
# Authorization

[OAuth 2.0](https://oauth.net/2/) is the industry-standard protocol for authorization. This is used to describe the user.

ack-nestjs-boilerplate-mongoose use JsonWebToken to implement OAuth2, and provide 2 guards for Authorization for admin and for public.

```typescript
@AuthPublicJwtGuard()
@AuthAdminJwtGuard()
```
