# 前言

数据签名的目的是为了防止信息被篡改，签名过程如下：

1）A生成一对密钥（公钥和私钥），私钥不公开，A自己保留。公钥为公开的，任何人可以获取。

2）A用自己的私钥对消息进行加签，形成签名，并将签名和消息本身一起传递给B。

3）B收到消息后，通过A的公钥进行验签，如果验签成功，则证明消息是A发送的。

整个过程，只有使用A私钥签名的消息才能被验签成功。即使知道了消息内容，也无法伪造签名，防止消息被篡改。

需要特别强调的一点是：在实际场景中，数据签名通常是配合各种 Hash（如 MD4、MD5、SHA1、SHA256、SHA512）算法实现，也就是常见的 HashWithRSA（如 SHA256WithRSA）。也就是说，只要是 Hash 算法就可以 WithRSA（如 MD5withRSA）。另外 HashWithRSA 不是 Hash-Then-Rsa，这两者是有本质区别，千万不要理解错了。在 stackoverflow 上也有这个提问，见：[Difference between SHA256withRSA and SHA256 then RSA](https://stackoverflow.com/questions/33305800/difference-between-sha256withrsa-and-sha256-then-rsa)。

|**Note**|
|:------|
|注意 HashWithRSA 并不是 Hash-then-RSA。|

接下来就来看下 Go 以及 Java 如何实现数据签名和验签：

# Go 数据签名以及验签

数据签名和验签需要一个 rsa 密匙对，而使用 go 如何生成密匙对可以看下[RSA密匙对生成#使用 go 生成rsa密匙对](RSA密匙对生成.md#使用-go-生成rsa密匙对)中的代码示例，这里就直接使用前面生成的 pkcs1 私钥和 pkcs1 公钥做说明。

**pkcs1 私钥：**

```
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA1/kSthok32KKq/rSeeBkGJBWsRxq74ahToYYx9j2UcFLifE/
SuEJJz54/Qplzg/rYQypUfxixwr1flFYpnJOrXAP+JBM1cpNovLHOukUr093jqWi
+8ItrIMbMZwOCFd1GdTGIvrTeqVZ5Hv1X6YVX2Q4auzuYAhYz7/U0Ybkdymm7zal
B8nWeB6WjkSb2lPBAnx+9EWSft8VYkr76YO9qbu98Yntt/fPPYsdMToyWb34Echf
0E7H2ZJuTik01IqUB/xlBGRVjXYO96OZ/krwQbUMrJW5zl7jRBjPCeD30lhSfUv/
YMwAznz5V5SJFbJH1jGdZmcw8Y1PefFH1nXz9QIDAQABAoIBAQC4CJjmLa7/3zN1
Sv0xN4Fz4QLOVv3Ub29ivye8m+eBUOnyeG1LfCjVlQl57fNEPRKAl05s+1icbX01
j1GwO7VJWzVwxF2Ey6eOU7xrOZQTsu6Y0hHo39UVrH5urqDSgNvbEBFqWzv7xYyt
AufbAmYx6++bRZv2nwY2uSs7A+wIkRc0Ip1gyOvMVRFTI1AnK5vHBpCMivmKquVg
17ru+jcnqoizwGdgzozHhz/EIqCdNxKgwrLGYL+Hv7f6BopXLcfojCXzfvnJnERo
eTw/+jsoheuD2eq4wdBtRBoHd9rpzLrAWou9rb3mNWSQa0EfJQTnrAl+Bs9sC9j7
AE1IDFa9AoGBAN3mkQ/21a51KDjfDNgWejrC+HAfmZxrEBcEr3F+Z7uHNlYXxXk+
+RRtlnfGOT7KwWNFmgrMBVIaiAn0Cs6ItpaTWo97mzkcYSrJFjOXi5Zy6rGcpCAv
Xen//d1CaZrLYCkbUHcxkeJ5Vj/0hfuj1cHL1BEvtI9qY8lQEtmOdEVXAoGBAPkp
UGXbbswNk0EcVv2JVHh8/xfOUyz5S+76XGeZSgOQzXm33gwo+/Qi57VcGoEhayRF
o+cvf8AEDacOgrLJvnn1bWAzbf7zDNwg9R3uGbiyDo2uX5kRY4kzzTzcG+wZXfjs
dUt7Bxu+KbIaiEktIGQmyrMJSBNhN94epB1szhWTAoGAcaTPzjlAHmTqAJZbYZ/B
Ya7QxbupVC6QwvknURzHy4boEReDjyDtN8UYNqdSUhJNEKZoyfbLEs17tn1Bu5zQ
xjUZQDqxuIIMUXlC4ZFPb/HSuLvcYo9fraPpRBPrOdfGm8bdYu4EJNS3k3GL3OB/
bQ0SwuqHsptuF3/m53zJl2kCgYA45QMzzxCUzcDDTGpcyKAeR0SktftuOJrrPgYd
rfYlrEoPMjolArLNw66vThHLTMs/ZBuSLdl5CjPn4rtbgg/Zn35TSksivXNBTNFL
/VOoRhhmp9uygzL8YHTMIeLKXdyEe9Io4zXzUtAImGBofUJ5PBZrHYJ00m1EbjDB
e1fvuwKBgBhUOFkZy2jrAiYuFGZQbqO8vKP2caXomARqS1F2Iz2Fh+oGzmTJiRVt
4urw5PLSPGHeiRA8GSx20F7McrFoPOqjEu2Y2kuvq9NymMXw9Zz4UycXb6uSzo36
vxn17NwW7i/Zcfk9x3iLnQxPFk/X572XteeVhe5BHCe5ECakODKc
-----END RSA PRIVATE KEY-----
```

**pkcs1 公钥：**

```
-----BEGIN RSA PUBLIC KEY-----
MIIBCgKCAQEA1/kSthok32KKq/rSeeBkGJBWsRxq74ahToYYx9j2UcFLifE/SuEJ
Jz54/Qplzg/rYQypUfxixwr1flFYpnJOrXAP+JBM1cpNovLHOukUr093jqWi+8It
rIMbMZwOCFd1GdTGIvrTeqVZ5Hv1X6YVX2Q4auzuYAhYz7/U0Ybkdymm7zalB8nW
eB6WjkSb2lPBAnx+9EWSft8VYkr76YO9qbu98Yntt/fPPYsdMToyWb34Echf0E7H
2ZJuTik01IqUB/xlBGRVjXYO96OZ/krwQbUMrJW5zl7jRBjPCeD30lhSfUv/YMwA
znz5V5SJFbJH1jGdZmcw8Y1PefFH1nXz9QIDAQAB
-----END RSA PUBLIC KEY-----
```

--

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
	pkcs1Private = `
MIIEowIBAAKCAQEA1/kSthok32KKq/rSeeBkGJBWsRxq74ahToYYx9j2UcFLifE/SuEJJz54/Qplzg/rYQypUfxixwr1flFYpnJOrXAP+JBM1cpNovLHOukUr093jqWi+8ItrIMbMZwOCFd1GdTGIvrTeqVZ5Hv1X6YVX2Q4auzuYAhYz7/U0Ybkdymm7zalB8nWeB6WjkSb2lPBAnx+9EWSft8VYkr76YO9qbu98Yntt/fPPYsdMToyWb34Echf0E7H2ZJuTik01IqUB/xlBGRVjXYO96OZ/krwQbUMrJW5zl7jRBjPCeD30lhSfUv/YMwAznz5V5SJFbJH1jGdZmcw8Y1PefFH1nXz9QIDAQABAoIBAQC4CJjmLa7/3zN1Sv0xN4Fz4QLOVv3Ub29ivye8m+eBUOnyeG1LfCjVlQl57fNEPRKAl05s+1icbX01j1GwO7VJWzVwxF2Ey6eOU7xrOZQTsu6Y0hHo39UVrH5urqDSgNvbEBFqWzv7xYytAufbAmYx6++bRZv2nwY2uSs7A+wIkRc0Ip1gyOvMVRFTI1AnK5vHBpCMivmKquVg17ru+jcnqoizwGdgzozHhz/EIqCdNxKgwrLGYL+Hv7f6BopXLcfojCXzfvnJnERoeTw/+jsoheuD2eq4wdBtRBoHd9rpzLrAWou9rb3mNWSQa0EfJQTnrAl+Bs9sC9j7AE1IDFa9AoGBAN3mkQ/21a51KDjfDNgWejrC+HAfmZxrEBcEr3F+Z7uHNlYXxXk++RRtlnfGOT7KwWNFmgrMBVIaiAn0Cs6ItpaTWo97mzkcYSrJFjOXi5Zy6rGcpCAvXen//d1CaZrLYCkbUHcxkeJ5Vj/0hfuj1cHL1BEvtI9qY8lQEtmOdEVXAoGBAPkpUGXbbswNk0EcVv2JVHh8/xfOUyz5S+76XGeZSgOQzXm33gwo+/Qi57VcGoEhayRFo+cvf8AEDacOgrLJvnn1bWAzbf7zDNwg9R3uGbiyDo2uX5kRY4kzzTzcG+wZXfjsdUt7Bxu+KbIaiEktIGQmyrMJSBNhN94epB1szhWTAoGAcaTPzjlAHmTqAJZbYZ/BYa7QxbupVC6QwvknURzHy4boEReDjyDtN8UYNqdSUhJNEKZoyfbLEs17tn1Bu5zQxjUZQDqxuIIMUXlC4ZFPb/HSuLvcYo9fraPpRBPrOdfGm8bdYu4EJNS3k3GL3OB/bQ0SwuqHsptuF3/m53zJl2kCgYA45QMzzxCUzcDDTGpcyKAeR0SktftuOJrrPgYdrfYlrEoPMjolArLNw66vThHLTMs/ZBuSLdl5CjPn4rtbgg/Zn35TSksivXNBTNFL/VOoRhhmp9uygzL8YHTMIeLKXdyEe9Io4zXzUtAImGBofUJ5PBZrHYJ00m1EbjDBe1fvuwKBgBhUOFkZy2jrAiYuFGZQbqO8vKP2caXomARqS1F2Iz2Fh+oGzmTJiRVt4urw5PLSPGHeiRA8GSx20F7McrFoPOqjEu2Y2kuvq9NymMXw9Zz4UycXb6uSzo36vxn17NwW7i/Zcfk9x3iLnQxPFk/X572XteeVhe5BHCe5ECakODKc
`
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

这里唯一要注意的是 Hash\* 算法，要知道该如何去选择。最后签名如下：

```
签名:
zgwr1zJ1UAXCUZqmrpvCssVo/gPzKdPiAl16Cgi7a7P4nI4rgArvOKXdRxpQQE0GzZE0Vrxu0k5Y1L2OM7FPtEUH4vb8nsi2XoMXzJV2gMncAm6xbjXp/XobkbMdarFdnqMPGhBwDvIhVXArQpiLhsrFTOlJSTl2U9DzlBstr9fT0wbr/Ruyc4IIS6eoNWjy9Mz0t2fDiOsOCsl336yVtnOnTO3rZNxnKKiIgoV6H8c9Fis8TUaGBhr4aI0s17p2g5vVMJDyGWveEp9uCvMLj8sx5ERBlDfyv4bHMLsLgDRjR6vnVl0sxoc2YkNTlX/hSQNxc3Uv9zBUhnTQAfxJ6Q==
```

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

最后显而易见，验签: ok！

# Java 数据签名以及验签

Java 签名与验签与 Go 如出一辙，区别是 Java 加载的私钥必须是 PKCS#8 格式，公钥必须是 X.509 格式。这里就直接使用[RSA密匙对生成#使用java生成rsa密匙对](RSA密匙对生成.md#使用java生成rsa密匙对)中生成的 rsa 密匙做演示。

直接上代码：

```java
import org.apache.commons.lang3.StringUtils;

import java.security.KeyFactory;
import java.security.PrivateKey;
import java.security.PublicKey;
import java.security.Signature;
import java.security.spec.PKCS8EncodedKeySpec;
import java.security.spec.X509EncodedKeySpec;
import java.util.Base64;

/**
 * Hash-RSA 签名
 */
public class HashWithRsaUtils {

    private static final String pkcs8Private = "MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQCrYVd9I348JQVUfvnDR+BYrAf+zK1v+HFjwBZJYX0vmmlC9vSlR5jZt805ZqIDvV3HmlDscDkJdq1BYhZ33bqP3VQIujeuV+bsc4PRsVbANxhJkBYqfNa7eRZONv8RZv5UEq4OElbt0uA02EfZmr+cjC5EkmFn8HHH9j9yFXCBMAJbywn8BLTYfD8+LHGRVvC9VBAUN2F1AbQounZG77vEuo2q6B7h8bg05AdSe8ioMBzbzHPs1NNvtd2jQ+wix762++UUaX5XoHn1+46NwTyiWddypeW6kBgOqeE9NuyJiMksjnSQLIfuNTDjLalK7xYuO2CZLMdq6v/a9e+VqSn9AgMBAAECggEADR9E7aX8EmrbP58BTLyCJwf1DMNrPDmPpQ2eJzw5s51lFeWG0ejrgTAbnJuJKDQlrBTawV/OXjUiCvGRutfVYxKVTp3DiavdWJl3rGShudH+IBPSPqqUXoJk/NQlL5XtP0622Qj6cvdEh4dP6Q96QGhTWpjCsIyuiZsHPJr7pEiCgqsEm4GRwmm6/FndJjzO6EhxJAoJ3h26O9TpMvxxz2uKMFr+7d18z8mc33pq/MxQ4k73mSmaXsqXIZ8WA6v70Jb8tlqXytNyE1EUXz2i1cAZUZdDnkn59VXyDn8m2LgF6cuvex10Rs8NZl8pZEd6KjPD7cynN9/m0mV6siuKWQKBgQDpQTcyOZUVNOnkqHtoD8pOZH9nbRNbKasDTtNo+62gQ0V6/Xg2RZpFs/x6pCy7JLTOo1bjicKgssBEuhkN2uTXjrrTbNVoxtWt3LvnCZyQjEXrUhFwQ3tX/0zYKlqFm+YodquxV1sUF/MstYd8zc+wgvMwwJwvkY2i6ZzaE4jBywKBgQC8F4pkBxhSMnGHrdR3zFdqrvq/E1ZU2gBAVOVDyODuhCSw735vC71yB8UhfazmK0JJFkkvPsnHYS7Xci+useX9eSALxTQQZD4mde/7kyRCnaGxeWuvpYbPZmb2r2tiI6sQcg31ldMDlmtuUlor57PVAMn3PXIIkXn/q3CzKqwqVwKBgHEFK4SHiCglYmtsA5exhvojEeSAYo+aigVpTWCiCkgMMH9ej7/kS9MYWe7m29G2AHHbjT9lQcvxsN3cOkWPWbDktLu7EVDafzDmMpnI+3bCsSJLuq1l1T8XMhnPs9rnZkcW8SxVwoji7e4s60BazRae7M0jlsopwbI9dcXpDyuvAoGAQxqO5/LUDXcro8EzfO3s/gWhNYwbj7gawNjI1m8TOGTyhiFbVRi2JLl67eDdT1x9kH+f6ngXGsekI378XfHf4JdXBHv9gB+TQLtwu6saE54TMrLj5U1DlDivYTDiOagJ/psCOeyS1MPUXg6JVwKZ2NMoOsm0JjRodvteAL2noI8CgYEAvsiKEy3RUnZydDA///Yg+SAz55fpiO1M/Vx1BVNLGJv7fRcTf2cPUDvAOI/ZSQq8+QqT5nSfuJG+yQDex82bsSHfpYF6ETTSz7WLkGjaUSqwfgBivLzPu1HuZHYchwdfumY6bbOd2v2G/In2lM0bw2qbhaNhauq9gy7WuWzKSlE=";
    private static final String x509Public = "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAq2FXfSN+PCUFVH75w0fgWKwH/sytb/hxY8AWSWF9L5ppQvb0pUeY2bfNOWaiA71dx5pQ7HA5CXatQWIWd926j91UCLo3rlfm7HOD0bFWwDcYSZAWKnzWu3kWTjb/EWb+VBKuDhJW7dLgNNhH2Zq/nIwuRJJhZ/Bxx/Y/chVwgTACW8sJ/AS02Hw/PixxkVbwvVQQFDdhdQG0KLp2Ru+7xLqNquge4fG4NOQHUnvIqDAc28xz7NTTb7Xdo0PsIse+tvvlFGl+V6B59fuOjcE8olnXcqXlupAYDqnhPTbsiYjJLI50kCyH7jUw4y2pSu8WLjtgmSzHaur/2vXvlakp/QIDAQAB";

    /**
     * 私钥签名
     *
     * @param privateKeyStr 私钥
     * @param plaintext     明文
     * @param hash          Hash算法
     */
    public static String hashWithRsaSignature(String privateKeyStr, String plaintext, HashAlgorithm hash) throws Exception {

        byte[] key = Base64.getDecoder().decode(privateKeyStr);
        byte[] data = plaintext.getBytes();

        PKCS8EncodedKeySpec keySpec = new PKCS8EncodedKeySpec(key);
        KeyFactory keyFactory = KeyFactory.getInstance("RSA");
        PrivateKey privateKey = keyFactory.generatePrivate(keySpec);

        // 这个根据需求填充 SHA1WithRSA 或 SHA256WithRSA
        Signature signature = Signature.getInstance(hash.name());
        signature.initSign(privateKey);
        signature.update(data);
        return new String(Base64.getEncoder().encode(signature.sign()));
    }

    /**
     * RSA公钥验签
     *
     * @param publicKeyStr 公钥
     * @param plaintext    明文
     * @param sign         签名 {@link #hashWithRsaSignature(String, String, HashAlgorithm)}
     * @param hash         Hash算法
     */
    public static boolean hashWithRsaVerify(String publicKeyStr, String plaintext, String sign, HashAlgorithm hash) throws Exception {
        KeyFactory keyFactory = KeyFactory.getInstance("RSA");
        X509EncodedKeySpec x509EncodedKeySpec = new X509EncodedKeySpec(Base64.getDecoder().decode(publicKeyStr));
        PublicKey publicKey = keyFactory.generatePublic(x509EncodedKeySpec);

        // 这个根据需求填充 SHA1WithRSA 或 SHA256WithRSA
        Signature signature = Signature.getInstance(hash.name());
        signature.initVerify(publicKey);
        signature.update(plaintext.getBytes());
        return signature.verify(Base64.getDecoder().decode(sign));
    }

    public static void main(String[] args) {

        String plaintext = "你好, 世界!";

        String signature = null;
        try {
            signature = hashWithRsaSignature(pkcs8Private, plaintext, HashAlgorithm.SHA1withRSA);
        } catch (Exception e) {
            e.printStackTrace();
        }
        if (StringUtils.isBlank(signature)) {
            return;
        }

        System.out.println("签名:");
        System.out.println(signature);

        try {
            boolean ok = hashWithRsaVerify(x509Public, plaintext, signature, HashAlgorithm.SHA1withRSA);
            if (ok) {
                System.out.println("验签: ok");
            } else {
                System.err.println("验签失败!");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

这里我为了方便，直接将 Hash 算法用枚举代替：

```java
/**
 * Hash 算法
 */
public enum HashAlgorithm {

    MD5withRSA,

    SHA1withRSA,

    SHA256withRSA,

    SHA512withRSA,

    ;
}
```

是不是很简单？

# Go与Java消息签名互相验证

接下来我们来看下 Go 与 Java 生成的 rsa 消息签名如何互相验证。简单的说就是，Go 使用 Java 生成的密匙对，反之亦然！

所谓的互相验证就是如何正确加载 rsa 密匙对。首先我们要明白一点的是 Java 标准库生成的私钥格式默认是 PKCS8，公钥格式是 X.509。用于加载和解析 rsa 私钥的类是 `java.security.spec.PKCS8EncodedKeySpec`，这个类解析的是 PKCS8 格式。加载公钥的类是 `java.security.spec.X509EncodedKeySpec`，显然公钥要求是 X.509 格式。

因此当 Java 加载 Go 生成的密匙对，只需要将私钥转换为 PKCS8，公钥转换为 X.509 即可！而这个转换在 [RSA密匙对生成#使用 go 生成rsa密匙对](RSA密匙对生成.md#使用-go-生成rsa密匙对) 中也有相关代码，因此这里就不做任何说明了。唯一要注意的是，Java 加载的内容需要去除首尾 PEM Header 信息，并剔除数据体中的换行符。

那 Go 如何加载 Java 生成的密匙对呢？

一定要记住，Java 生成的私钥是 PKCS8 格式。所以，我们使用 PKCS8 进行解析即可：

```go
package main

import (
	"crypto/rsa"
	"crypto/x509"
	"encoding/base64"
	"fmt"
)

const (
	javaPkcs8Private = `
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQCrYVd9I348JQVUfvnDR+BYrAf+zK1v+HFjwBZJYX0vmmlC9vSlR5jZt805ZqIDvV3HmlDscDkJdq1BYhZ33bqP3VQIujeuV+bsc4PRsVbANxhJkBYqfNa7eRZONv8RZv5UEq4OElbt0uA02EfZmr+cjC5EkmFn8HHH9j9yFXCBMAJbywn8BLTYfD8+LHGRVvC9VBAUN2F1AbQounZG77vEuo2q6B7h8bg05AdSe8ioMBzbzHPs1NNvtd2jQ+wix762++UUaX5XoHn1+46NwTyiWddypeW6kBgOqeE9NuyJiMksjnSQLIfuNTDjLalK7xYuO2CZLMdq6v/a9e+VqSn9AgMBAAECggEADR9E7aX8EmrbP58BTLyCJwf1DMNrPDmPpQ2eJzw5s51lFeWG0ejrgTAbnJuJKDQlrBTawV/OXjUiCvGRutfVYxKVTp3DiavdWJl3rGShudH+IBPSPqqUXoJk/NQlL5XtP0622Qj6cvdEh4dP6Q96QGhTWpjCsIyuiZsHPJr7pEiCgqsEm4GRwmm6/FndJjzO6EhxJAoJ3h26O9TpMvxxz2uKMFr+7d18z8mc33pq/MxQ4k73mSmaXsqXIZ8WA6v70Jb8tlqXytNyE1EUXz2i1cAZUZdDnkn59VXyDn8m2LgF6cuvex10Rs8NZl8pZEd6KjPD7cynN9/m0mV6siuKWQKBgQDpQTcyOZUVNOnkqHtoD8pOZH9nbRNbKasDTtNo+62gQ0V6/Xg2RZpFs/x6pCy7JLTOo1bjicKgssBEuhkN2uTXjrrTbNVoxtWt3LvnCZyQjEXrUhFwQ3tX/0zYKlqFm+YodquxV1sUF/MstYd8zc+wgvMwwJwvkY2i6ZzaE4jBywKBgQC8F4pkBxhSMnGHrdR3zFdqrvq/E1ZU2gBAVOVDyODuhCSw735vC71yB8UhfazmK0JJFkkvPsnHYS7Xci+useX9eSALxTQQZD4mde/7kyRCnaGxeWuvpYbPZmb2r2tiI6sQcg31ldMDlmtuUlor57PVAMn3PXIIkXn/q3CzKqwqVwKBgHEFK4SHiCglYmtsA5exhvojEeSAYo+aigVpTWCiCkgMMH9ej7/kS9MYWe7m29G2AHHbjT9lQcvxsN3cOkWPWbDktLu7EVDafzDmMpnI+3bCsSJLuq1l1T8XMhnPs9rnZkcW8SxVwoji7e4s60BazRae7M0jlsopwbI9dcXpDyuvAoGAQxqO5/LUDXcro8EzfO3s/gWhNYwbj7gawNjI1m8TOGTyhiFbVRi2JLl67eDdT1x9kH+f6ngXGsekI378XfHf4JdXBHv9gB+TQLtwu6saE54TMrLj5U1DlDivYTDiOagJ/psCOeyS1MPUXg6JVwKZ2NMoOsm0JjRodvteAL2noI8CgYEAvsiKEy3RUnZydDA///Yg+SAz55fpiO1M/Vx1BVNLGJv7fRcTf2cPUDvAOI/ZSQq8+QqT5nSfuJG+yQDex82bsSHfpYF6ETTSz7WLkGjaUSqwfgBivLzPu1HuZHYchwdfumY6bbOd2v2G/In2lM0bw2qbhaNhauq9gy7WuWzKSlE=
`
)

func main() {
	bytes, _ := base64.StdEncoding.DecodeString(javaPkcs8Private)
	privateKey, err := x509.ParsePKCS8PrivateKey(bytes)
	if err != nil {
		panic(err)
	}

	fmt.Println(privateKey.(*rsa.PrivateKey).Size())
}
```

至于 X.509 格式的公钥。也是一样的：

```go
package main

import (
	"crypto/rsa"
	"crypto/x509"
	"encoding/base64"
	"fmt"
)

const (
	javaX509Public = `
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAq2FXfSN+PCUFVH75w0fgWKwH/sytb/hxY8AWSWF9L5ppQvb0pUeY2bfNOWaiA71dx5pQ7HA5CXatQWIWd926j91UCLo3rlfm7HOD0bFWwDcYSZAWKnzWu3kWTjb/EWb+VBKuDhJW7dLgNNhH2Zq/nIwuRJJhZ/Bxx/Y/chVwgTACW8sJ/AS02Hw/PixxkVbwvVQQFDdhdQG0KLp2Ru+7xLqNquge4fG4NOQHUnvIqDAc28xz7NTTb7Xdo0PsIse+tvvlFGl+V6B59fuOjcE8olnXcqXlupAYDqnhPTbsiYjJLI50kCyH7jUw4y2pSu8WLjtgmSzHaur/2vXvlakp/QIDAQAB
`
)

func main() {
	bytes, _ := base64.StdEncoding.DecodeString(javaX509Public)
	publicKey, err := x509.ParsePKIXPublicKey(bytes)
	if err != nil {
		panic(err)
	}

	fmt.Println(publicKey.(*rsa.PublicKey).Size())
}
```

--

完结，撒花🎉🎉🎉🎉~