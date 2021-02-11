
# 流加密

流加密只能提供机密性，不能保证数据的完整性。用户应尽可能使用 AEAD 加密。

下列流加密算法可提供一定的机密性。

名称 | 密钥长度 | 初始向量长度
----------- | -- | --
aes-128-ctr | 16 | 16
aes-192-ctr | 24 | 16
aes-256-ctr | 32 | 16
aes-128-cfb | 16 | 16
aes-192-cfb | 24 | 16
aes-256-cfb | 32 | 16
camellia-128-cfb | 16 | 16
camellia-192-cfb | 24 | 16
camellia-256-cfb | 32 | 16
chacha20-ietf | 32 | 12

## 加密/解密

加密函数 `Stream_encrypt` 接受一个密钥 `key`、一个初始化向量 `IV` 和明文 `message`，输出与 `message` 等长的密文 `ciphertext`。

```
Stream_encrypt(key, IV, message) => ciphertext
```

解密函数 `Stream_decrypt` 接受同样的密钥 `key`、初始化向量 `IV` 以及密文 `ciphertext`，输出明文 `message`。

```
Stream_decrypt(key, IV, ciphertext) => message
```

密钥 `key` 既可以由用户直接输入，也可以由密码生成。密钥的派生遵循 OpenSSL 中的 `EVP BytesToKey(3)`。

## TCP

TCP 流的开头是随机生成的初始化向量 `IV`，之后是加密的有效载荷。

```
[IV][encrypted payload]
```

## UDP

一个经过流加密 UDP 包结构如下：

```
[IV][encrypted payload]
```

不同 UDP 包的加密/解密，不能共用一个初始化向量 `IV`，每个 UDP 包都要单独生成一个初始化向量。
