---
id: version-oryOS.11-configuration
title: Configuration
original_id: configuration
---

```yaml
# ORY Keto Configuration
#
#
# !!WARNING!!
# This configuration file is for documentation purposes only. Do not use it in production. As all configuration items
# are enabled, it will not work out of the box either.
#
#
# ORY Keto can be configured using a configuration file and passing the file location using `-c path/to/config.yaml`.
# Per default, ORY Keto will look up and load file ~/.keto.yaml. All configuration keys can be set using environment
# variables as well.
#
# Setting environment variables is easy:
#
## Linux / OSX
#
# $ export MY_ENV_VAR=foo
# $ keto ...
#
# alternatively:
#
# $ MY_ENV_VAR=foo keto ...
#
## Windows
#
### Command Prompt
#
# > set MY_ENV_VAR=foo
# > keto ...
#
### Powershell
#
# > $env:MY_ENV_VAR="foo"
# > keto ...
#
## Docker
#
# $ docker run -e MY_ENV_VAR=foo oryd/keto:...
#
#
# Assuming the following configuration layout:
#
# serve:
#   public:
#     port: 4444
#     something_else: foobar
#
# Key `something_else` can be set as an environment variable by uppercasing it's path:
#   `serve.public.port.somethihng_else` -> `SERVE.PUBLIC.PORT.SOMETHING_ELSE`
# and replacing `.` with `_`:
#   `serve.public.port.somethihng_else` -> `SERVE_PUBLIC_PORT_SOMETHING_ELSE`
#
# Environment variables always override values from the configuration file. Here are some more examples:
#
# Configuration key | Environment variable |
# ------------------|----------------------|
# dsn               | DSN                  |
# serve.admin.host  | SERVE_ADMIN_HOST     |
# ------------------|----------------------|
#
#
# List items such as
#
# secrets:
#   system:
#     - this-is-the-primary-secret
#     - this-is-an-old-secret
#     - this-is-another-old-secret
#
# must be separated using `,` when using environment variables. The environment variable equivalent to the code section#
# above is:
#
# Linux/macOS: $ export SECRETS_SYSTEM=this-is-the-primary-secret,this-is-an-old-secret,this-is-another-old-secret
# Windows: > set SECRETS_SYSTEM=this-is-the-primary-secret,this-is-an-old-secret,this-is-another-old-secret

# log configures the logger
log:
  # Sets the log level, supports "panic", "fatal", "error", "warn", "info" and "debug". Defaults to "info".
  level: info
  # Sets the log format. Leave it undefined for text based log format, or set to "json" for JSON formatting.
  format: json

# serve controls the configuration for the http(s) daemon(s).
serve:
  # The port to listen on. Defaults to 4444
  port: 4444
  # The interface or unix socket ORY Keto should listen and handle public API requests on.
  # Use the prefix "unix:" to specify a path to a unix socket.
  # Leave empty to listen on all interfaces.
  host: localhost # leave this out or empty to listen on all devices which is the default
  # host: unix:/path/to/socket

  # cors configures Cross Origin Resource Sharing for public endpoints.
  cors:
    # set enabled to true to enable CORS. Defaults to false.
    enabled: true
    # allowed_origins is a list of origins (comma separated values) a cross-domain request can be executed from.
    # If the special * value is present in the list, all origins will be allowed. An origin may contain a wildcard (*)
    # to replace 0 or more characters (i.e.: http://*.domain.com). Only one wildcard can be used per origin.
    #
    # If empty or undefined, this defaults to `*`, allowing CORS from every domain (if cors.enabled: true).
    allowed_origins:
      - https://example.com
      - https://*.example.com
    # allowed_methods is list of HTTP methods the user agent is allowed to use with cross-domain
    # requests. Defaults to GET and POST.
    allowed_methods:
      - POST
      - GET
      - PUT
      - PATCH
      - DELETE

    # A list of non simple headers the client is allowed to use with cross-domain requests.
    allowed_headers:
      - Authorization

    # Sets which headers (comma separated values) are safe to expose to the API of a CORS API specification
    exposed_headers:
      - Set-Cookie
      - Content-Type

    # Sets whether the request can include user credentials like cookies, HTTP authentication
    # or client side SSL certificates.
    allow_credentials: true

    # Sets how long (in seconds) the results of a preflight request can be cached. If set to 0, every request
    # is preceded by a preflight request. Defaults to 0.
    max_age: 10

    # If set to true, adds additional log output to debug server side CORS issues. Defaults to false.
    debug: true

  # tls configures HTTPS (HTTP over TLS). If configured, the server automatically supports HTTP/2.
  tls:
    # key configures the private key (pem encoded)
    key:
      # The key can either be loaded from a file:
      path: /path/to/key.pem
      # Or from a base64 encoded (without padding) string:
      base64: LS0tLS1CRUdJTiBFTkNSWVBURUQgUFJJVkFURSBLRVktLS0tLVxuTUlJRkRqQkFCZ2txaGtpRzl3MEJCUTB3...

    # cert configures the TLS certificate (PEM encoded)
    cert:
      # The cert can either be loaded from a file:
      path: /path/to/cert.pem
      # Or from a base64 encoded (without padding) string:
      base64: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tXG5NSUlEWlRDQ0FrMmdBd0lCQWdJRVY1eE90REFOQmdr...

# dsn sets the data source name. This configures the backend where ORY Keto persists data.
#
## In-memory database
#
# If dsn is "memory", data will be written to memory and is lost when you restart this instance.
# You can set this value using the DSN environment variable:
#
## SQL databases
#
# ORY Keto supports popular SQL databases. For more detailed configuration information go to:
# https://www.ory.sh/docs/keto/dependencies-environment#sql
#
### PostgreSQL (recommended)
#
# If dsn is starting with postgres:// PostgreSQL will be used as storage backend:
# dsn: dsn=postgres://user:password@host:123/database
#
### MySQL database
#
# If dsn is starting with mysql:// MySQL will be used as storage backend:
# dsn: mysql://user:password@tcp(host:123)/database
dsn: memory
# dsn: dsn=postgres://user:password@host:123/database
# dsn: mysql://user:password@tcp(host:123)/database

# Enables profiling if set. Use "cpu" to enable cpu profiling and "mem" to enable memory profiling. For more details
# on profiling, head over to: https://blog.golang.org/profiling-go-programs
profiling: cpu
# profiling: mem

# ORY Keto supports distributed tracing.
tracing:
  # Set this to the tracing backend you wish to use. Currently supports jaeger. If omitted or empty, tracing will
  # be disabled.
  provider: jaeger
  # Specifies the service name to use on the tracer.
  service_name: ORY Keto
  providers:
    # Configures the jaeger tracing backend.
    jaeger:
      # The address of the jaeger-agent where spans should be sent to
      local_agent_address: 127.0.0.1:6831
      sampling:
        # The type of the sampler you want to use. Supports:
        # - const
        # - probabilistic
        # - ratelimiting
        type: const
        # The value passed to the sampler type that has been configured.
        # Supported values: This is dependant on the sampling strategy used:
        # - const: 0 or 1 (all or nothing)
        # - rateLimiting: a constant rate (e.g. setting this to 3 will sample requests with the rate of 3 traces per second)
        # - probabilistic: a value between 0..1
        value: 1.0
        # The address of jaeger-agent's HTTP sampling server
        server_url: http://localhost:5778/sampling
```
