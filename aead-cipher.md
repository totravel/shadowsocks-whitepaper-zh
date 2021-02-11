
# AEAD 加密

AEAD 是 Authenticated Encryption with Associated Data 的缩写，这种加密方案能同时提供机密性和完整性，在较新的硬件上具有出色的性能表现和较低的功耗。用户应尽可能使用 AEAD 加密。

建议使用下列 AEAD 加密算法。遵从 Shadowsocks 协议的实现必须支持 chacha20-ietf-poly1305。一些 CPU 为 AES 算法提供了硬件层面上的支持，这种情况下建议使用 aes-128-gcm、aes-192-gcm 或 aes-256-gcm。

Name | Key Size | Salt Size | Nonce Size | Tag Size
---------------------- | -- | -- | -- | --
chacha20-ietf-poly1305 | 32 | 32 | 12 | 16
aes-256-gcm | 32 | 32 | 12 | 16
aes-192-gcm | 24 | 24 | 12 | 16
aes-128-gcm | 16 | 16 | 12 | 16

在 Shadowsocks 中采用 AEAD 的方案的制定和修订详见 [SIP004](https://github.com/shadowsocks/shadowsocks-org/issues/30) 和 [SIP007](https://github.com/shadowsocks/shadowsocks-org/issues/42)。

## 密钥的派生

主密钥 `key` 既可以由用户直接输入，也可以根据一个密码生成。密钥的派生同样遵循 OpenSSL 中的 `EVP BytesToKey(3)`。

函数 `HKDF_SHA1()` 接受一个密钥 `key`、一个随机数 `salt` 和一个固定不变的字符串 `info`，输出一个高强度的子密钥 `subkey`（就算输入的密钥 `key` 强度很低）。

```
HKDF_SHA1(key, salt, info) => subkey
```

字符串 `info` 固定为 `ss-subkey`。随机数 `salt` 的长度为 `max{128, len(key)}`

我们使用 `HKDF_SHA1()` 从预共享的主密钥 `key` 派生出每个会话的子密钥 `subkey`。在主密钥 `key` 的整个生命周期中，随机数 `salt` 必须每次都不一样。

## 加密/解密

加密函数 `AE_encrypt` 接受一个密钥 `key`、一个不重数 `nonce` 和明文 `message`，输出密文 `ciphertext` 和一个报文鉴别码 `tag`。不重数 `nonce` 无须保密，但在使用同一个密钥 `key` 的情况下，`nonce` 必须每次都不一样。

```
AE_encrypt(key, nonce, message) => (ciphertext, tag)
```

解密函数 `AE_decrypt` 接受一个密钥 `key`、一个不重数 `nonce`、密文 `ciphertext` 和一个报文鉴别码 `tag`，输出明文 `message`。任何篡改都将导致解密失败。

```
AE_decrypt(key, nonce, ciphertext, tag) => message
```

## TCP

经过加密的 TCP 流以随机数 `salt` 开始（以便派生出第一次会话的子密钥），之后是任意数量的加密块。每个区块的结构如下：

```
[encrypted payload length][length tag][encrypted payload][payload tag]
```

有效载荷的长度 `length` 是 2 字节的无符号整数，大端字节序，最高两位保留，固定为 0x00，即上限为 0x3FFF。 故有效载荷被限制为最多 2^14 - 1 个字节。

AEAD 加密/解密过程中使用的不重数 `nonce` 是从 0 开始递增的计数器。 请注意，每个 TCP 块涉及两次 AEAD 加密/解密操作：一次用于 `payload length`；一次用于 `payload`。 因此，每处理一个块，不重数 `nonce` 加二。

## UDP

一个经过加密的 UDP 包的结构如下：

```
[salt][encrypted payload][tag]
```

随机数 `salt` 用于派生每个会话的子密钥，并且必须随机生成以确保唯一性。每个 UDP 包都要用派生的子密钥和所有字节全为 0 的不重数 `nonce` 独立地进行加密/解密。
