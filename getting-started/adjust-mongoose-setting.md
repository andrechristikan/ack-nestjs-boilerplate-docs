# Adjust Mongoose Setting

!> Just is case, if you mongodb version is < 5

Go to file `src/database/database.service.ts` and add `useMongoClient` then set value to `true`.

```typescript
...
...
...

if (this.env !== 'production') {
    mongoose.set('debug', this.debug);
}

const mongooseOptions: MongooseModuleOptions = {
    uri,
    useNewUrlParser: true,
    useUnifiedTopology: true,
    serverSelectionTimeoutMS: 5000,
    useMongoClient: true  // <<<<---- add this or uncomment this
};

if (this.user && this.password) {
    mongooseOptions.auth = {
        username: this.user,
        password: this.password,
    };
}

...
...
...
```
