
# 简介

Shadowsocks 是由两部分组成的加密代理，由 [SOCKS5](https://tools.ietf.org/html/rfc1928) 改造而来。

```
客户端 <---> ss-local <--[加密]--> ss-remote <---> 目标服务器
```

Shadowsocks 的本地组件（ss-local）的作用类似于一般的 SOCKS5 服务器，为客户机提供代理服务。它将客户端的数据流和数据包加密并转发给 Shadowsocks 的远端组件（ss-remote），后者再将收到数据解密并转发给目标服务器。同样，来自目标服务器的应答则会被 ss-remote 加密并转发给 ss-local，再由 ss-local 解密并最终返回给客户端。

## 地址

Shadowsocks 沿用 [SOCKS5 的地址格式](https://tools.ietf.org/html/rfc1928#section-5)：

```
[1-byte type][variable-length host][2-byte port]
```

其中的 type 字段有如下定义：

- 0x01：host 是 4 字节的 IPv4 地址。
- 0x03：host 是长度可变的字符串，开头第一个字节是域名的长度，之后是不超过 255 字节的域名。
- 0x04：host 是 16 字节的 IPv6 地址。

端口号是 2 字节的无符号数，大端字节序。

## TCP

ss-local 和 ss-remote 建立 TCP 连接后，发送的是加密的数据流（一开始是目标地址，之后就都是有效载荷）。 至于如何加密则取决于所用的加密算法。

```
[target address][payload]
```

ss-remote 接收加密的数据流，解密并解析前导的目标地址。得到目标服务器的地址后，和目标服务器建立 TCP 连接，并向其转发有效载荷。ss-remote 从目标接收到应答后，加密并转发回 ss-local，直到 ss-local 断开连接。

## UDP

ss-local 将包含目标地址和有效载荷的数据包加密后发送给 ss-remote。

```
[target address][payload]
```

ss-remote 在收到加密的数据包后，解密并解析出目标地址。得到目标服务器的地址后，向目标服务器发送一个只包含有效载荷的数据包。ss-remote 从目标服务器接收数据包，在每个数据包的开头插入目标地址后，一并加密再发送回 ss-local。

```
[target address][payload]
```

本质上，ss-remote 是在为 ss-local 做网络地址转换。
