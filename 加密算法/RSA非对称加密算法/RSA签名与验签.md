# 前言

数据签名的目的是为了防止信息被篡改，签名过程如下：

1）A生成一对密钥（公钥和私钥），私钥不公开，A自己保留。公钥为公开的，任何人可以获取。

2）A用自己的私钥对消息进行加签，形成签名，并将签名和消息本身一起传递给B。

3）B收到消息后，通过A的公钥进行验签，如果验签成功，则证明消息是A发送的。

整个过程，只有使用A私钥签名的消息才能被验签成功。即使知道了消息内容，也无法伪造签名，防止消息被篡改。

需要特别强调的一点是：在实际场景中，数据签名通常是配合各种 Hash（如 MD4、MD5、SHA1、SHA256、SHA512）算法实现，也就是常见的 HashWithRSA（如 SHA256WithRSA）。也就是说，只要是 Hash 算法就可以 WithRSA（如 MD5withRSA）。另外 HashWithRSA 不是 Hash-Then-Rsa，这两者是有本质区别，千万不要理解错了。在 stackoverflow 上也有这个提问，见：[Difference between SHA256withRSA and SHA256 then RSA](https://stackoverflow.com/questions/33305800/difference-between-sha256withrsa-and-sha256-then-rsa)。

接下来就来看下 Go 以及 Java 如何实现数据签名和验签：

# Go 数据签名以及验签

在解析密匙对时一定要选择合适的格式，如私钥是 PKCS#8 格式，你不能使用 pkcs1 去解析，反之亦然。

以 pkcs1 私钥为例，实际场景中密匙数据有如下两种使用形式：一个是完整的 pkcs1 私钥内容（包含头 PEM header 信息），另一个则是去掉 PEM header 及换行符后的数据体。根据这两种形式，在解析时也需要做不同的处理。

如果是第二种形式，在加载时应该先进行 base64 解码，然后再进行解析：

```go
package main

import (
	"crypto/x509"
	"encoding/base64"
	"fmt"
)

const (
	pkcs1Private = `............. 略 .............`
)

func main() {

	// base64 解码
	bytes, _ := base64.StdEncoding.DecodeString(pkcs1Private)

	// 解析 pkcs1 私钥
	pkcs1Private, err := x509.ParsePKCS1PrivateKey(bytes)
	if err != nil {
		fmt.Println("pkcs1 私钥解析失败")
		return
	}

	fmt.Println(pkcs1Private.Size())
}
```

但如果你的私钥是第一种形式就需要换种解析方式，直接使用 `pem.Decode`：

```go
package main

import (
	"crypto/x509"
	"encoding/pem"
	"fmt"
)

const (
	pkcs1Private = `
-----BEGIN RSA PRIVATE KEY-----
............. 略 .............
-----END RSA PRIVATE KEY-----
`
)

func main() {

	block, _ := pem.Decode([]byte(pkcs1Private))
	pkcs1Private, err := x509.ParsePKCS1PrivateKey(block.Bytes)
	if err != nil {
		fmt.Println("pkcs1 私钥解析失败")
		return
	}

	fmt.Println(pkcs1Private.Size())
}
```

在实际中也要根据需要做选择。接下来就来看下具体的签名和验签流程：

## 私钥签名

```go
package main

import (
	"crypto"
	"crypto/rand"
	"crypto/rsa"
	"crypto/x509"
	"encoding/base64"
	"encoding/pem"
	"fmt"
)

const (
	pkcs1Private = `
-----BEGIN RSA PRIVATE KEY-----
............. 略 .............
-----END RSA PRIVATE KEY-----
`
)

func main() {
	data := []byte("你好,world!")

	// 这里可以是任何 Hash* 算法, 选择 SHA256withRSA.
	// 你也可以选择 MD5withRSA: crypto.MD5, SHA512withRSA: crypto.SHA512.
	// 根据实际需要进行选择

	hash := crypto.SHA256

	// 私钥签名
	sign, _ := HashWithRsaSign(pkcs1Private, hash, data)
	if err != nil {
		fmt.Println("签名失败", err)
		return
	}
	fmt.Printf("签名:\n%s", sign)
}

func HashWithRsaSign(privateKey string, hash crypto.Hash, data []byte) (base64sign string, err error) {

	// PEM 解码, 得到 block 的 der 编码数据
	block, _ := pem.Decode([]byte(privateKey))
	pkcs1, err := x509.ParsePKCS1PrivateKey(block.Bytes)
	if err != nil {
		return "", err
	}

	// Hash 计算, 得到签名
	h := hash.New()
	h.Write(data)
	h.Sum(nil)

	b, err := rsa.SignPKCS1v15(rand.Reader, pkcs1, hash, h.Sum(nil))
	if err != nil {
		return "", err
	}

	// 转 base64
	return base64.StdEncoding.EncodeToString(b), nil
}
```

这里唯一要注意的是 Hash\* 算法，要知道该如何去选择。

接下来就是验签：

## 公钥验签

验签与签名代码如初一折，区别就是这回要加载的是公钥：

```go
const (
	pkcs1Private = `
-----BEGIN RSA PRIVATE KEY-----
............. 略 .............
-----END RSA PRIVATE KEY-----
`

	pkcs1Public = `
-----BEGIN RSA PUBLIC KEY-----
............. 略 .............
-----END RSA PUBLIC KEY-----
`
)

func main() {
	data := []byte("你好,world!")

	// 这里可以是任何 Hash* 算法, 选择 SHA256withRSA.
	// 你也可以选择 MD5withRSA: crypto.MD5, SHA512withRSA: crypto.SHA512.
	// 根据实际需要进行选择

	hash := crypto.SHA256

	// 私钥签名
	sign, _ := HashWithRsaSign(pkcs1Private, hash, data)
	// ...

	// 公钥验签
	err := HashWithRsaSignVerify(pkcs1Public, hash, data, sign)
	if err == nil {
		fmt.Println("验签: ok")
	}
}

func HashWithRsaSignVerify(publicKey string, hash crypto.Hash, data []byte, base64sign string) error {

	// PEM 解码, 得到 block 的 der 编码数据
	block, _ := pem.Decode([]byte(publicKey))
	pkcs1, err := x509.ParsePKCS1PublicKey(block.Bytes)
	if err != nil {
		return err
	}

	// Hash 计算, 得到签名
	h := hash.New()
	h.Write(data)
	h.Sum(nil)

	sign, err := base64.StdEncoding.DecodeString(base64sign)
	if err != nil {
		return err
	}

	// 验证签名
	err = rsa.VerifyPKCS1v15(pkcs1, hash, h.Sum(nil), sign)
	if err != nil {
		return err
	}

	return nil
}
```

# Java 数据签名以及验签

直接整理出一个常用工具类：

```java

import java.nio.charset.StandardCharsets;
import java.security.*;
import java.security.interfaces.RSAPrivateKey;
import java.security.interfaces.RSAPublicKey;
import java.security.spec.InvalidKeySpecException;
import java.security.spec.PKCS8EncodedKeySpec;
import java.security.spec.X509EncodedKeySpec;
import java.util.HashMap;
import java.util.Map;

/**
 * <p>
 * 该类提供常见的 HashWithRSA 组合算法, 用于提供数据签名和验签.
 * </p><br>
 *
 * <p>
 * HashWithRSA 指的是使用 RSA 加密算法结合哈希函数进行数字签名或数据加密。
 * RSA(Rivest–Shamir–Adleman)是一种非对称加密算法,而哈希函数则用于生成消息
 * 摘要(即签名).
 * </p><br>
 *
 * <p>加密和签名的区别:
 * 加密和签名都是为了安全性考虑,但有所不同.加密是为了防止信息被泄露,签名是为了防止信息被篡改.
 * </p><br>
 *
 * <h3>加密过程:</h3>
 * <ul>
 * <li>A生成一对密钥(公钥和私钥).私钥不公开,A自己保留.公钥为公开的,任何人可以获取.</li>
 * <li>A传递自己的公钥给B,B使用A的<em>公钥对消息进行加密</em>.</li>
 * <li>A接收到B加密的消息,利用A自己的<em>私钥对消息进行解密</em></li>
 * </ul>
 * <p>
 * 整个过程中,只用A的私钥才能对消息进行解密,防止消息被泄露.
 * </p><br>
 *
 * <h3>签名过程:</h3>
 * <ul>
 * <li>A生成一对密钥(公钥和私钥).私钥不公开,A自己保留.公钥为公开的,任何人可以获取.</li>
 * <li>A用自己的私钥对消息进行加签,形成签名,并将签名和消息本身一起传递给B.</li>
 * <li>B收到消息后,通过A的公钥进行验签.如果验签成功,则证明消息是A发送的.</li>
 * </ul>
 * <p>
 * 整个过程,只有使用A私钥签名的消息才能被验签成功.即使知道了消息内容,也无法伪造签名,防止消息被篡改.
 * </p><br>
 *
 * @author magicianlib@gmail.com
 */
public enum HashWithRSA {

    MD5withRSA,
    SHA1withRSA,
    SHA256withRSA,
    SHA384withRSA,
    SHA512withRSA,

    ;

    /**
     * 签名
     */
    public String signatureHex(String rsaPriBase64, String plaintext) throws NoSuchAlgorithmException,
            InvalidKeySpecException, InvalidKeyException, SignatureException {
        return Hex.toHexString(signature(rsaPriBase64, plaintext));
    }

    public String signatureBase64(String rsaPriBase64, String plaintext) throws NoSuchAlgorithmException,
            InvalidKeySpecException, InvalidKeyException, SignatureException {
        return Base64.encodeToString(signature(rsaPriBase64, plaintext));
    }

    public byte[] signature(String rsaPriBase64, String plaintext) throws NoSuchAlgorithmException,
            InvalidKeySpecException, InvalidKeyException, SignatureException {
        return signature(Base64.decodeToByte(rsaPriBase64), plaintext);
    }

    public byte[] signature(byte[] rsaPri, String plaintext) throws NoSuchAlgorithmException,
            InvalidKeySpecException, InvalidKeyException, SignatureException {

        // 私钥证书
        PKCS8EncodedKeySpec keySpec = new PKCS8EncodedKeySpec(rsaPri);
        PrivateKey privateKey = KeyFactory.getInstance("RSA").generatePrivate(keySpec);

        // 进行签名
        Signature spi = Signature.getInstance(this.name());
        spi.initSign(privateKey);
        spi.update(plaintext.getBytes(StandardCharsets.UTF_8));

        return spi.sign();
    }

    /**
     * 验签
     */
    public boolean verifyHex(String rsaPubBase64, String plaintext, String signatureHex) throws NoSuchAlgorithmException, InvalidKeySpecException, SignatureException, InvalidKeyException {
        return verify(rsaPubBase64, plaintext, Hex.toByteArray(signatureHex));
    }

    public boolean verifyBase64(String rsaPubBase64, String plaintext, String signatureBase64) throws NoSuchAlgorithmException, InvalidKeySpecException, SignatureException, InvalidKeyException {
        return verify(rsaPubBase64, plaintext, Base64.decodeToByte(signatureBase64));
    }

    public boolean verify(String rsaPubBase64, String plaintext, byte[] signature) throws NoSuchAlgorithmException, InvalidKeySpecException, SignatureException, InvalidKeyException {
        return verify(Base64.decodeToByte(rsaPubBase64), plaintext, signature);
    }

    public boolean verify(byte[] rsaPub, String plaintext, byte[] signature) throws NoSuchAlgorithmException, InvalidKeySpecException, SignatureException, InvalidKeyException {

        // 公钥证书
        X509EncodedKeySpec keySpec = new X509EncodedKeySpec(rsaPub);
        PublicKey publicKey = KeyFactory.getInstance("RSA").generatePublic(keySpec);

        Signature spi = Signature.getInstance(this.name());
        spi.initVerify(publicKey);
        spi.update(plaintext.getBytes(StandardCharsets.UTF_8));

        // 验签
        return spi.verify(signature);
    }

    /**
     * RSA key-pair
     */
    public static Map<String, String> genKeyPair(int keySize) throws Exception {

        KeyPairGenerator keyPairGen = KeyPairGenerator.getInstance("RSA");
        keyPairGen.initialize(keySize);
        KeyPair keyPair = keyPairGen.generateKeyPair();
        RSAPublicKey publicKey = (RSAPublicKey) keyPair.getPublic();
        RSAPrivateKey privateKey = (RSAPrivateKey) keyPair.getPrivate();

        Map<String, String> keyMap = new HashMap<>(2);
        keyMap.put("private", Base64.encodeToString(privateKey.getEncoded()));
        keyMap.put("public", Base64.encodeToString(publicKey.getEncoded()));

        return keyMap;
    }


    /**
     * Convert each byte to a hexadecimal string
     */
    public static String toHexString(byte[] data) {
        StringBuilder builder = new StringBuilder(2 * data.length);

        for (byte b : data) {
            builder.append(String.format("%02X", b));
        }

        return builder.toString();
    }

    public static void main(String[] args) throws Exception {

        Map<String, String> keyPair = genKeyPair(2048);

        String plaintext = "hello,world";

        String signature = HashWithRSA.SHA1withRSA.signatureHex(keyPair.get("private"), plaintext);
        System.out.println(signature);

        boolean verify = HashWithRSA.SHA1withRSA.verifyHex(keyPair.get("public"), plaintext, signature);
        System.out.println(verify);

    }
}
```
