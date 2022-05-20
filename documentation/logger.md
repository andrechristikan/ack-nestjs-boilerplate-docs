# Logger

> Location of log file is `/logs`

`Logger` can record every activity. ack-nestjs-mongoose can record every action taken by the user. By default, user activities are recorded in files.

There are 3 different logger modules.

* `HttpDebuggerModule` can catch everything from the incoming requests, package `morgan`.
* `DebuggerModule` is similar to `console.log` but can write into files, package `winston`.
* `LoggerModule` is a logger that can write into a database

## HttpDebuggerModule

`HttpDebuggerModule` is automatically imported into the app module as `middleware`. The location in `src/utils/middleware/middleware.module.ts`

```txt
src
  └── utils
      └── middleware
           ├── *
           └── middleware.module.ts
```

## DebuggerModule

> Store as global module

`DebuggerModule` is similar to `console.log` but can write into files. `DebuggerModule` have 3 different level

1. Info : will write to console, and write into file `/logs/system/default/*`.
2. Debugger: will write into file`/logs/system/debug/*`.
3. Error: will write into file `/logs/system/log/*`

### Usage

Everywhere in the module (service, controller, pipe, etc).

```typescript
@Controller({
    version: '1',
})
export class DebuggerController {
    constructor(
        private readonly debuggerService: DebuggerService,
    ){}

    @Response('debugger')
    @Post('/debugger')
    async debugger(): Promise<void> {
        this.debuggerService.info(
            'description debugger', 
            'class', 
            'function', 
            data
        );

        return;
    }
}
```

## LoggerModule

> Store as global module

`LoggerModule` is a logger that can write into a database. `LoggerModule` has a purpose to save the log into the database, and then the data will use by our data scientist.

### Usage

```typescript
@Controller({
    version: '1',
})
export class LoggerController {
    constructor(
        private readonly loggerService: LoggerService,
    ){}

    @Response('logger')
    @Post('/logger')
    async logger(
        @User() user: Record<string,any>, 
        @ApiKey() apiKey: IAuthApiPayload
    ): Promise<void> {
        await this.loggerService.info({
            action: ENUM_LOGGER_ACTION.LOGGER,
            description: `${user._id} do test logger`,
            user: user._id,
            apiKey: apiKey._id,
            tags: ['logger', 'test'],
        });

        return;
    }
}
```

&nbsp;
