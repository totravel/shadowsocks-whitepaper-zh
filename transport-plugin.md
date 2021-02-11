
# Transport plugin

## Architecture Overview

The plugin of shadowsocks is very similar to the [Pluggable Transport](https://gitweb.torproject.org/torspec.git/tree/pt-spec.txt) plugins from Tor project. Dislike the SOCKS5 proxy design in PT, every SIP003 plugin works as a tunnel (or called local port forwarding). This design aims to avoid per-connection arguments in PT, leading to much easier implementation.

```
+-----------+                    +--------------------------+
| SS Client +-- Local Loopback --+  Plugin Client (Tunnel)  +--+
+-----------+                    +--------------------------+   |
                                                                |
          Public Internet (Obfuscated/Transformed traffic) ==>  |
                                                                |
+-----------+                    +--------------------------+   |
| SS Server +-- Local Loopback --+  Plugin Server (Tunnel)  +--+
+-----------+                    +--------------------------+
```


## Life cycle of a plugin

Very similar to PT, the plugin client/server is started as child process of shadowsocks client/server.

If any error happens, the child process of plugin should exit with a error code. Then, the parent process of shadowsocks stops as well (SIGCHLD). 

When a shadowsocks client/server is stopped by user, the child process of plugin will also be terminated.

## Passing arguments to a plugin

A plugin accepts arguments through environment variables.

- a. Four **MUST-HAVE** environment variables are `SS_REMOTE_HOST`, `SS_REMOTE_PORT`, `SS_LOCAL_HOST` and `SS_LOCAL_PORT`. `SS_REMOTE_HOST` and `SS_REMOTE_PORT` are the hostname and port of the remote plugin service. `SS_LOCAL_HOST` and `SS_LOCAL_PORT` are the hostname and port of the local shadowsocks or plugin service.
- b. One **OPTIONAL** environment variable is `SS_PLUGIN_OPTIONS`. If a plugin requires additional arguments, like path to a config file, these arguments can be passed as extra options in a formatted string. An example is ‘obfs=http;obfs-host=www.baidu.com’, where semicolons, equal signs and backslashes **MUST** be escaped with a backslash.

## Compatibility with PT

For all the plugins from Tor projects, there are two possible ways to support them. 1) We can fork these plugins and modify them to support SIP003, e.g. [obfs4-tunnel](https://github.com/madeye/obfs4-tunnel). 2) Implement a adapter of PT as SIP003 plugin.

## Licenses of plugins

As all plugin services should run in a separate process, they can pick any license they like. There is no GPL restrictions for any plugin providers.

## Restrictions

- a. Plugin over plugin is NOT supported. Only one plugin can be enabled when a shadowsocks service is started. If you really need this feature, implement a plugin-over-plugin transport as a SIP003 plugin.
- b. Only TCP traffic is forwarded. For now, there is no plan to support UDP traffic forwarding.

## Example projects

- A SIP003 plugin for traffic obfuscating: [simple-obfs](https://github.com/shadowsocks/simple-obfs).
- A shadowsocks implementation based on SIP003: [shadowsocks/shadowsockslibev](https://github.com/shadowsocks/shadowsocks-libev).
