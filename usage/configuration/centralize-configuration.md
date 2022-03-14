# Centralize Configuration

## Config Module

ack-nestjs-boilerplate-mongoose used `@nestjs/config` module to manage all configs and set in `src/config/*`.

We don't need to import ConfigModule for every modules, that because stored in `global variable.`  We can see in Core`Module`

```typescript
import { ConfigModule } from '@nestjs/config';

@Module({
    controllers: [],
    providers: [],
    imports: [
        MiddlewareModule,
        ConfigModule.forRoot({
            load: Configs,
            ignoreEnvFile: false,
            isGlobal: true, // <--- Import as global
            cache: true,
            envFilePath: ['.env', '.env.share'],
        }),
        
        ...
        ...
        ...
    ],
})
export class CoreModule {}

```

## Usage

### Set default value

> We recommend to set default value with `ConfigModule`, and don't set default value in Controller, Service, or OtherModule. If you set without `ConfigModule`, that's can make hard to maintain.

We can use `.env file` as default value of our config, or we can set directly. Let see our `AppConfig`

```typescript
import { registerAs } from '@nestjs/config';

// we can call our env just by using process.env
export default registerAs(
    'app',
    (): Record<string, any> => ({
        env: process.env.APP_ENV || 'development',
        language: process.env.APP_LANGUAGE || 'en',
        versioning: process.env.APP_VERSIONING === 'true' || false,
        http: {
            host: process.env.APP_HOST || 'localhost',
            port: parseInt(process.env.APP_PORT) || 3000,
        },
        
        ...
        ...
        ...
    })
);

```

### Add new config

Let add `AppConfig` into `config index`, so we can call `AppConfig` anywhere.

```typescript
import AppConfig from 'src/config/app.config';

export default [
    AppConfig, // <--- add to here
    
    ...
    ...
    ...
];

```

### Use Config in Controller or Service

Configs can access by call `ConfigService` . Just put it tu constructor of function and don't forget to use `@Injectable` decorator too.

```typescript
import { ConfigService } from '@nestjs/config';

@Injectable()
export class AppService {
    private readonly env: string;

    constructor(
        private readonly configService: ConfigService
    ) {
        this.env = this.configService.get<string>(
            'app.env'
        );
    }
    
    ...
    ...
    ...
}
```
