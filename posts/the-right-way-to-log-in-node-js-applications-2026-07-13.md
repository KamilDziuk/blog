# The Right Way to Log in Node.js Applications
2026-07-13

**Tags:**  `web-development` `pino` `node.js` `AWS` `logger` `console.log`   

## Why use Pino?

While `console.log()` is useful during development, production applications should use a dedicated logger. **Pino** provides fast, structured logging with multiple log levels (`info`, `warn`, `error`), making debugging and monitoring much easier.

## 1. Install Pino

```bash
npm install pino
```

## 2. Configure the logger

```js
import pino from "pino";

const logger = pino({
  level: process.env.LOG_LEVEL || "info",
});

export default logger;
```

## 3. Replace `console.log()`

Instead of:

```js
console.log("Server started");
```

use:

```js
logger.info("Server started");
```

Instead of:

```js
console.error(error);
```

use:

```js
logger.error({ err: error }, "Database connection failed");
```

## 4. Common log levels

```js
logger.info("Application started");
logger.warn("Rate limit reached");
logger.error({ err }, "Unexpected error");
```

## 5. AWS Lambda

In AWS Lambda, there is no need to write logs to files. Pino logs are automatically collected by **AWS CloudWatch**, making monitoring and troubleshooting straightforward.

## Conclusion

Replacing `console.log()` with Pino is a simple improvement that makes your application easier to monitor, debug, and maintain. With structured logs and multiple log levels, your backend becomes more production-ready.
