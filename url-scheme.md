
# URI Scheme

Shadowsocks supports a standard URI scheme, following RFC3986:

`SS-URI = "ss://" userinfo "@" hostname ":" port ["/"] ["?"plugin] ["#" tag]
userinfo = websafe-base64-encode-utf8(method ":" password)`

The last / should be appended if plugin is present, but is optional if only tag is present. For example:

```
ss://YmYtY2ZiOnRlc3Q@192.168.100.1:8888/
    ?plugin=url-encoded-plugin-argument-value
    &unsupported-arguments=should-be-ignored
    #Dummy+profile+name.
```

This kind of URIs can be parsed by standard libraries provided by most languages.

For plugin argument, we use the similar format as `TOR_PT_SERVER_TRANSPORT_OPTIONS`, which have the format like

`simple-obfs;obfs=http;obfs-host=www.baidu.com`

where colons, semicolons, equal signs and backslashes MUST be escaped with a
backslash.

Examples:

`ss://YWVzLTEyOC1nY206dGVzdA==@192.168.100.1:8888#Example1`

```
ss://cmM0LW1kNTpwYXNzd2Q=@192.168.100.1:8888/
    ?plugin=obfs-local%3Bobfs%3Dhttp#Example2
```
