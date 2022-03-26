# Logger

> Location of log file is `/logs`

Catch every activity with `logger`. ack-nestjs-mongoose can catch every activity about what user do. By default, user activities will be written into files, and console.

There have 3 different module about logger

* `HttpDebuggerModule` can catch everything from the incoming requests.
* `DebuggerModule` is like `console.log` but can write into files.
* `LoggerModule` is a logger that can write into a database

## HttpDebuggerModule

`HttpDebuggerModule` is automatically import into app module as `middleware`. The location in `src/utils/middleware/middleware.module.ts`

```txt
src
  └── utils
      └── middleware
           ├── *
           └── middleware.module.ts
```

## DebuggerModule

> Store as global module

`DebuggerModule` is like `console.log` but can write into files. `DebuggerModule` have 3 difference level

1. Info : will write into console, and write into `/logs/system/default/*`.
2. Debugger: will write into `/logs/system/debug/*`.
3. Error: will write into `/logs/system/log/*`

### Usage

In every where of the module (service, controller, pipe, etc).

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
            {data}
        );

        return;
    }
}
```

## LoggerModule

> Store as global module

`LoggerModule` is a logger that can write into a database. `LoggerModule` have purpose to save log into database, and then the data will use by our data scientist.

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
    async logger(): Promise<void> {
        await this.loggerService.info({
            action: ENUM_LOGGER_ACTION.LOGGER,
            description: `${user._id} do test logger`,
            user: user._id,
            tags: ['logger', 'test'],
        });

        return;
    }
}
```
