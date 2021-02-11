
# 官方的实现

## 服务器端

- [shadowsocks](https://github.com/shadowsocks/shadowsocks)：最初用 Python 实现的版本。
- [shadowsocks-libev](https://github.com/shadowsocks/shadowsocks-libev)：C 语言实现的版本，轻量，能够用于嵌入式设备和低端设备。很小的占用空间（几兆字节）就能应对于数千个连接。
- [shadowsocks-go](https://github.com/shadowsocks/shadowsocks-go)：Go 语言实现的版本，支持多端口、多密码、用户管理和流量统计等商用需求。
- [go-shadowsocks2](https://github.com/shadowsocks/go-shadowsocks2)：另一个 Go 语言实现的版本，注重核心功能和代码复用。

### 功能比较

 \ | ss  | ss-libev | ss-go | go-ss2
-- | --- | -------- | ----- | ------
TCP Fast Open | Y | Y | N | N
多用户 | Y | Y | Y | N
管理接口 | Y | Y | N | N
中转模式 | N | Y | N | Y
隧道模式 | Y | Y | N | Y
UDP 转发 | Y | Y | Y | Y
AEAD 加密 | Y | Y | N | Y
插件 | N | Y | N | N

## 客户端

- [shadowsocks-android](https://github.com/shadowsocks/shadowsocks-android)：Android 客户端。
- [shadowsocks-windows](https://github.com/shadowsocks/shadowsocks-csharp)：Windows 客户端。
- [shadowsocksX-NG](https://github.com/shadowsocks/ShadowsocksX-NG)：MacOS 客户端。
- [shadowsocks-qt5](https://github.com/shadowsocks/shadowsocks-qt5)：适用于 Windows/MacOS/Linux 的跨平台客户端。

### 功能比较

 \ | ss-win | ssx-ng | ss-qt5 | ss-android
-- | ------ | ------ | ------ | ----------
系统代理 | Y | Y | N | Y
CHNRoutes | Y | Y | N | Y
PAC 配置 | Y | Y | N | N
配置文件切换 | Y | Y | Y | Y
二维码扫描 | Y | Y | Y | Y
二维码生成 | Y | Y | Y | Y
AEAD 加密 | Y | Y | N | Y
插件 | Y | Y | N | Y
