# 📝 tslog: Beautiful logging experience for TypeScript and JavaScript

[![lang: Typescript](https://img.shields.io/badge/Language-Typescript-Blue.svg?style=flat-square)](https://www.typescriptlang.org)
![License: MIT](https://img.shields.io/npm/l/tslog?logo=tslog&style=flat-square)
[![npm version](https://img.shields.io/npm/v/tslog?color=76c800&logoColor=76c800&style=flat-square)](https://www.npmjs.com/package/tslog)
![CI: GitHub](https://github.com/fullstack-build/tslog/actions/workflows/ci.yml/badge.svg)
[![codecov.io](https://codecov.io/github/fullstack-build/tslog/coverage.svg?branch=v4)](https://codecov.io/github/fullstack-build/tslog?branch=master)
[![code style: prettier](https://img.shields.io/badge/code_style-prettier-ff69b4.svg?style=flat-square)](https://github.com/prettier/prettier)
[![](https://img.shields.io/static/v1?label=Sponsor&message=%E2%9D%A4&logo=GitHub&color=%23fe8e86)](https://github.com/sponsors/fullstack-build)
[![GitHub stars](https://img.shields.io/github/stars/fullstack-build/tslog.svg?style=social&label=Star)](https://github.com/fullstack-build/tslog)


> Powerful, fast and expressive logging for TypeScript and JavaScript
 
![tslog pretty output](https://raw.githubusercontent.com/fullstack-build/tslog/master/docs/assets/tslog.png "tslog pretty output in browser and Node.js")


## Highlights

⚡ **fast and powerful**<br>
🪶 **Lightweight and flexible**<br>
🏗 **Isomorphic: Works in Browsers and Node.js**<br>
👮‍️ **Fully typed with TypeScript support (native source maps)**<br>
🗃 **_Pretty_ or `{} JSON` output**<br>
📝 **Customizable log level**<br>
⭕️ **Supports _circular_ structures**<br>
🦸 **Custom pluggable loggers**<br>
💅 **Object and error interpolation**<br>
🤓 **Stack trace and pretty errors**<br>
👨‍👧‍👦 **Sub logger with inheritance**<br>
🙊 **Mask/hide secrets and keys**<br>
📦 **ESM with tree shaking support**<br>
✍️ **well documented and tested**<br>

## Example

```typescript
import { Logger } from "tslog";

const log: Logger = new Logger();
log.silly("I am a silly log.");
```

## [Become a Sponsor](https://github.com/sponsors/fullstack-build)
Donations help me allocate more time for my open source work.

[![](https://img.shields.io/static/v1?label=Sponsor&message=%E2%9D%A4&logo=GitHub&color=%23fe8e86)](https://github.com/sponsors/fullstack-build)


## Install

```bash
npm install tslog
```

**Enable TypeScript source map support:**

This feature enables `tslog` to reference a correct line number in your TypeScript source code.

```json5
// tsconfig.json
{
  // ...
  compilerOptions: {
    // ...
    sourceMap: true,
    // we recommend using a current ES version
    target: "es2020",
  },
}
```

And run with:

Node.js with JavaScript:
```bash
node --enable-source-maps
```

Node.js with TypeScript (with ESM support):
```bash
node --enable-source-maps --experimental-specifier-resolution=node --no-warnings --loader ts-node/esm
```

## Simple example

```typescript
import { Logger } from "tslog";

const log: Logger = new Logger({ name: "myLogger" });
log.silly("I am a silly log.");
log.trace("I am a trace log.");
log.debug("I am a debug log.");
log.info("I am an info log.");
log.warn("I am a warn log with a json object:", { foo: "bar" });
log.error("I am an error log.");
log.fatal(new Error("I am a pretty Error with a stacktrace."));
```

## All Features

- **Isomorphic:** Works in browsers and Node.js
- **Tested:** 100% code coverage, CI
- **Super customizable** Every aspect can be overwritten
- **Fully typed:** Written in TypeScript, with native TypeScript support
- **Default log level:** `silly`, `trace`, `debug`, `info`, `warn`, `error`, `fatal` (different colors)
- **Customizable log level:** BaseLogger with configurable log level
- **Pretty & JSON output:** Structured/_pretty_, `JSON` or suppressed output
- **Attachable transports:** Send logs to an external log aggregation services, file system, database, or email/slack/sms/you name it...
- **Minimum log level per output:** `minLevel` level can be set individually per transport
- **Native source maps lookup:** Shows exact position also in TypeScript code (compile-to-JS), one click to IDE position
- **Pretty Error:** Errors and stack traces printed in a structured way and fully accessible through _JSON_ (e.g. external Log services)
- **ES Modules** import syntax with ([tree-shaking](https://webpack.js.org/guides/tree-shaking/))
- **Object/JSON highlighting:** Nicely prints out an objects
- **Sub Logger with inheritance** Powerful sub loggers with settings inheritance, also at runtime
- **Secrets masking:** Prevent passwords and secrets from sneaking into log files by masking them
- **Short paths:** Paths are relative to the root of the application folder
- **Prefixes:** Prefix log messages and bequeath prefixes to child loggers

## API documentation

### <a name="life_cycle"></a>Lifecycle of a log message

Every incoming log message runs through a number of steps before being displayed or handed over to a "transport". Every steps can be overwritten and adjusted.  

![tslog pretty output](https://raw.githubusercontent.com/fullstack-build/tslog/master/docs/assets/tslog_lifesycle.png "tslog: life cycle of a log message")

- **log message** Log message comes in through the `BaseLogger.log()` method
- **mask** If masking is configured, log message gets recursively masked
- **toLogObj** Log message gets transformed into a log object: A default typed log object can be passed to constructor as a second parameter and will be cloned and enriched with the incoming log parameters. Error properties will be handled accordingly. If there is only one log property, and it's an object, both objects (cloned default `logObj` as well as the log property object will be merged.) Are there more than one, they will be pu into properties called "0", "1", ... and so on. Alternatively, log message properties can be put into a property with a name configured with the `argumentsArrayName` setting.  
- **addMetaToLogObj** Additional meta information, like the source code position of the log will be gathered and added to the `_meta` property or any other one configured with the setting `metaProperty`.
- **format** In case of "pretty" configuration, a log object will be formatted based on the templates configured in settings. Meta will be formatted by the method `_prettyFormatLogObjMeta` and the actual log payload will be formatted by `prettyFormatLogObj`. Both steps can be overwritten with the settings `formatMeta` and `formatMeta`. 
- **transport** Last step is to "transport" a log message to every attached transport from the setting `attachedTransports`. Last step is the actual transport, either JSON (`transportJSON`), formatted (`transportFormatted`) or omitted, if its set to "hidden". Both default transports can also be overwritten by the corresponding setting.  

### Default log level

`tslog` comes with default log level `0: silly`, `1: trace`, `2: debug`, `3: info`, `4: warn`, `5: error`, `6: fatal`.

> **Tip:** Each logging method has a return type, which is a _JSON_ representation of the log message (`ILogObject`).

```typescript
import { Logger } from "tslog";

const log: Logger = new Logger();
log.silly("I am a silly log.");
log.trace("I am a trace log.");
log.debug("I am a debug log.");
log.info("I am an info log.");
log.warn("I am a warn log with a json object:", { foo: "bar" });
log.error("I am an error log.");
log.fatal(new Error("I am a pretty Error with a stacktrace."));
```

### Custom log level

In addition to the default log level, custom log level can be defined in the same way `tslog` does it under the hood, by extending the `BaseLogger` and utilizing the `log` method.
`log` method expects the following parameters:
- logLevelId    - Log level ID e.g. 0
- logLevelName  - Log level name e.g. silly
- args          - Multiple log attributes that should be logged out.

> **Tip:** Also the generic logging method (log()) returns a _JSON_ representation of the log message (`ILogObject`).

```typescript
import { BaseLogger, ILogObjMeta, ISettingsParam, ILogObj } from "./BaseLogger";

export class CustomLogger<LogObj> extends BaseLogger<LogObj> {
  constructor(settings?: ISettingsParam<LogObj>, logObj?: LogObj) {
    super(settings, logObj, 5);
  }

  /**
   * Logs a _CUSTOM_ message.
   * @param args  - Multiple log attributes that should be logged out.
   */
  public custom(...args: unknown[]): LogObj & ILogObjMeta {
    return super.log(8, "CUSTOM", ...args);
  }

}
```

### Settings
`tslog` is highly customizable and pretty much every aspect can bei either configured or overwritten. 
A `settings` object is the first parameter passed to the `tslog` constructor:

```typescript 
const logger = new Logger<ILogObj>({ /* SETTINGS */ }, defaultLogObject);
```

#### Type: pretty, json, hidden

- `pretty` **Default setting** prints out a formatted structured "pretty" log entry. 
- `json` prints out a `JSON` formatted log entry.
- `hidden` suppresses any output whatsoever and can be used with attached loggers for example.

```typescript
// pretty
const defaultPrettyLogger = new Logger();

// also pretty
const prettyLogger = new Logger({type: "pretty"});

// JSON
const jsonLogger = new Logger({type: "json"});

// also pretty
const hiddenLogger = new Logger({type: "hidden"});
```

#### minLevel

You can ignore every log message from being processed until a certain severity.
Default severities are:
`0: silly`, `1: trace`, `2: debug`, `3: info`, `4: warn`, `5: error`, `6: fatal`

```typescript

const suppressSilly = new Logger({minLevel: 1 });
suppressSilly.silly("Will be hidden");
suppressSilly.trace("Will be visible");
```

#### argumentsArrayName

`tslog` < 4 wrote all parameters into an arguments array. In `tslog` >= 4 the main object becomes home for all log parameters, and they get merged with the default `logObj`. 
If you still want to put them into a separated parameter, you can do so by defining the `argumentsArrayName`.

```typescript

const logger = new Logger({
  type: "json",
  argumentsArrayName: "argumentsArray",
});
const logMsg = logger.silly("Test1", "Test2");

//logMsg : {
// argumentsArray: [ 'Test1', 'Test2' ],
//   _meta: {
//       [...]
//     }
//   }
// }

```

#### Pretty templates and styles (color settings)
Enables you to overwrite the looks of a formatted _"pretty"_ log message by providing a template string.
Following settings are available for styling:

- **Templates:**
  - `prettyLogTemplate`: template string for logs message. Possible placeholder: 
    - `{{yyyy}}`: year
    - `{{mm}}`: month
    - `{{dd}}`: day
    - `{{hh}}`: hour
    - `{{MM}}`: minute
    - `{{ss}}`: seconds
    - `{{ms}}`: milliseconds
    - `{{dateIsoStr}}`: Shortcut for `{{yyyy}}.{{mm}}.{{dd}} {{hh}}:{{MM}}:{{ss}}:{{ms}}`
    - `{{logLevelName}}`: name of the log level
    - `{{fullFilePath}}`: a full path starting from `/` root
    - `{{filePathWithLine}}`: a full path below the project path with line number
  - `prettyErrorTemplate`: template string for error message. Possible placeholder:
    - `{{errorName}}`: name of the error
    - `{{errorMessage}}`: error message
    - `{{errorStack}}`: Placeholder for all stack lines defined by `prettyErrorStackTemplate` 
  - `prettyErrorStackTemplate`: template string for error stack trace lines. Possible placeholder:
    - `{{fileName}}`: name of the file
    - `{{filePathWithLine}}`: a full path below the project path with line number
    - `{{method}}`: _optional_ name of the invoking method
  - `prettyInspectOptions`: 
  
- **Styling:**
  - `stylePrettyLogs`: defines whether logs should be styled and colorized
  - `prettyLogStyles`: provides colors and styles for different placeholders and can also be dependent on the value (e.g. log level)
    - Level 1: template placeholder (defines a style for a certain template placeholder, s. above, without brackets).
    - Level 2: Either a string with one style (e.g. `white`), or an array of styles (e.g. `["bold", "white"]`), or a nested object with key being a value.  
    - Level 3: Optional nested style based on placeholder values. Key is the value of the template placeholder and value is either a string of a style, or an array of styles (s. above), e.g. `{ SILLY: ["bold", "white"] }` which means: value "SILLY" should get a style of "bold" and "white". `*` means any value other than the defined.
  - `prettyInspectOptions`: When a (potentially nested) object is printed out in Node.js, we use `util.formatWithOptions` under the hood. With `prettyInspectOptions` you can define the output. [Possible values](https://nodejs.org/api/util.html#utilinspectobject-showhidden-depth-colors)

#### Log meta information
`tslog` collects meta information for every log, like runtime, code position etc. The meta information collected depends on the runtime (browser or Node.js) and is accessible through the `LogObj`. 
You can define the property containing this meta information with `metaProperty`, which is "_meta" per default.

#### Pretty templates and styles (color settings)

```typescript

const logger = new Logger({
  prettyLogTemplate: "{{yyyy}}.{{mm}}.{{dd}} {{hh}}:{{MM}}:{{ss}}:{{ms}}\t{{logLevelName}}\t[{{filePathWithLine}}]\t",
  prettyErrorTemplate: "\n{{errorName}} {{errorMessage}}\nerror stack:\n{{errorStack}}",
  prettyErrorStackTemplate: "  • {{fileName}}\t{{method}}\n\t{{filePathWithLine}}",
  stylePrettyLogs: true,
  prettyLogStyles: {
    logLevelName: {
      "*": ["bold", "black", "bgWhiteBright", "dim"],
      SILLY: ["bold", "white"],
      TRACE: ["bold", "whiteBright"],
      DEBUG: ["bold", "green"],
      INFO: ["bold", "blue"],
      WARN: ["bold", "yellow"],
      ERROR: ["bold", "red"],
      FATAL: ["bold", "redBright"],
    },
    dateIsoStr: "white",
    filePathWithLine: "white",
    errorName: ["bold", "bgRedBright", "whiteBright"],
    fileName: ["yellow"],
  },
});

```

#### Masking secrets in logs
One of the most common ways of a password/secret breach is through log files.
Given the central position of `tslog` as the collecting hub of all application logs, it's only natural to use it as a filter.
There are multiple ways of masking secrets, before they get exposed:

- `maskPlaceholder`: Placeholder to replaced masked secrets with, Default: `[***]`
- `maskValuesOfKeys`: Array of keys to replace the values with the placeholder (`maskPlaceholder`). Default: `["password"]`
- `maskValuesOfKeysCaseInsensitive`: Should the keys be matched case insensitive (e.g. "password" would replace "password" as well as "Password", and "PASSWORD"). Default: `false`
- `maskValuesRegEx`: For even more flexibility, you can also replace strings and object values with a RegEx.

#### Prefixing logs
Prefix every log message with an array of additional attributes.<br>
Prefixes propagate to sub loggers and can help to follow a chain of promises.<br>
In addition to <a href="https://nodejs.org/api/async_hooks.html#async_hooks_class_asynclocalstorage" target="_blank">`AsyncLocalStorage`</a>, prefixes can help further distinguish different parts of a request.

> **Hint:** A good example could be a GraphQL request, that by design could consist of multiple queries and/or mutations.

**Example:**

```typescript
const logger = new Logger({
  prefix: ["main-prefix", "parent-prefix"],
});
logger.info("MainLogger message");
// Output:
// main-prefix parent-prefix MainLogger message

const childLogger = logger.getSubLogger({
  prefix: ["child1-prefix"],
});
childLogger.info("child1 message");
// Output:
// main-prefix parent-prefix MainLogger message

const grandchildLogger = childLogger.getSubLogger({
  prefix: ["grandchild1-prefix"],
});
grandchildLogger.silly("grandchild1 message");
// Output:
// main-prefix parent-prefix child1-prefix grandchild1-prefix grandchild1 message
```


#### Attach additional transports

`tslog` focuses on the one thing it does well: capturing logs.
Therefore, there is no build-in _file system_ logging, _log rotation_, or similar.
Per default all logs go to `console`, which can be overwritten (s. below).

However, you can easily attach as many _transports_ as you wish, enabling you to do fancy stuff
like sending messages to _Slack_ or _Telegram_ in case of an urgent error or forwarding them to a log aggregator service.

**Attached transports are also inherited to child loggers.**

##### Simple transport example

Here is a very simple implementation used in our _jest_ tests. 
This example with suppress logs from being send to `console` (`type: "hidden"`) and will instead collect them in an `array`.

```typescript
const transports: any[] = [];
const logger = new Logger({ type: "hidden" });

logger.attachTransport((logObj) => {
  transports.push(logObj);
});

const logMsg = logger.info("Log message");
```

##### Storing logs in a file

Here is an example how to store all logs in a file.

```typescript
import { Logger } from "tslog";
import { appendFileSync } from "fs";

const logger = new Logger();
logger.attachTransport((logObj) => {
  appendFileSync("logs.txt", JSON.stringify(logObj) + "\n");
});

logger.debug("I am a debug log.");
logger.info("I am an info log.");
logger.warn("I am a warn log with a json object:", { foo: "bar" });

```

##### Storing logs in file system with rotating files

If you want to limit the file size of the stored logs, a good practice is to use file rotation, where old logs will be deleted automatically. 
There is a great library called `rotating-file-stream` solving this problem for us and even adding features like compression, file size limit etc.

1. First you need to install this library:  
```bash
  npm i rotating-file-stream
```

2. Combine it with `tslog`:

```typescript
import { Logger } from "tslog";
import { createStream } from "rotating-file-stream";

const stream = createStream("tslog.log", {
  size: "10M", // rotate every 10 MegaBytes written
  interval: "1d", // rotate daily
  compress: "gzip", // compress rotated files
});

const logger = new Logger();
logger.attachTransport((logObj) => {
  stream.write(JSON.stringify(logObject) + "\n");
});

logger.debug("I am a debug log.");
logger.info("I am an info log.");
logger.warn("I am a warn log with a json object:", { foo: "bar" });

```

#### Overwriting default behavior 

One of the key advantages of `tslog` >= 4 is that you can overwrite pretty much every aspect of the log processing described in <a href="#life_cycle">"Lifecycle of a log message"</a>.

For every log:
```typescript
    const logger = new Logger({
  overwrite: {
    mask: (args: unknown[]): unknown[] => {
      // mask and return an array of log attributes for further processing
    },
    toLogObj: (args: unknown[], clonesLogObj?: LogObj): unknown => {
      // convert the log attributes to a LogObj and return it
    },
    addMeta: (logObj: any, logLevelId: number, logLevelName: string) => {
      // add meta information to the LogObj and return it
    }

  },
});
```

For `pretty` logs log:
```typescript
    const logger = new Logger({
      type: "pretty",
      overwrite: {
        formatMeta: (meta?: IMeta) => {
          // format LogObj meta object to a string and return it
        },
        formatLogObj: <LogObj>(maskedArgs: unknown[], settings: ISettings<LogObj>) => {
            // format LogObj attributes to a string and return it
        },
        transportFormatted: (logMetaMarkup: string, logArgs: unknown[], logErrors: string[], settings: unknown) => {
          // overwrite the default transport for formatted (e.g. pretty) log levels. e.g. replace console with StdOut, write to file etc. 
        },
      },
    });
```

For `JSON` logs log (no formatting happens here):
```typescript
    const logger = new Logger({
      type: "pretty",
      overwrite: {
        transportJSON: (logObjWithMeta: any) => {
          // transport the LogObj to console, StdOut, a file or an external service
        },
      },
    });
```

### Defining and accessing `logObj`
As described in <a href="#life_cycle">"Lifecycle of a log message"</a>, every log message goes through some lifecycle steps and becomes an object representation of the log with the name `logObj`.
A default logObj can be passed to the `tslog` constructor and will be cloned and merged into the log message. This makes `tslog` >= 4 highly configurable and easy to integrate into any 3rd party service.
The entire `logObj` will be printed out in `JSON` mode and also returned by every log method.

> **Tip:** All properties of the default `LogObj` containing function calls will be executed for every log message making use cases possible like `requestId` (s. below). 

```typescript
interface ILogObj {
    foo: string;
}

const defaultLogObject: ILogObj = {
  foo: "bar",
};

const logger = new Logger<ILogObj>({ type: "json" }, defaultLogObject);
const logMsg = logger.info("Test"); 

// logMsg: {
//  '0': 'Test',
//  foo: 'bar',
//  _meta: {
//    runtime: 'Nodejs',
//    hostname: 'Eugenes-MBP.local',
//    date: 2022-10-23T10:51:08.857Z,
//    logLevelId: 3,
//    logLevelName: 'INFO',
//    path: {
//      fullFilePath: 'file:///[...]/tslog/examples/nodejs/index.ts:113:23',
//      fileName: 'index.ts',
//      fileColumn: '23',
//      fileLine: '113',
//      filePath: '/examples/nodejs/index.ts',
//      filePathWithLine: '/examples/nodejs/index.ts:113'
//    }
//  }
//}
```


### Tip: RequestID: Mark a request (e.g. HTTP) call with AsyncLocalStorage and `tslog`
>**Node.js 13.10 introduced a new feature called <a href="https://nodejs.org/api/async_hooks.html#async_hooks_class_asynclocalstorage" target="_blank">AsyncLocalStorage.</a>**<br>

**❗ Keep track of all subsequent calls and promises originated from a single request (e.g. HTTP).**

In a real world application a call to an API would lead to many logs produced across the entire application.
When debugging it can get quite handy to be able to group all logs based by a unique identifier, e.g.  `requestId`.

Some provides (e.g. `Heroku`) already set a `X-Request-ID` header, which we are going to use or fallback to a short ID generated by <a href="https://www.npmjs.com/package/nanoid" target="_blank">`nanoid`</a>.

**In this example every subsequent logger is a sub logger of the main logger and thus inherits all of its settings making `requestId` available throughout the entire application without any further ado.**

`tslog` works with any API framework (like `Express`, `Koa`, `Hapi` and so on), but in this example we are using `Koa`.
```typescript
  import { AsyncLocalStorage } from "async_hooks";
  import Koa from "koa";
  import { customAlphabet } from "nanoid";
  import { Logger } from "tslog";
  
  interface ILogObj {
    requestId?: string | (() => string | undefined);
  }
  
  const asyncLocalStorage: AsyncLocalStorage<{ requestId: string }> = new AsyncLocalStorage();
  
  const defaultLogObject: ILogObj = {
    requestId: () => asyncLocalStorage.getStore()?.requestId,
  };
  
  const logger = new Logger<ILogObj>({ type: "json" }, defaultLogObject);
  export { logger };
  
  logger.info("Test log without requestId");
  
  const koaApp = new Koa();
  
  /** START AsyncLocalStorage requestId middleware **/
  koaApp.use(async (ctx: Koa.Context, next: Koa.Next) => {
    // use x-request-id or fallback to a nanoid
    const requestId: string = (ctx.request.headers["x-request-id"] as string) ?? customAlphabet("1234567890abcdefghijklmnopqrstuvwxyz", 6)();
    // every other Koa middleware will run within the AsyncLocalStorage context
    await asyncLocalStorage.run({ requestId }, async () => {
      return next();
    });
  });
  /** END AsyncLocalStorage requestId middleware **/
  
  // example middleware
  koaApp.use(async (ctx: Koa.Context, next) => {
      
    // log request
    logger.silly({ originalUrl: ctx.originalUrl, status: ctx.response.status, message: ctx.response.message });
    
    // also works with a sub logger
    const subLogger = logger.getSubLogger();
    subLogger.info("Log containing requestId"); // <-- will contain a requestId
  
    return await next();
  });
  
  koaApp.listen(3000);
  
  logger.info("Server running on port 3000");
```
