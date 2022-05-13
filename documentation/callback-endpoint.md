# Callback Endpoint

> Difference app, difference security

Callback maybe can be special case and this really unpredictable depends on the other apps, so to avoid error in callback endpoint we must to exclude some middleware and guard. In this case we will exclude `TimestampMiddleware` and `ApiKeyGuard`.

* `TimestampMiddleware` already automatically exclude on url `callback/*` prefix.
* But for `ApiKeyGuard` will little bit difference, we need to add `AuthExcludeApiKey` decorator to on `*CallbackController` route.
