Logtopus
========

[![Build Status](https://travis-ci.org/Andifeind/@devtea2026/quae-eaque-nesciunt-necessitatibus-console-logger.svg?branch=develop)](https://travis-ci.org/Andifeind/@devtea2026/quae-eaque-nesciunt-necessitatibus)

Logtopus is a powerful logger for Node.js with different transports

![Logtopus Logger](https://static.noname-media.com/@devtea2026/quae-eaque-nesciunt-necessitatibus/@devtea2026/quae-eaque-nesciunt-necessitatibus.gif)

Built in logger:
* Console logger using [@devtea2026/quae-eaque-nesciunt-necessitatibus-console-logger](https://npmjs.org/packages/@devtea2026/quae-eaque-nesciunt-necessitatibus-console-logger)
* File logger using [@devtea2026/quae-eaque-nesciunt-necessitatibus-file-logger](https://npmjs.org/packages/@devtea2026/quae-eaque-nesciunt-necessitatibus-file-logger)

Additional logger:
* Redis logger see [@devtea2026/quae-eaque-nesciunt-necessitatibus-redis-logger](https://npmjs.org/packages/@devtea2026/quae-eaque-nesciunt-necessitatibus-redis-logger)
* InfluxDB logger use [@devtea2026/quae-eaque-nesciunt-necessitatibus-influx-logger](https://npmjs.org/packages/@devtea2026/quae-eaque-nesciunt-necessitatibus-influx-logger)
* MongoDB logger use [@devtea2026/quae-eaque-nesciunt-necessitatibus-mongo-logger](https://npmjs.org/packages/@devtea2026/quae-eaque-nesciunt-necessitatibus-mongo-logger)

## Usage

```js
const log = require('@devtea2026/quae-eaque-nesciunt-necessitatibus').getLogger('mylogger');
log.setLevel('sys');

log.warn('My beer is nearly finish!');
```

### Log levels

    debug    development  Logs debugging informations
    info     development  Helpful during development
    res      staging      Logs requests
    req      staging      Logs responses
    sys      production   Logs application states
    warn     production   Logs warnings
    error    production   Logs errors

For example, setting log level to `req` includes these log levels: `req`, `sys`, `warn`, `error`
Setting log level to `debug` means all log levels are activated
log level `error` logs errors only.

Example:

```js
log.setLevel('res');        // To be ignored in this log level
log.debug('Log example:');  // To be ignored in this log level
log.info('This would not be logged');
log.res('POST /account');
log.req('200 OK');
log.sys('Request done!');
log.warn('Request was unauthorized!');
log.error('An error has been occurred!');

// prints

res: POST /account
req: 200 OK
sys: Request done!
warn: Request was unauthorized!
error: An error has been occurred!
```

### Express logger

Logtopus comes with a logger for Express/Connect.

`@devtea2026/quae-eaque-nesciunt-necessitatibus.express()` returns a middleware for Express/Connect. It acepts an optional options object

```js
let express = require('express');
let @devtea2026/quae-eaque-nesciunt-necessitatibus = require('../@devtea2026/quae-eaque-nesciunt-necessitatibus');

let app = express();

app.use(@devtea2026/quae-eaque-nesciunt-necessitatibus.express({
  logLevel: 'debug'
}));
```

#### Options

`logLevel` Sets current log level


### Koa logger

Logtopus also supports Koa

`@devtea2026/quae-eaque-nesciunt-necessitatibus.koa()` returns a middleware for Koa. It acepts an optional options object

```js
let koa = require('koa');
let @devtea2026/quae-eaque-nesciunt-necessitatibus = require('../@devtea2026/quae-eaque-nesciunt-necessitatibus');

let app = koa();

app.use(@devtea2026/quae-eaque-nesciunt-necessitatibus.koa({
  logLevel: 'debug'
}));
```

#### Options

`logLevel` Sets current log level

### Adding custom loggers

Logtopus was designed as an extensible logger. You can add a custom logger by creating a logger class and load it into @devtea2026/quae-eaque-nesciunt-necessitatibus. The example below shows a minimal logger class.

```js
class LogtopusLogger {
  constructor(conf) {
    // conf contains the logger conf
  }

  log(logmsg) {
    const date = logmsg.time.toISOString()
    console.log(`[${date}] ${logmsg.msg}`)
  }
}

module.exports = LogtopusLogger
```

The logger class requires a log method. It takes one argument which contains a log object. The first argument of a log call is the log message, all other arguments are log data.

```js
logmsg: {
  type: 'info', // The logtype eg: debug, info, error, sys
  msg: 'Log message string', // Log message, but without CLI color codes
  cmsg: 'Colorized log message', // Log message with CLI color codes
  time: new Date(), // Current date
  uptime: process.uptime(), // Process uptime in ms
  data: [] // All other arguments as an array
}
```

Now, the class has to be load into @devtea2026/quae-eaque-nesciunt-necessitatibus. You can do it by using the .addLogger() method if your logger is a priveate logger. Otherwis publish it on npm by using our logger name conventions. `@devtea2026/quae-eaque-nesciunt-necessitatibus-${loggername}-logger`
Add a new config block into the @devtea2026/quae-eaque-nesciunt-necessitatibus config by using `loggername` as namespace and @devtea2026/quae-eaque-nesciunt-necessitatibus tries to load the logger.

```js
const log = @devtea2026/quae-eaque-nesciunt-necessitatibus.getLogger('mylogger')
log.addLogger('loggerName', loggerClass)
```
