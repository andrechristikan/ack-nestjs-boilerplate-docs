# Adjust Mongoose Setting

!> This is optional document

Go to file `src/database/database.service.ts` and add `useMongoClient` then set value to `true`.

```typescript
...
...
...

mongoose.set('debug', this.debug);

const mongooseOptions: MongooseModuleOptions = {
    uri,
    useNewUrlParser: true,
    useUnifiedTopology: true,
    useMongoClient: true  // <<<<---- uncomment this
};


if (this.admin) {
    mongooseOptions.authSource = 'admin';
}

...
...
...
```
