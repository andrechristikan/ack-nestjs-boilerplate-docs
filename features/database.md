# Database

[MongoDB](https://www.mongodb.com) is one of the popular no sql database. [Mongoose](https://mongoosejs.com) is a package that integrates NodeJs and MongoDB. Mongoose provides an elegant way to write MongoDB object modeling for NodeJs. We can fully control our database by writing code with mongoose.

## Mongoose

### Integration

ack-nestjs-boiler-mongoose already integrated with `mongodb` and we already provide `config` to control the settings of database. Location in `src/config/database.config.ts`.

```typescript
import { registerAs } from '@nestjs/config';

export default registerAs(
    'database',
    (): Record<string, any> => ({
        host: process.env.DATABASE_HOST || 'localhost:27017',
        name: process.env.DATABASE_NAME || 'ack',
        user: process.env.DATABASE_USER || null,
        password: process.env.DATABASE_PASSWORD || null,
        admin: process.env.DATABASE_ADMIN === 'true' || false,
        srv: process.env.DATABASE_SRV === 'true' || false,
        ssl: process.env.DATABASE_SSL === 'true' || false,
        debug: process.env.DATABASE_DEBUG === 'true' || false,
        options: process.env.DATABASE_OPTIONS,
    })
);
```

and also for dynamic configuration, ack-nestjs-boiler-mongoose put configuration in `env`.

```txt
DATABASE_HOST=localhost:27017
DATABASE_NAME=ack
DATABASE_USER=
DATABASE_PASSWORD=
DATABASE_ADMIN=false
DATABASE_SRV=false
DATABASE_DEBUG=false
DATABASE_SSL=false
DATABASE_OPTIONS=
```

Simply if we want to change database, we just need to change the env.


### Populate and Deep Populate

`Populate` is similar as `lookup in mongodb` or `join in mysql`. To do populate in mongoose do as follows.

#### Populate

Example, we want populate role with permissions

```typescript
this.roleModel.findOne(find).populate({
    path: 'permissions', // <--- path is field in role collection
    model: PermissionEntity.name, // <--- model of permission
});
```

#### Deep Populate

> We can also do deep populate to deepest populate as many times as we want.

Example, Get user with role and permissions.

```typescript
this.userModel.findOne(find).populate({
    path: 'role',
    model: RoleEntity.name,
    populate: { // <--- add deep populate
        path: 'permissions',
        model: PermissionEntity.name,

        populate: { // <--- can add repeat populate / deepest populate
            ...
            ...
            ...
        }
    },
});
```

### Transaction

!> require mongodb as replication set

The scenario is `delete our user`.

1. Make sure we defined `connection name of Mongoose`

    ```typescript
    import { MongooseModule } from '@nestjs/mongoose';
    import { DATABASE_CONNECTION_NAME } from 'src/database/database.constant';

    @Module({
        imports: [
            MongooseModule.forFeature(
                [
                    {
                        name: UserEntity.name,
                        schema: UserSchema,
                        collection: UserDatabaseName,
                    },
                ],
                DATABASE_CONNECTION_NAME // < ----- mongoose connectionName
            ),
        ],
        exports: [UserService, UserBulkService],
        providers: [UserService, UserBulkService],
        controllers: [],
    })
    export class UserModule {}
    ```

2. Import mongoose connection into controller

    ```typescript
    import { Connection } from 'mongoose';
    import { DatabaseConnection } from 'src/database/database.constant'; 

    export class UserAdminController {
        constructor(
            @DatabaseConnection() private readonly databaseConnection: Connection, // < ---- import this
            private readonly debuggerService: DebuggerService,
            ...
            ...
            ...
        ) {}

    ...
    ...
    ...
    }
    ```

3. Edit UserService to use  `Transaction session mongoose`

    ```typescript

    export class UserService {
        async deleteOneById(_id: string, session: mongodb.ClientSession): Promise<boolean> {
            return this.userModel.findOneAndDelete({
                _id: new Types.ObjectId(_id),
            }, { session });
        }
    }
    ```

4. Use UserService like this

    ```typescript
    import { Connection } from 'mongoose';
    import { DatabaseConnection } from 'src/database/database.constant'; 
    
    export class UserAdminController {
        constructor(
            @DatabaseConnection() private readonly databaseConnection: Connection,
            ...
            ...
            ...
        ) {}
    
        @Response('user.delete')
        @UserDeleteGuard()
        @AuthAdminJwtGuard(ENUM_PERMISSIONS.USER_READ, ENUM_PERMISSIONS.USER_DELETE)
        @Delete('/delete/:user')
        async transaction(
            @GetUser() user: IUserDocument,
            @Query('type') type: string
        ): Promise<void> {
            const databaseSession = await this.databaseConnection.startSession();
            databaseSession.startTransaction();
    
            // let assume we already edit our LoggerService too, options second param
            await this.loggerService.info(
                {
                    action: ENUM_LOGGER_ACTION.USER,
                    description: `${user._id} test login with transaction`,
                    user: user._id,
                    tags: ['user', 'deleteUser'],
                },
                databaseSession,
            );
        
            // let assume we already edit our UserService, options second param
            await this.userService.deleteOneById(user._id,
                databaseSession,
            );
    
            await databaseSession.commitTransaction();
            databaseSession.endSession();
        
            return;
        }
    }
    ```

## Migration

ack-nestjs-boilerplate-mongoose use `nestjs-command` package.

Example migrate permission.

1. Create seeding and name as `PermissionSeed`

    ```typescript
    import { Command } from 'nestjs-command';
    import { Injectable } from '@nestjs/common';
    import { ENUM_PERMISSIONS } from 'src/permission/permission.constant';
    import { PermissionBulkService } from 'src/permission/service/permission.bulk.service';

    @Injectable()
    export class PermissionSeed {
        constructor(
            private readonly permissionBulkService: PermissionBulkService
        ) {}

        @Command({
            command: 'insert:permission', // <--- this will be the command to migrate
            describe: 'insert permissions',
        })
        async insert(): Promise<void> {
            const permissions = Object.keys(ENUM_PERMISSIONS).map((val) => ({
                code: val,
                name: val.replace('_', ' '),
            }));

            await this.permissionBulkService.createMany(permissions);
        }
    }
    ```

2. Add `PermissionSeed` into `SeedModule`

    ```typescript
    import { Module } from '@nestjs/common';
    import { CommandModule } from 'nestjs-command';
    import { PermissionModule } from 'src/permission/permission.module';
    import { PermissionSeed } from 'src/database/seeds/permission.seed';

    @Module({
        imports: [
            CoreModule,
            CommandModule,
            PermissionModule,
        ],
        providers: [PermissionSeed],
        exports: [],
    })
    export class SeedsModule {}

    ```

3. Then executed in our terminal

    ```sh
    npx nestjs-command insert:permission
    ```
