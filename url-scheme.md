
# URI 方案

Shadowsocks 遵循 RFC3986 协议，支持标准 URI 方案：

```
SS-URI = "ss://" userinfo "@" hostname ":" port ["/"] ["?"plugin] ["#" tag]
userinfo = websafe-base64-encode-utf8(method ":" password)
```

如果 plugin 存在，最后一个 `/` 不能省略，但如果只有 tag 存在，则是可选的。例如：

```
ss://YmYtY2ZiOnRlc3Q@192.168.100.1:8888/
    ?plugin=url-encoded-plugin-argument-value
    &unsupported-arguments=should-be-ignored
    #Dummy+profile+name.
```

This kind of URIs can be parsed by standard libraries provided by most languages.

For plugin argument, we use the similar format as `TOR_PT_SERVER_TRANSPORT_OPTIONS`, which have the format like

```
simple-obfs;obfs=http;obfs-host=www.baidu.com
```

where colons, semicolons, equal signs and backslashes MUST be escaped with a
backslash.

Examples:

```
ss://YWVzLTEyOC1nY206dGVzdA==@192.168.100.1:8888#Example1
```

```
ss://cmM0LW1kNTpwYXNzd2Q=@192.168.100.1:8888/
    ?plugin=obfs-local%3Bobfs%3Dhttp#Example2
```
