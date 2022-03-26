# Language

> Store as global module

ack-nestjs-boilerplate-mongoose use `I18nModule` and wrap into `MessageModule`. All languages will manage in MessageModule and store into `Global variable` same as `ConfigModule`. It's because we will use it often. Location in `src/message/message.module.ts`

```txt
src
  └── message
      └── languages 
          ├── en
          └── id
```

## Message Module

`MessageModule` will look like this.

```typescript
import { Global } from '@nestjs/common';
import { MessageService } from 'src/message/message.service';

@Global() // <--- will store in global
@Module({
    providers: [MessageService],
    exports: [MessageService],
    imports: [],
    controllers: [],
})
export class MessageModule {}
```

and then, we import into our `CoreModule`

```typescript
import { MessageModule } from 'src/message/message.module';

@Module({
    controllers: [],
    providers: [],
    imports: [
        ...
        ...
        ...
        
        MessageModule, // <--- add to here
        PaginationModule,
        DebuggerModule,

        ...
        ...
        ...
    ],
})
export class CoreModule {}
```

## Language

ack-nestjs-boilerplate-mongoose already provide 2 languages (`English` and `Indonesia`). Check out in `src/message/languages/*`

And this our enum

```typescript
export enum ENUM_MESSAGE_LANGUAGE {
    en = 'en',
    id = 'id',
}
```

### Default language

> The default of language is `en`

We can see or change default from `.env` file

```txt
APP_ENV=development
APP_HOST=localhost
APP_PORT= 3000
APP_LANGUAGE=en # <-- change this value
APP_VERSIONING=false
APP_DEBUG=false
APP_TZ=Asia/Jakarta

...
...
...
```

### Dynamic language

We don't need any extra configuration, we just need to add `x-custom-lang` into `Header Request`. Example we will change language to `id` .

```json
{
    "headers": {
        "x-custom-lang": "id",
        
        ...
        ...
        ...
    }
}
```

### Multi Language

> Delimiter is coma (,)

Yup, as you guess. We just need to change value from `.env` or `x-custom-lang` on `Header Request`.

```json
{
    "headers": {
        "x-custom-lang": "en,id",
        
        ...
        ...
        ...
    }
}
```

## Usage

We can use `MessageModule` with 3 way.

* [@Response()](/features/response?id=response-decorator) or [@ResponsePaging()](/features/response?id=response-paging-decorator)
* [ErrorHttpFilter](/features/exception)
* [Direct](#direct)

### Response Decorator

We just need to pass the path of language to be param of response decorator.

```typescript
import { Response } from 'src/utils/response/response.decorator';

@Controller({
    version: VERSION_NEUTRAL,
})
export class TestingController {

    @Response('test.hello') // <--- path of language
    @Get('/hello')
    async hello(): Promise<void> {
        return;
    }
}
```

### Error Http Filter

Same as response decorator, we just need to pass the path of language.

```typescript
import { InternalServerErrorException } from '@nestjs/common';
import { ENUM_STATUS_CODE_ERROR } from 'src/utils/error/error.constant';
import { Response } from 'src/utils/response/response.decorator';

@Controller({
    version: VERSION_NEUTRAL,
})
export class TestingController {
    @Response('test.hello')
    @Get('/hello')
    async hello(): Promise<void> {
        throw new InternalServerErrorException(
            'http.serverError.internalServerError', // <--- path of language
        );
    }
}
```

### Direct

Or we want to direct use, here example for that

```typescript
import { Message } from 'src/message/message.decorator';
import { MessageService } from 'src/message/message.service';

@Controller({
    version: VERSION_NEUTRAL,
})
export class TestingController {
    constructor(
        private readonly messageService: MessageService, // <--- describe the service
    ) {}

    @Response('test.hello')
    @Get('/hello')
    async hello(): Promise<void> {
        return this.messageService.get('test.hello'); // <--- direct use
    }
}

```
