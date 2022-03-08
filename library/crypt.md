  `crypt`是一个基于`OpenSSL`编写的`Lua`加密套件库.

## 一、常用算法

---

  导入库: `local crypt = require "crypt"`

### 1. 冗余校验算法

---

  函数原型: `crypt.crc32(text)`

  计算`text`参数的`crc32`值, 此方法返回一个`uint32_t`类型的值.

---

  函数原型: `crypt.crc64(text)`

  计算`text`参数的`crc64`值, 此方法返回一个`String`类型的值.

### 2. 信息摘要算法

---

  函数原型: `crypt.md5(text, hex)`

  计算`text`参数的`md5`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.sha1(text, hex)`

  计算`text`参数的`sha1`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.sha128(text, hex)`

  计算`text`参数的`sha128`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.sha224(text, hex)`

  计算`text`参数的`sha224`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.sha256(text, hex)`

  计算`text`参数的`sha256`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.sha384(text, hex)`

  计算`text`参数的`sha384`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.sha512(text, hex)`

  计算`text`参数的`sha512`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

### 3. 消息认证码算法

---

  函数原型: `crypt.hmac_md5(key, text, hex)`

  计算`key`与`text`参数的`hmac_md5`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.hmac_sha1(key, text, hex)`

  计算`key`与`text`参数的`hmac_sha1`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.hmac_sha128(key, text, hex)`

  计算`key`与`text`参数的`hmac_sha128`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

<!-- ---

  函数原型: `crypt.hmac_sha224(key, text, hex)`

  计算`key`与`text`参数的`hmac_sha224`值, 此方法返回一个`String`类型的值.

  `hex`参数为`true`, 则会将返回值经过`hex`编码; -->

---

  函数原型: `crypt.hmac_sha256(key, text, hex)`

  计算`key`与`text`参数的`hmac_sha256`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

<!-- ---

  函数原型: `crypt.hmac_sha384(key, text, hex)`

  计算`key`与`text`参数的`hmac_sha384`值, 此方法返回一个`String`类型的值.

  `hex`参数为`true`, 则会将返回值经过`hex`编码; -->

---

  函数原型: `crypt.hmac_sha512(key, text, hex)`

  计算`key`与`text`参数的`hmac_sha512`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

### 4. 特殊编码算法

---

  函数原型: `crypt.hexencode(text)`

  将`text`参数进行`16进制`编码, 此方法返回一个`String`类型的值.

---

  函数原型: `crypt.hexdecode(text)`

  将`text`参数进行`16进制`解码, 此方法返回一个`String`类型的值.

---

  函数原型: `crypt.base64encode(text)`

  将`text`参数进行`base64`编码, 此方法返回一个`String`类型的值.

---

  函数原型: `crypt.base64decode(text)`

  将`text`参数进行`base64`解码, 此方法返回一个`String`类型的值.

---

  函数原型: `crypt.base64urlencode(text)`

  将`text`参数进行`base64`编码, 此方法返回一个`String`类型的值.

---

  函数原型: `crypt.base64urldecode(text)`

  将`text`参数进行`base64`解码, 此方法返回一个`String`类型的值.

---

  函数原型: `crypt.urlencode(text)`

  将`text`参数进行`urlencode`编码, 此方法返回一个`String`类型的值.

---

  函数原型: `crypt.urldecode(text)`

  将`text`参数进行`urldecode`解码, 此方法返回一个`String`类型的值.

### 5. 对称加密算法

---

  函数原型: `crypt.desencode(key, text)`

  使用`key`参数对指定的`text`进行加密, 此方法返回加密成功后的值(`String`类型).

---

  函数原型: `crypt.desdecode(key, ciphertext)`

  使用`key`参数对指定的`ciphertext`进行加密, 此方法返回解密成功后的值(`String`类型).

### 6. 非对称协商算法

---

  函数原型: `crypt.dhexchange(text)`

  根据`text`参数生成一个挑战字符串, 并且返回整个字符串.

---

  函数原型: `crypt.dhsecret(challenge, text)`

  将`challenge`参数与`text`进行计算, 此方法返回一个`String`类型的值.

### 7. 公钥加密与私钥解密

---

  函数原型: `crypt.rsa_public_key_encode(text, public_key_path, b64)`

  使用`public_key_path`提供的公钥文件对`text`参数提供的内容进行加密;

  如果传递的`b64`参数为`true`, 则将加密的结果以`base64`编码返回; (填充方式: `RSA_PKCS1_PADDING`);

---

  函数原型: `crypt.rsa_public_key_oaep_padding_encode(text, public_key_path, b64)`

  使用`public_key_path`提供的公钥文件对`text`参数提供的内容进行加密;

  如果传递的`b64`参数为`true`, 则将加密的结果以`base64`编码返回; (填充方式: `RSA_PKCS1_OAEP_PADDING`);

---

  函数原型: `crypt.rsa_private_key_decode(ciphertext, private_key_path, b64)`

  使用`private_key_path`提供的私钥文件对`ciphertext`参数提供的密文进行解密;

  如果传递的`b64`参数为`true`, 则会在解密前先对密文进行`base64`解码; (填充方式: `RSA_PKCS1_PADDING`);

---

  函数原型: `crypt.rsa_private_key_oaep_padding_decode(ciphertext, private_key_path, b64)`

  使用`private_key_path`提供的私钥文件对`ciphertext`参数提供的内容进行加密;

  如果传递的`b64`参数为`true`, 则会在解密前先对密文进行`base64`解码; (填充方式: `RSA_PKCS1_OAEP_PADDING`);

### 8. 私钥加密与公钥解密

---

  函数原型: `crypt.rsa_private_key_encode(text, private_key_path, b64)`

  使用`private_key_path`提供的私钥文件对`text`参数提供的内容进行加密;

  如果传递的`b64`参数为`true`, 则将加密的结果以`base64`编码返回; (填充方式: `RSA_PKCS1_PADDING`);

---

  函数原型: `crypt.rsa_private_key_oaep_padding_encode(text, private_key_path, b64)`

  使用`private_key_path`提供的私钥文件对`text`参数提供的内容进行加密;

  如果传递的`b64`参数为`true`, 则将加密的结果以`base64`编码返回; (填充方式: `RSA_PKCS1_OAEP_PADDING`);

---

  函数原型: `crypt.rsa_public_key_decode(ciphertext, public_key_path, b64)`

  使用`public_key_path`提供的公钥文件对`ciphertext`参数提供的内容进行解密;

  如果传递的`b64`参数为`true`, 则会在解密前先对密文进行`base64`解码; (填充方式: `RSA_PKCS1_PADDING`);

---

  函数原型: `crypt.rsa_public_key_oaep_padding_decode(ciphertext, public_key_path, b64)`

  使用`public_key_path`提供的公钥文件对`ciphertext`参数提供的内容进行解密;

  如果传递的`b64`参数为`true`, 则会在解密前先对密文进行`base64`解码; (填充方式: `RSA_PKCS1_OAEP_PADDING`);


### 9. 签名与验签算法

---

  签名验签的`algorithm`参数必须一致, 并且`algorithm`是`String`类型.

  当前支持的`algorithm`参数只有: `md5`, `sha1`, `sha128`, `sha256`, `sha512`.

---

  函数原型: `crypt.rsa_sign(text, private_key_path, algorithm, hex)`

  使用参数`private_key_path`提供的私钥文件对`text`参数内容进行签名, 签名使用的算法由`algorithm`参数提供;

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.rsa_verify(text, public_key_path, signtext, algorithm, hex)`

  使用参数`public_key_path`提供的公钥文件对`text`与`signtext`内容验签, 验签使用的算法由`algorithm`参数提供;

  当`hex`参数为`true`的时候, 会先对`signtext`解除`十六进制`编码;

### 10. 高级加密算法

---

  密码学中的高级加密标准(Advanced Encryption Standard), 又称`Rijndael`加密法，是美国联邦政府采用的一种区块加密标准。

  高级加密标准由美国国家标准与技术研究院(NIST)于`2001`年`11`月`26`日发布于`FIPS PUB 197`，并在`2002`年`5`月`26`日成为有效的标准。

  这个标准用来替代原先的DES（Data Encryption Standard），已经被多方分析且广为全世界所使用。

  `2006`年，高级加密标准已然成为对称密钥加密中最流行的算法之一。


#### 10.1 CBC模式

---

  函数原型: `crypt.aes_128_cbc_encrypt(key, text, iv, hex)`

  使用`CBC`模式对参数`text`进行加密; `key`参数提供`128`位的密钥, `iv`参数提供向量`IV`;

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.aes_128_cbc_decrypt(key, ciphertext, iv, hex)`

  使用`CBC`模式对参数`ciphertext`进行解密; `key`参数提供`128`位的密钥, `iv`参数提供向量`IV`; 

  当`hex`参数为`true`的时候, 会先对`ciphertext`解除`十六进制`编码;

---

  函数原型: `crypt.aes_192_cbc_encrypt(key, text, iv, hex)`

  使用`CBC`模式对参数`text`进行加密; `key`参数提供`192`位的密钥, `iv`参数提供向量`IV`;

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.aes_192_cbc_decrypt(key, ciphertext, iv, hex)`

  使用`CBC`模式对参数`ciphertext`进行解密; `key`参数提供`192`位的密钥, `iv`参数提供向量`IV`; 

  当`hex`参数为`true`的时候, 会先对`ciphertext`解除`十六进制`编码;

---

  函数原型: `crypt.aes_256_cbc_encrypt(key, text, iv, hex)`

  使用`CBC`模式对参数`text`进行加密; `key`参数提供256位的密钥, `iv`参数提供向量`IV`;

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.aes_256_cbc_decrypt(key, ciphertext, iv, hex)`

  使用`CBC`模式对参数`ciphertext`进行解密; `key`参数提供256位的密钥, `iv`参数提供向量`IV`; 

  当`hex`参数为`true`的时候, 会先对`ciphertext`解除`十六进制`编码;

#### 10.2 ECB模式

---

  函数原型: `crypt.aes_128_ecb_encrypt(key, text, iv, hex)`

  使用`ECB`模式对参数`text`进行加密; `key`参数提供`128`位的密钥, `iv`参数提供向量`IV`;

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.aes_128_ecb_decrypt(key, ciphertext, iv, hex)`

  使用`ECB`模式对参数`ciphertext`进行解密; `key`参数提供`128`位的密钥, `iv`参数提供向量`IV`; 

  当`hex`参数为`true`的时候, 会先对`ciphertext`解除`十六进制`编码;

---

  函数原型: `crypt.aes_192_ecb_encrypt(key, text, iv, hex)`

  使用`ECB`模式对参数`text`进行加密; `key`参数提供`192`位的密钥, `iv`参数提供向量`IV`;

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.aes_192_ecb_decrypt(key, ciphertext, iv, hex)`

  使用`ECB`模式对参数`ciphertext`进行解密; `key`参数提供`192`位的密钥, `iv`参数提供向量`IV`; 

  当`hex`参数为`true`的时候, 会先对`ciphertext`解除`十六进制`编码;

---

  函数原型: `crypt.aes_256_ecb_encrypt(key, text, iv, hex)`

  使用`ECB`模式对参数`text`进行加密; `key`参数提供256位的密钥, `iv`参数提供向量`IV`;

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.aes_256_ecb_decrypt(key, ciphertext, iv, hex)`

  使用`ECB`模式对参数`ciphertext`进行解密; `key`参数提供256位的密钥, `iv`参数提供向量`IV`; 

  当`hex`参数为`true`的时候, 会先对`ciphertext`解除`十六进制`编码;

### 11. UUID与GUID算法

---

  函数原型: `crypt.uuid()`

  按照`uuid v4`标准, 返回一个`基于随机数`的`UUID`;

---

  函数原型: `crypt.guid(hostname)`

  参数`hostname`默认为当前运行程序的主机名, 可以自己指定任意长度的字符串;

  作者自己设计的`GUID`算法: 前缀为`hostname`, 中间为毫秒级`int64`时间戳, 结尾处是`加密随机数`;

  经过随机测试可以`20万/秒`生成`唯一ID`, 已经在线上的`微信收款`功能的`生产环境`中稳定运行;


### 12. 其他算法

---

  函数原型: `crypt.xor_str(key, text)`

  对参数`text`的内容与参数`key`进行异或运算, 返回`String`类型的字符串;

---

  函数原型: `crypt.randomkey(hex)`

  随机生成字符串(长度固定为`8`); 当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示(长度固定为`16`);

---

  函数原型: `crypt.randomkey_ex(length, hex)`

  随机生成字符串(长度为`7<length<65`); 当`hex`参数为`true`的时候, 返回的内容使用`十六进制`编码来表示(长度为`(7<length<65) * 2`);

---

  函数原型: `crypt.hashkey(text, hex)`

  对参数`text`的内容进行`哈希摘要`, 返回`64位`的`String`类型字符串;

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.hmac_hash(text, hex)`

  对参数`text`的内容进行`HMAC哈希摘要`, 返回`String`类型字符串;

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.hmac64(text, hex)`

  对参数`text`的内容进行`HMAC摘要`, 返回`String`类型字符串;

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.hmac64_md5(text, hex)`

  对参数`text`的内容进行`HMAC46的md5摘要`, 返回`String`类型字符串;

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;


## 二、使用测试

  本次测试的所有代码均来自[这里](https://github.com/CandyMi/cfadmin/blob/master/script/test_crypt.lua), 可参考`源码文件`进行更多角度测试;

```lua
local crypt = require "crypt"
local Log = require "logging"

local function test_hex()
  print("----------*** 开始测试 HEXENCODE/HEXDECODE ***----------")

  -- 测试hexencode编码
  Log:DEBUG("测试hexencode", crypt.hexencode("1234567890"), crypt.hexencode("1234567890") == "31323334353637383930")
  -- 测试hexdecode编码
  Log:DEBUG("测试hexdecode", crypt.hexdecode("31323334353637383930"), crypt.hexdecode("31323334353637383930") == "1234567890")

  print("----------*** 开始测试 HEXENCODE/HEXDECODE ***----------\n")
end


local function test_crc()
  print("----------*** 开始测试CRC32/CRC64 ***----------")

  -- 测试crc32编码
  Log:DEBUG("测试crc32",crypt.crc32("admin"))
  -- 测试crc64编码
  Log:DEBUG("测试crc64", crypt.crc64("admin"))

  print("----------*** CRC32/CRC64 测试完毕 ***----------\n")
end


local function test_url( ... )
  print("----------*** 开始测试 urlencode/urldecode ***----------")

  local url = "https://www.baiud.com/我是谁/api?name=水果糖的小铺子&age=30"

  Log:DEBUG("测试urlencode: " .. crypt.urlencode(url))

  Log:DEBUG("测试urlencode: " .. crypt.urldecode(crypt.urlencode(url)))

  assert(crypt.urldecode(crypt.urlencode(url)) == url, "测试失败")

  print("----------*** urlencode/urldecode 测试完毕 ***----------\n")
end

local function test_sha()

  print("----------*** 开始测试MD5/SHA128/SHA256/SHA512 ***----------")

  local text = "123456789admin"

  Log:DEBUG("测试md5 :" .. crypt.md5(text, true))
  assert(crypt.md5(text, true) == "dce70093fd997b5e1a37c86dadaf0a48", "MD5测试失败")

  Log:DEBUG("测试sha128 :" .. crypt.sha1(text, true))
  assert(crypt.sha1(text, true) == "1e2f566cbda0a9c855240bf21b8bae030404cad7", "SHA128测试失败")

  Log:DEBUG("测试sha224 :" .. crypt.sha224(text, true))
  assert(crypt.sha224(text, true) == "47e386607ca91384c4ccca3dfd3da211aaf618b7a043bac2aa138495", "SHA224测试失败")

  Log:DEBUG("测试sha256 :" .. crypt.sha256(text, true))
  assert(crypt.sha256(text, true) == "e39594b63146c3f089bc12e1421cb3fe2fb9e4925908a995989e635d9bd1b096", "SHA256测试失败")

  Log:DEBUG("测试sha384 :" .. crypt.sha384(text, true))
  assert(crypt.sha384(text, true) == "18f26760edbc390cd61834e3100fbf88a14f2fe7b4dfdb11d2d5aea92388274163d5f4ae0cd9662b8a88e148d3b358f4", "SHA384测试失败")

  Log:DEBUG("测试sha512 :" .. crypt.sha512(text, true))
  assert(crypt.sha512(text, true) == "434042f8e8a262ffa53cb2ac1366aa4647c66464e9db8442338f0398cf400d7f966b360f0d12f1670fba01f2a0e900a3295143162ec5a215cf2d6b321294d02e", "SHA512测试失败")

  print("----------*** MD5/SHA128/SHA256/SHA512 测试完毕 ***----------\n")
end

local function test_hmac()

  print("----------*** 开始测试HMAC(MD5/SHA128/SHA256/SHA512) ***----------")

  local text = "123456789admin"
  local key = "admin"

  Log:DEBUG("hmac_md5 :" .. crypt.hmac_md5(key, text, true))
  assert(crypt.hmac_md5(key, text, true) == "fbe0f8e2cfb44139cfdf7162d6b9e709", "HMAC_MD5测试失败")

  Log:DEBUG("hmac_sha128 :" .. crypt.hmac_sha1(key, text, true))
  assert(crypt.hmac_sha1(key, text, true) == "197c3254b4c935717b7d7ca38fbdb642d22a63f9", "HMAC_SHA128测试失败")

  Log:DEBUG("hmac_sha256 :" .. crypt.hmac_sha256(key, text, true))
  assert(crypt.hmac_sha256(key, text, true) == "824902bf7fc037243b6cf0444a4887d21779526b0937b9f76b8ac23dafa0eb45", "HMAC_SHA256测试失败")

  Log:DEBUG("hmac_sha512 :" .. crypt.hmac_sha512(key, text, true))
  assert(crypt.hmac_sha512(key, text, true) == "346b81fb7771816ad1206d996de063859e8225d09a74776ded859d1e3e388b34aae09636c5c60b2ad7d88f5518483cc3d952753573b856aab4b96531a3cb4094", "HMAC_SHA512测试失败")

    -- 测试hmac64编码
  Log:DEBUG("hmac64", crypt.hmac64("12345678", "abcdefgh", true))

  -- 测试hmac64_md5编码
  Log:DEBUG("hmac64_md5", crypt.hmac64_md5("12345678", "abcdefgh", true))

  -- 测试hmac_hash编码
  Log:DEBUG("hmac_hash", crypt.hmac_hash("12345678", "abcdefgh", true))

  print("----------*** HMAC(MD5/SHA128/SHA256/SHA512) 测试完毕 ***----------\n")

end

local function test_des( ... )

  print("----------*** 开始测试 desencode/desdecode ***----------")

  local text = [[{"code":200,"data":[1,2,3,4,5,6,7,8,9,10]}]]

  local key = "12345678"

  -- 测试desencode编码
  Log:DEBUG("测试desencode", crypt.desencode(key, text, true))
  -- 测试desdecode编码
  Log:DEBUG("测试desdecode", crypt.desdecode(key, crypt.desencode(key, text)) == text)

  print("----------*** desencode/desdecode 测试完毕 ***----------\n")


end

local function test_other( ... )
  print("----------*** 开始测试 hashkey/randomkey ***----------")
  -- 测试randomkey编码
  Log:DEBUG("测试randomkey", crypt.randomkey(true))

  -- 测试hashkey编码
  Log:DEBUG("测试hashkey", crypt.hashkey("admin", true))

  print("----------*** hashkey/randomkey 测试完毕 ***----------\n")
end


local function test_xor_str( ... )

  print("----------*** 开始测试 xor_str ***----------")
  
  local rawData = "admin00000"
  local key = "1234567890-----"

  local xor = crypt.xor_str(rawData, key)

  local raw = crypt.xor_str(xor, key)

  assert(raw == rawData, "转换失败")

  Log:DEBUG( "xor data = " .. crypt.hexencode(xor), "raw data = " .. raw)

  print("----------*** xor_str 测试完成 ***----------\n")

end

local function test_b64()

  print("----------*** 开始测试 base64 ***----------")

  local rawData = "123456789"

  Log:DEBUG("测试base64:" ..  crypt.base64encode(rawData), "原始数据为:" .. rawData)

  assert(rawData == crypt.base64decode(crypt.base64encode(rawData)))

  print("----------*** base64 测试完毕 ***----------\n")

end

local function test_aes()

  print("----------*** 开始测试 aes_cbc/aes_ecb ***----------")
  
  local text = [[{"code":200,"msg":[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16]}]]
  local key_128 = "abcdefghabcdefgh"
  local iv = "98765432100admin"

  local function test_aes_128_bit()
    -- ECB
    local ecb_encryptData = crypt.aes_128_ecb_encrypt(key_128, text, iv)
    local ecb_rawData = crypt.aes_128_ecb_decrypt(key_128, ecb_encryptData, iv)
    Log:DEBUG("测试aes_128_ecb_encrypt: " .. crypt.hexencode(ecb_encryptData))
    assert(ecb_rawData == text, "aes加密/解密失败")

    -- CBC
    local cbc_encryptData = crypt.aes_128_cbc_encrypt(key_128, text, iv)
    local cbc_rawData = crypt.aes_128_cbc_decrypt(key_128, cbc_encryptData, iv)
    Log:DEBUG("测试aes_128_cbc_encrypt: " .. crypt.hexencode(cbc_encryptData))
    assert(cbc_rawData == text, "aes加密/解密失败")
  end


  local function test_aes_192_bit()
    key_192 = "abcdefghabcdefghabcdefgh"
    -- ECB
    local ecb_encryptData = crypt.aes_192_ecb_encrypt(key_192, text, iv)
    local ecb_rawData = crypt.aes_192_ecb_decrypt(key_192, ecb_encryptData, iv)
    Log:DEBUG("测试aes_192_ecb_encrypt: " .. crypt.hexencode(ecb_encryptData))
    assert(ecb_rawData == text, "aes加密/解密失败")

    -- CBC
    local cbc_encryptData = crypt.aes_192_cbc_encrypt(key_192, text, iv)
    local cbc_rawData = crypt.aes_192_cbc_decrypt(key_192, cbc_encryptData, iv)
    Log:DEBUG("测试aes_192_cbc_encrypt: " .. crypt.hexencode(cbc_encryptData))
    assert(cbc_rawData == text, "aes加密/解密失败")
  end

  local function test_aes_256_bit()
    key_256 = "abcdefghabcdefghabcdefghabcdefgh"
    -- ECB
    local ecb_encryptData = crypt.aes_256_ecb_encrypt(key_256, text, iv)
    local ecb_rawData = crypt.aes_256_ecb_decrypt(key_256, ecb_encryptData, iv)
    Log:DEBUG("测试aes_256_ecb_encrypt: " .. crypt.hexencode(ecb_encryptData))
    assert(ecb_rawData == text, "aes加密/解密失败")

    -- CBC
    local cbc_encryptData = crypt.aes_256_cbc_encrypt(key_256, text, iv)
    local cbc_rawData = crypt.aes_256_cbc_decrypt(key_256, cbc_encryptData, iv)
    Log:DEBUG("测试aes_256_cbc_encrypt: " .. crypt.hexencode(cbc_encryptData))
    assert(cbc_rawData == text, "aes加密/解密失败")
  end

  test_aes_128_bit()

  test_aes_192_bit()

  test_aes_256_bit()

  print("----------*** aes_cbc/aes_ecb 测试完毕 ***----------\n")

end

local function test_rsa()


  local function test_rsa_1024 ( ... )
    -- 生成公钥/私钥方法:

    -- 1. 使用openssl命令生成1024位私钥: openssl genrsa -out private1024.pem 1024

    -- 2. 使用openssl命令根据1024位私钥生成公钥: openssl rsa -in private1024.pem -out public1024.pem -pubout

    local publick_key_path = "public1024.pem"
    local private_key_path = "private1024.pem"

    local text = [[{"code":200,"data":[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,26,26,27,28,29,30,31,32,33,34,35]}]]

    local encData = crypt.rsa_public_key_encode(text, publick_key_path, true)
    -- print(encData)
    local decData = crypt.rsa_private_key_decode(encData, private_key_path, true)

    assert(decData == text, "rsa 1024 公钥加密 -> 私钥解密 失败." .. decData)


    local encData = crypt.rsa_private_key_encode(text, private_key_path, true)
    -- print(encData)
    local decData = crypt.rsa_public_key_decode(encData, publick_key_path, true)

    assert(decData == text, "rsa 1024 私钥加密 -> 公钥解密 失败.")

  end

  local function test_rsa_2048( ... )
      -- 生成公钥/私钥方法:

      -- 1. 使用openssl命令生成2048位私钥: openssl genrsa -out private2048.pem 2048

      -- 2. 使用openssl命令根据2048位私钥生成公钥: openssl rsa -in private2048.pem -out public2048.pem -pubout

      local publick_key_path = "public2048.pem"
      local private_key_path = "private2048.pem"

      local text = [[{"code":200,"data":[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,26,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40]}]]

      local encData = crypt.rsa_public_key_encode(text, publick_key_path, true)
      -- print(encData)
      local decData = crypt.rsa_private_key_decode(encData, private_key_path, true)
      -- print(decData)
      assert(decData == text, "rsa 2048 公钥加密 -> 私钥解密 失败." .. decData)


      local encData = crypt.rsa_private_key_encode(text, private_key_path, true)
      -- print(encData)
      local decData = crypt.rsa_public_key_decode(encData, publick_key_path, true)
      -- print(decData)
      assert(decData == text, "rsa 2048 私钥加密 -> 公钥解密 失败.")

  end

  local function test_rsa_4096( ... )
      -- 生成公钥/私钥方法:

      -- 1. 使用openssl命令生成4096位私钥: openssl genrsa -out private4096.pem 4096

      -- 2. 使用openssl命令根据4096位私钥生成公钥: openssl rsa -in private4096.pem -out public4096.pem -pubout

      local publick_key_path = "public4096.pem"
      local private_key_path = "private4096.pem"

      local text = [[{"code":200,"data":[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,26,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,]}]]

      local encData = crypt.rsa_public_key_encode(text, publick_key_path, true)
      -- print(encData)
      local decData = crypt.rsa_private_key_decode(encData, private_key_path, true)
      -- print(decData)
      assert(decData == text, "rsa 4096 公钥加密 -> 私钥解密 失败." .. decData)


      local encData = crypt.rsa_private_key_encode(text, private_key_path, true)
      -- print(encData)
      local decData = crypt.rsa_public_key_decode(encData, publick_key_path, true)
      -- print(decData)
      assert(decData == text, "rsa 4096 私钥加密 -> 公钥解密 失败.")

  end

  local function test_rsa_sign_and_rsa_verify(...)

    local text = [[{"code":200,"data":[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,26,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40]}]]

    local public_1024 = "public1024.pem"
    local private_1024 = "private1024.pem"
    
    local public_2048 = "public2048.pem"
    local private_2048 = "private2048.pem"

    local public_4096 = "public4096.pem"
    local private_4096 = "private4096.pem"

    local hex = true -- 是结果hex输出

    local sign_1024 = crypt.rsa_sign(text, private_1024, "md5", hex)
    print("md5 with rsa: 1024", sign_1024, crypt.rsa_verify(text, public_1024, sign_1024, "md5", hex))
    local sign_1024 = crypt.rsa_sign(text, private_1024, "sha128", hex)
    print("sha128 with rsa: 1024", sign_1024, crypt.rsa_verify(text, public_1024, sign_1024, "sha128", hex))
    local sign_1024 = crypt.rsa_sign(text, private_1024, "sha256", hex)
    print("sha256 with rsa: 1024", sign_1024, crypt.rsa_verify(text, public_1024, sign_1024, "sha256", hex))
    local sign_1024 = crypt.rsa_sign(text, private_1024, "sha512", hex)
    print("sha512 with rsa: 1024, ", sign_1024, crypt.rsa_verify(text, public_1024, sign_1024, "sha512", hex))

    local sign_2048 = crypt.rsa_sign(text, private_2048, "md5", hex)
    print("md5 with rsa: 2048", sign_2048, crypt.rsa_verify(text, public_2048, sign_2048, "md5", hex))
    local sign_2048 = crypt.rsa_sign(text, private_2048, "sha128", hex)
    print("sha128 with rsa: 2048", sign_2048, crypt.rsa_verify(text, public_2048, sign_2048, "sha128", hex))
    local sign_2048 = crypt.rsa_sign(text, private_2048, "sha256", hex)
    print("sha256 with rsa: 2048", sign_2048, crypt.rsa_verify(text, public_2048, sign_2048, "sha256", hex))
    local sign_2048 = crypt.rsa_sign(text, private_2048, "sha512", hex)
    print("sha512 with rsa: 2048", sign_2048, crypt.rsa_verify(text, public_2048, sign_2048, "sha512", hex))


    local sign_4096 = crypt.rsa_sign(text, private_4096, "md5", hex)
    print("md5 with rsa: 4096", sign_4096, crypt.rsa_verify(text, public_4096, sign_4096, "md5", hex))
    local sign_4096 = crypt.rsa_sign(text, private_4096, "sha128", hex)
    print("sha128 with rsa: 4096", sign_4096, crypt.rsa_verify(text, public_4096, sign_4096, "sha128", hex))
    local sign_4096 = crypt.rsa_sign(text, private_4096, "sha256", hex)
    print("sha256 with rsa: 4096", sign_4096, crypt.rsa_verify(text, public_4096, sign_4096, "sha256", hex))
    local sign_4096 = crypt.rsa_sign(text, private_4096, "sha512", hex)
    print("sha512 with rsa: 4096", sign_4096, crypt.rsa_verify(text, public_4096, sign_4096, "sha512", hex))

  end

  print("----------*** 开始测试 rsa public/private encode/decode ***----------")

  test_rsa_1024()

  test_rsa_2048()

  test_rsa_4096()

  test_rsa_sign_and_rsa_verify()

  print("----------*** rsa public/private encode/decode 测试完成 ***----------\n")

end

local function test_uuid( ... )

  print("----------*** 开始测试 uuid 生成 ***----------")

  Log:DEBUG("生成的UUID为: " .. crypt.uuid())

  print("----------*** uuid 测试完成 ***----------\n")
end

local function main()

  local examples = {

    -- test_xor_str,

    -- test_hex,
   
    -- test_crc,

    -- test_url,

    -- test_b64,

    -- test_sha,

    -- test_hmac,

    -- test_des,
    
    -- test_strxor,

    -- test_aes,

    -- test_other,

    -- test_rsa,

    -- test_uuid,

  }

  for _, f in ipairs(examples) do

    f()

  end

  Log:DEBUG("总共完成了" .. #examples .. "个测试用例.")

end

main()
```

## 三、国密算法

---

  `国密算法`是指`中国国家密码管理局`认定的`国产商用密码算法`. 具有自主知识产权与较高安全性，由国家密码局公开并大力推广。

  目前主要使用`SM2`、`SM3`、`SM4`三类算法，分别是非对称加密/解密算法、哈希校验算法和对称分组加密解密算法。

  * SM2 - 椭圆曲线非对称算法: 基于椭圆曲线离散对数问题，计算复杂度是完全指数级，同等安全程度要求下，椭圆曲线密码较其他公钥秒速昂发所需密钥长度小很多。

  * SM3 - 哈希校验算法: 主要用于数字签名及验证、消息认证码生成及验证、随机数生成, 目前已知其安全性及效率与SHA-256相当。

  * SM4 - 分组对称加密算法: 将明文分成多个等长的模块，使用确定的算法和对称密钥对每组分别加密解密。

  * SM3WithSM2 - 签名与验签算法; SM2配套的签名算法是基于SM3的SM2签名算法，叫做SM3WithSM2。

  需要注意的是: 目前的`OpenSSL < 3.0`版本既不支持`SSL`加密套件，同时也不支持命令行对基于`SM2`与`SM3`的证书签发.

  值得庆幸的是可以使用`EVP封装`的签名、验签、公钥/私钥生成, `OpenSSL(>= 1.1.1)`才可以使用到下面会介绍到的算法;

  如果需要完善的`国密算法`命令行支持, 开发者可以考虑使用`GMSSL`或`TASSL`衍生版. 目前框架可以兼容这些衍生版本.

### 1. 使用介绍

---

  函数原型: `crypt.sm3(text, hex)`

  计算`text`参数的`sm3`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.hmac_sm3(key, text, hex)`

  计算`key`与`text`参数的`hmac_sm3`值, 此方法返回一个`String`类型的值.

  当`hex`参数为`true`的时候, 会将返回的内容使用`十六进制`编码来表示;

---

  函数原型: `crypt.sm2keygen(pri, pub)`

  根据参数`pri`与`pub`提供的文件名生成EC-SM2`私钥`与`公钥`;

  此方法成功生成后不会有返回值, 失败时此方法将会直接抛出异常;

---

  函数原型: `crypt.sm2sign(pri, text, b64)`

  根据`pri`参数提供的私钥文件对参数`text`提供的值生成SM3WithSM2签名, 此方法返回一个`String`类型的值.

  当`b64`参数为`true`的时候, 会将返回的内容使用`base64`进行编码来表示;

---

  函数原型: `crypt.sm2verify(pub, text, sign, b64)`

  根据`pub`参数提供的公钥文件对参数`text`与`sign`进行SM3WithSM2验签, 此方法有一个bool类型的返回值;

  当`b64`参数为`true`的时候会对`sign`参数进行解码, 方法结果为true表示验签成功, 否则为验签失败;

---

  函数原型: `crypt.sm4_cbc_encrypt(key, text, iv, b64)`

  使用`CBC`模式对参数`text`进行加密; 参数`key`与`iv`分别为128位(16字节)的`秘钥`与`向量`;

  当`b64`参数为`true`的时候, 会将返回的内容使用`base64`编码来表示; 此方法失败将会直接抛出异常;

---

  函数原型: `crypt.sm4_cbc_decrypt(key, cipher, iv, b64)`

  使用`CBC`模式对参数`cipher`进行解密; 参数`key`与`iv`分别为128位(16字节)的`秘钥`与`向量`;

  当`b64`参数为`true`的时候, 会将返回的内容使用`base64`编码来表示; 此方法失败将会直接抛出异常;

---

  函数原型: `crypt.sm4_ecb_encrypt(key, text, iv, b64)`

  使用`ECB`模式对参数`text`进行加密; 参数`key`与`iv`分别为128位(16字节)的`秘钥`与`向量`;

  当`b64`参数为`true`的时候, 会将返回的内容使用`base64`编码来表示; 此方法失败将会直接抛出异常;

---

  函数原型: `crypt.sm4_ecb_decrypt(key, cipher, iv, b64)`

  使用`ECB`模式对参数`cipher`进行解密; 参数`key`与`iv`分别为128位(16字节)的`秘钥`与`向量`;

  当`b64`参数为`true`的时候, 会将返回的内容使用`base64`编码来表示; 此方法失败将会直接抛出异常;

---

  函数原型: `crypt.sm4_ofb_encrypt(key, text, iv, b64)`

  使用`OFB`模式对参数`text`进行加密; 参数`key`与`iv`分别为128位(16字节)的`秘钥`与`向量`;

  当`b64`参数为`true`的时候, 会将返回的内容使用`base64`编码来表示; 此方法失败将会直接抛出异常;

---

  函数原型: `crypt.sm4_ofb_decrypt(key, cipher, iv, b64)`

  使用`OFB`模式对参数`cipher`进行解密; 参数`key`与`iv`分别为128位(16字节)的`秘钥`与`向量`;

  当`b64`参数为`true`的时候, 会将返回的内容使用`base64`编码来表示; 此方法失败将会直接抛出异常;

---

  函数原型: `crypt.sm4_ctr_encrypt(key, text, iv, b64)`

  使用`CTR`模式对参数`text`进行加密; 参数`key`与`iv`分别为128位(16字节)的`秘钥`与`向量`;

  当`b64`参数为`true`的时候, 会将返回的内容使用`base64`编码来表示; 此方法失败将会直接抛出异常;

---

  函数原型: `crypt.sm4_ctr_decrypt(key, cipher, iv, b64)`

  使用`CTR`模式对参数`cipher`进行解密; 参数`key`与`iv`分别为128位(16字节)的`秘钥`与`向量`;

  当`b64`参数为`true`的时候, 会将返回的内容使用`base64`编码来表示; 此方法失败将会直接抛出异常;

### 2. 示例代码

---

```lua
local crypt = require "crypt"
local hexencode = crypt.hexencode

local sm3 = crypt.sm3
local hmac_sm3 = crypt.hmac_sm3
local sm2keygen = crypt.sm2keygen
local sm2sign = crypt.sm2sign
local sm2verify = crypt.sm2verify

local sm4_cbc_encrypt = crypt.sm4_cbc_encrypt
local sm4_cbc_decrypt = crypt.sm4_cbc_decrypt

local sm4_ecb_encrypt = crypt.sm4_ecb_encrypt
local sm4_ecb_decrypt = crypt.sm4_ecb_decrypt

local sm4_ofb_encrypt = crypt.sm4_ofb_encrypt
local sm4_ofb_decrypt = crypt.sm4_ofb_decrypt

local sm4_ctr_encrypt = crypt.sm4_ctr_encrypt
local sm4_ctr_decrypt = crypt.sm4_ctr_decrypt

local key = "administrator"
local iv = "0123456789123456"

local text = "测试版的`SM API`实现."
print(string.format('text = "%s", key = "%s", iv = "%s"', text, key, iv))
-- 这里的命令行代码可以在openssl 1.1.1以后的版本中直接运行测试.
print(string.format("使用openssl 1.1.1测试[%s]算法: echo -n '%s' | openssl %s -nosalt -iv '%s' -K '%s' -a -p", "sm4-cbc", text, "sm4-cbc", hexencode(iv), hexencode(key)))
print(string.format("使用openssl 1.1.1测试[%s]算法: echo -n '%s' | openssl %s -nosalt -iv '%s' -K '%s' -a -p", "sm4-ecb", text, "sm4-ecb", hexencode(iv), hexencode(key)))
print(string.format("使用openssl 1.1.1测试[%s]算法: echo -n '%s' | openssl %s -nosalt -iv '%s' -K '%s' -a -p", "sm4-ofb", text, "sm4-ofb", hexencode(iv), hexencode(key)))
print(string.format("使用openssl 1.1.1测试[%s]算法: echo -n '%s' | openssl %s -nosalt -iv '%s' -K '%s' -a -p", "sm4-ctr", text, "sm4-ctr", hexencode(iv), hexencode(key)))
print()

-- sm3 摘要内容
print('SM3摘要结果: ', sm3(text, true))

-- hmac_sm3 摘要内容
print('HMAC-SM3哈希结果:', hmac_sm3(key, text, true))

local cipher = sm4_cbc_encrypt(key, text, iv, true)
print("SM4-CBC-加密结果", cipher)
local raw = sm4_cbc_decrypt(key, cipher, iv, true)
print("SM4-CBC-解密结果", raw)

local cipher = sm4_ecb_encrypt(key, text, iv, true)
print("SM4-ECB-加密结果", cipher)
local raw = sm4_ecb_decrypt(key, cipher, iv, true)
print("SM4-ECB-解密结果", raw)

local cipher = sm4_ofb_encrypt(key, text, iv, true)
print("SM4-OFB-加密结果", cipher)
local raw = sm4_ofb_decrypt(key, cipher, iv, true)
print("SM4-OFB-解密结果", raw)

local cipher = sm4_ctr_encrypt(key, text, iv, true)
print("SM4-CTR-加密结果", cipher)
local raw = sm4_ctr_decrypt(key, cipher, iv, true)
print("SM4-CTR-解密结果", raw)

-- 在当前文件夹内生成EC-SM2的私钥与公钥
sm2keygen("pri.key", "pub.key")

-- 测试签名与验签
local sign = sm2sign("pri.key", text, true)
print("SM3 With SM2签名为：", sign)
print("SM3 With SM2验签结果:", sm2verify("pub.key", text, sign, true) and "成功" or "失败")
```