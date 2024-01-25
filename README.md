# history-elastic

A plugin for logging to Elasticsearch with predefined fields.

## Prerequisites

This plugin uses [http-bulk](https://github.com/halon-extras/http-bulk) queues. All exported functions uses "elastic" as default queue ID.

## Installation

Follow the [instructions](https://docs.halon.io/manual/comp_install.html#installation) in our manual to add our package repository and then run the below command.

### Ubuntu

```
apt-get install halon-extras-history-elastic
```

### RHEL

```
yum install halon-extras-history-elastic
```

## Exported functions for delivery attempts

Implement the HSL code to the [Post-delivery](https://docs.halon.io/hsl/postdelivery.html) script hook.

### log_delivery_attempt($args [, $opts])

**Params**

- args `array` - Log data
- opts `array` - Options array

**Returns**

- `boolean` - Whether it was successfully queued to http-bulk

The following options are available in the **args** array.

- arguments `array` - Use the predefined context variable $arguments
- message `array` - Use the predefined context variable $message
- class `string` - Bounce category (optional)
- redact `boolean` - Redacts the local-part of the email address for both sender and recipient (optional)
- custom `array` - Used for additional logging (optional)

The following options are available in the optional **opts** array.

- indexname `string` - Elasticsearch index pattern
- id `string` - The http-bulk ID

```
import { log_delivery_attempt } from "extras://history-elastic";

log_delivery_attempt([
    "arguments" => $arguments,
    "message" => $message
]);
```

## Exported functions for transactions

The following code is only examples and needs to be added before calling the built-in message actions (e.g. defer, reject, and queue). The `action` and `reason` array keys needs to be changed to reflect what happened with the message.

Implement the HSL code to the [RCPT TO](https://docs.halon.io/hsl/rcptto.html), [EOD](https://docs.halon.io/hsl/eod.html) and [Post-delivery](https://docs.halon.io/hsl/postdelivery.html) script hook.

### log_rcptto($args [, $opts])

**Params**

- args `array` - Log data
- opts `array` - Options array

**Returns**

- `boolean` - Whether it was successfully queued to http-bulk

The following options are available in the **args** array.

- action `string` -  Message action
- reason `string` - The reason for the specific action
- custom `array` - Used for additional logging (optional)
- arguments `array` - Use the predefined context variable $arguments
- connection `array` - Use the predefined context variable $connection
- transaction `array` - Use the predefined context variable $transaction

The following options are available in the optional **opts** array.

- indexname `string` - Elasticsearch index pattern
- indexrotate `string` - Elasticsearch index date rotation
- id `string` - The http-bulk ID

```
import { log_rcptto } from "extras://history-elastic";

log_rcptto([
    "action" => "REJECT",
    "reason" => "Message was rejected",
    "arguments" => $arguments,
    "connection" => $connection,
    "transaction" => $transaction
]);
```

---

### log_eod($args [, $opts])

**Params**

- args `array` - Log data
- opts `array` - Options array

**Returns**

- `boolean` - Whether it was successfully queued to http-bulk

The following options are available in the **args** array.

- action `string` -  Message action
- reason `string` - The reason for the specific action
- recipient `array` -  The recipient (https://docs.halon.io/hsl/eod.html#recipient)
- queueid `number` - Queue ID
- custom `array` - Used for additional logging
- arguments `array` - Use the predefined context variable $arguments
- connection `array` - Use the predefined context variable $connection
- transaction `array` - Use the predefined context variable $transaction

The following options are available in the optional **opts** array.

- indexname `string` - Elasticsearch index pattern
- indexrotate `string` - Elasticsearch index date rotation
- id `string` - The http-bulk ID

```
import { log_eod } from "extras://history-elastic";

log_eod([
    "action" => "QUEUE",
    "reason" => "",
    "recipient" => $recipient,
    "queueid" => 1,
    "arguments" => $arguments,
    "connection" => $connection,
    "transaction" => $transaction
]);
```

---

### log_predelivery($args [, $opts])

Use this function if it is necessary to add custom fields before the delivery attempt.

**Params**

- args `array` - Log data
- opts `array` - Options array

**Returns**

- `boolean` - Whether it was successfully queued to http-bulk

The following options are available in the **args** array.

- message `array` - Use the predefined context variable $message
- custom `array` - Used for additional logging

The following options are available in the optional **opts** array.

- indexname `string` - Elasticsearch index pattern
- indexrotate `string` - Elasticsearch index date rotation
- id `string` - The http-bulk ID

```
import { log_predelivery } from "extras://history-elastic";

log_predelivery([
    "custom" => [
        "hello" => "world"
    ],
    "message" => $message
]);
```

---

### log_postdelivery($args [, $opts])

**Params**

- args `array` - Log data
- opts `array` - Options array

**Returns**

- `boolean` - Whether it was successfully queued to http-bulk

The following options are available in the **args** array.

- arguments `array` - Use the predefined context variable $arguments
- message `array` - Use the predefined context variable $message
- custom `array` - Used for additional logging

The following options are available in the optional **opts** array.

- indexname `string` - Elasticsearch index pattern
- indexrotate `string` - Elasticsearch index date rotation
- id `string` - The http-bulk ID

```
import { log_postdelivery } from "extras://history-elastic";

log_postdelivery([
    "arguments" => $arguments,
    "message" => $message
]);
```
