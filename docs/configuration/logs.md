# Logs Definition

## Reference

### TOML

```toml
logLevel = "INFO"

[traefikLog]
  filePath = "/path/to/traefik.log"
  format   = "json"

[accessLog]
  filePath = "/path/to/access.log"
  format = "json"

  [accessLog.filters]
    statusCodes = ["200", "300-302"]

  [accessLog.fields]
    defaultMode = "keep"
    [accessLog.fields.names]
      "ClientUsername" = "drop"
      # ...

    [accessLog.fields.headers]
      defaultMode = "keep"
      [accessLog.fields.headers.names]
        "User-Agent" = "redact"
        "Authorization" = "drop"
        "Content-Type" = "keep"
        # ...
```

### CLI

For more information about the CLI, see the documentation about [Traefik command](/basics/#traefik).

```shell
--logLevel="DEBUG"
--traefikLog.filePath="/path/to/traefik.log"
--traefikLog.format="json"
--accessLog.filePath="/path/to/access.log"
--accessLog.format="json"
--accessLog.filters.statusCodes="200,300-302"
--accessLog.fields.defaultMode="keep"
--accessLog.fields.names="Username=drop Hostname=drop"
--accessLog.fields.headers.defaultMode="keep"
--accessLog.fields.headers.names="User-Agent=redact Authorization=drop Content-Type=keep"
```


## Traefik Logs

By default the Traefik log is written to stdout in text format.

To write the logs into a log file specify the `filePath`:
```toml
[traefikLog]
  filePath = "/path/to/traefik.log"
```

To write JSON format logs, specify `json` as the format:
```toml
[traefikLog]
  filePath = "/path/to/traefik.log"
  format   = "json"
```


Deprecated way (before 1.4):

!!! danger "DEPRECATED"
    `traefikLogsFile` is deprecated, use [traefikLog](/configuration/logs/#traefik-logs) instead.

```toml
# Traefik logs file
# If not defined, logs to stdout
#
# DEPRECATED - see [traefikLog] lower down
# In case both traefikLogsFile and traefikLog.filePath are specified, the latter will take precedence.
# Optional
#
traefikLogsFile = "log/traefik.log"
```

To customize the log level:
```toml
# Log level
#
# Optional
# Default: "ERROR"
#
# Accepted values, in order of severity: "DEBUG", "INFO", "WARN", "ERROR", "FATAL", "PANIC"
# Messages at and above the selected level will be logged.
#
logLevel = "ERROR"
```


## Access Logs

Access logs are written when `[accessLog]` is defined.
By default it will write to stdout and produce logs in the textual Common Log Format (CLF), extended with additional fields.

To enable access logs using the default settings just add the `[accessLog]` entry:
```toml
[accessLog]
```

To write the logs into a log file specify the `filePath`:
```toml
[accessLog]
filePath = "/path/to/access.log"
```

To write JSON format logs, specify `json` as the format:
```toml
[accessLog]
filePath = "/path/to/access.log"
format = "json"
```

To filter logs by status code:
```toml
[accessLog]
filePath = "/path/to/access.log"
format = "json"

  [accessLog.filters]

  # statusCodes keep only access logs with status codes in the specified range
  #
  # Optional
  # Default: []
  #
  statusCodes = ["200", "300-302"]
```

To customize logs format:
```toml
[accessLog]
filePath = "/path/to/access.log"
format = "json"

  [accessLog.filters]

  # statusCodes keep only access logs with status codes in the specified range
  #
  # Optional
  # Default: []
  #
  statusCodes = ["200", "300-302"]

  [accessLog.fields]

  # defaultMode
  #
  # Optional
  # Default: "keep"
  #
  # Accepted values "keep", "drop"
  #
  defaultMode = "keep"

  # Fields map which is used to override fields defaultMode
  [accessLog.fields.names]
    "ClientUsername" = "drop"
    # ...

  [accessLog.fields.headers]
    # defaultMode
    #
    # Optional
    # Default: "keep"
    #
    # Accepted values "keep", "drop", "redact"
    #
    defaultMode = "keep"
    # Fields map which is used to override headers defaultMode
    [accessLog.fields.headers.names]
      "User-Agent" = "redact"
      "Authorization" = "drop"
      "Content-Type" = "keep"
      # ...
```

#### List of all available fields

```ini
StartUTC
StartLocal
Duration
FrontendName
BackendName
BackendURL
BackendAddr
ClientAddr
ClientHost
ClientPort
ClientUsername
RequestAddr
RequestHost
RequestPort
RequestMethod
RequestPath
RequestProtocol
RequestLine
RequestContentSize
OriginDuration
OriginContentSize
OriginStatus
OriginStatusLine
DownstreamStatus
DownstreamStatusLine
DownstreamContentSize
RequestCount
GzipRatio
Overhead
RetryAttempts
```

Deprecated way (before 1.4):

!!! danger "DEPRECATED"
    `accessLogsFile` is deprecated, use [accessLog](/configuration/logs/#access-logs) instead.

```toml
# Access logs file
#
# DEPRECATED - see [accessLog]
#
accessLogsFile = "log/access.log"
```

## Log Rotation

Traefik will close and reopen its log files, assuming they're configured, on receipt of a USR1 signal.
This allows the logs to be rotated and processed by an external program, such as `logrotate`.

!!! note
    This does not work on Windows due to the lack of USR signals.
