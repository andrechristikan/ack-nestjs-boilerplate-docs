# Language

> Store as global module

ack-nestjs-boilerplate-mongoose used `I18nModule` and wrap it into `MessageModule`. All languages will be managed in MessageModule and stored as `global module`. Location in `src/message/message.module.ts`

```txt
src
  └── message
      ├── ...
      └── message.module.ts
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

and then, we import it into our `BaseModule`

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
export class BaseModule {}
```

## Usage

We can use `MessageModule` with 3 ways.

* [Response Decorator](/documentation/response?id=response-decorator) or [@Response Paging Decorator](/documentation/response?id=response-paging-decorator)
* [ErrorHttpFilter](/documentation/exception)
* [Direct](#direct)

### Response Decorator or Response Paging Decorator

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

<button-jump-to name="Jump To Features Section Response" link="/#/documentation/response"></button-jump-to>

### Error Http Filter

We only need to pass the path of language, just like the response decorator.

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

<button-jump-to name="Jump To Features Section Exception" link="/#/documentation/exception"></button-jump-to>

### Direct

Or, if we want to use it directly, here's an example.

```typescript
import { Message } from 'src/message/message.decorator';
import { MessageService } from 'src/message/message.service';

@Controller({
    version: VERSION_NEUTRAL,
})
export class TestingController {
    constructor(
        private readonly messageService: MessageService, // <--- import the service
    ) {}

    @Response('test.hello')
    @Get('/hello')
    async hello(): Promise<void> {
        return this.messageService.get('test.hello'); // <--- path of language
    }
}

```

## Usage with Properties

> Only for ErrorHttpFilter or Direct use

MessageModule can use with Properties, that means you can add some property and value into message.

### Example

Test Endpoint with specific module.
Make sure set our message to receive value and property. Location in `src/message/languages/test.json`

```json
{
    "test": "This is test endpoint for {module}"
}
```

We do like this for replace `{module}` with `MessageModule`

```typescript
import { Message } from 'src/message/message.decorator';
import { MessageService } from 'src/message/message.service';

@Controller({
    version: VERSION_NEUTRAL,
})
export class TestingController {
    constructor(
        private readonly messageService: MessageService,
    ) {}

    @Response('test.hello')
    @Get('/hello')
    async hello(): Promise<void> {
        // throw new InternalServerErrorException({
        //     message: 'test.hello',
        //     properties: {
        //             module: "Test module",
        //     }
        // });

        return this.messageService.get('test.hello', {
            properties: {
                module: "Test module",
            }
        }); // <--- add property and value
    }
}
```

Then the response will look like

```txt
This is test endpoint for Test module
```

## Languages

ack-nestjs-boilerplate-mongoose already provides 2 languages (`English` and `Indonesia`). Check out in `src/message/languages/*`

```txt
src
  └── message
      ├── ...
      └── languages 
          ├── en
          └── id
```

And this our enum

```typescript
export enum ENUM_MESSAGE_LANGUAGE {
    en = 'en',
    id = 'id',
}
```

### Default language

> The default of language is `en`

We can see or change the default from `.env` file

```txt
APP_LANGUAGE=en

...
...
...
```

### Dynamic language

We don't need any additional configuration, we just need to add `x-custom-lang` into `Header Request`. For example, we will change the language to `id` .

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

Yup, as you might expect. We just need to change the value from `.env` or `x-custom-lang` on `Header Request`.

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
