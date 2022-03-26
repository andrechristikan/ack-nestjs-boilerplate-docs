
## Logger

Catch every activity with logger. ack-nestjs-mongoose can catch every activity about users. By default, users' activities will be written into files. Activities in ack-nestjs-mongoose will divide into 3 types.

There have 3 different approaches while doing the implementation.

* `HttpDebuggerModule` can catch everything from the incoming requests.
* `DebuggerModule` is like `console.log` but can write into files.
* `LoggerModule` is a logger that can write into a database
