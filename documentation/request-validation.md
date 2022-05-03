# Request Validation

> Store as global module

ack-nestjs-boilerplate-mongoose consume NestJs validation pipe. `ValidationPipe` will validates all incoming requests based on `ClassValidator`. Location in `src/utils/request/request.module.ts`

```txt
src
  └── utils
      └── request 
          └── request.module.ts
```

## Validation Pipe

To use `ValidationPipe` we need to create `ClassValidator` as a validator for our request.

```typescript
import { Type } from 'class-transformer';
import {
    IsNotEmpty,
    IsEmail,
    MaxLength,
    IsBoolean,
    IsOptional,
    ValidateIf,
    IsString,
} from 'class-validator';

export class AuthLoginDto {
    @IsEmail()
    @IsNotEmpty()
    @MaxLength(100)
    readonly email: string;

    @IsOptional()
    @IsBoolean()
    @ValidateIf((e) => e.rememberMe !== '')
    readonly rememberMe?: boolean;

    @IsNotEmpty()
    @Type(() => String)
    @IsString()
    readonly password: string;
}

```

Then put `ClassValidator` as type of `@Body`/`@Query`/`@Param` in `Controller`.

```typescript
@Controller({
    version: '1',
})
export class AuthController {

    ...
    ...
    ...
    
    @Response('auth.login', ENUM_AUTH_STATUS_CODE_SUCCESS.AUTH_LOGIN_SUCCESS)
    @HttpCode(HttpStatus.OK)
    @Post('/login')
    async login( 
        @Body() body: AuthLoginValidation // <--- use like this
    ): Promise<IResponse> {
    
    ...
    ...
    ...
    
    }
}
```

`ValidationPipe` will pass error into `ErrorHttpFilter`, so the response will look like

```json
{
    "statusCode": 422,
    "message": "Request validation errors",
    "errors": [
        {
            // "message": "{property} should be a type of email",
            "message": "Email field should be a type of email",
            "property": "email"
        }
    ]
}
```

### Interface of errors

```typescript
export interface IErrors {
    readonly message: string;
    readonly property: string;
}
```
