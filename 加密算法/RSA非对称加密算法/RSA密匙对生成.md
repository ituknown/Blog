生成 RSA 密匙对有许多中方式，而在操作系统中使用最多的就是大名鼎鼎的 `openssl`。如果你的操作系统还没有 openssl，就需要手动安装：

Windows 用户需要到官网 [https://www.openssl.org](https://www.openssl.org) 进行下载安装。

Unix-Like 操作系统只需要一条命令即可按照。可以点击链接查看各种发行版对应的安装命令：[https://command-not-found.com/openssl](https://command-not-found.com/openssl)

# OpenSSL 生成 RSA 密匙对

## 生成私钥（默认PKCS#1格式）

生成 RSA 私钥命令如下（Windows 用户可以使用 Git 客户端）：

```bash
$ openssl genrsa -out [rsa_private_key.pem] [rsa_length]
```

`rsa_private_key.pem` 就是要生成的私钥文件名，可以随意指定。

`rsa_length` 则用于指定私钥，越长表示加密型越强（当前 1024 长度已经被证实可被破解）。

**示例：**

```bash
$ openssl genrsa -out id_rsa.pem 2048
Generating RSA private key, 2048 bit long modulus
.......................+++
............................................................................................................................+++
e is 65537 (0x10001)
```

命令执行完成之后就会看到在当前目录下多了一个 id_rsa.pem 私钥文件：

```bash
$ ls
id_rsa.pem
```

这里生成的私钥文件内容如下：

```
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEA2wmgGrNDxFWPEW/rTYRud1FI5sBmIFU9Ac943UUQHIfizWC3
9j2ZaLbM0eJGAzeYnaHlcM/DUW+dMs9hT9QNgEwJaCeLJgcnROMuqwcu+fuVWtJq
pGBOOkUDLfDpv6PQRlcM9V+odTcC3whxmZL7IW/L8XmSc5oIjA+AenLSioJC+mSy
r3x3G+xxgKBGh7F2UfNC8SHO5KurtTasynTVMgsAboY+k+gTt9HkLMO6hWvtSq1w
rocWeU2K4Lyg2Gx+XqnSvLy1xI9IWAbl1drHAdGRjPXOxn89suie+kR+hNGiMBXt
FwykZ4AoKvJBNf8ZjmDQtrG4qhyM0MtzjzejDwIDAQABAoIBAHcAlcGr34eNzHr2
yWqGJtYgjXEA2vt6E/kl1U6yNPyrPyfCAnCAKxbfKgxl2Q/qZrpNuTA/UKiv8UIn
tYD/fo5TwVCbG1h/zm/41eQJtRYP/6cmZtYDlLzHveB54SVDRObSaLHvVMDKMH8o
QT/kwRf7e1woZs7zwu0FhxH/zC9/cMBBiWGKpB4xGauPox3GEII2vdp1Kg7+yPHQ
qcVGcZYw4f+nYELX+tcGfk6tzOQGHiKX5QCIr6zTuV5/TlxkUeFt2AAWQQ+WyC8G
oVaY/LPKQkK5ruGR7KweFVMEJq6uW9r+GC29dC8JU7aPi4zQsYWRBuLa26o3uoir
3nEnjJECgYEA7kcA4QIyxIelLEH4F8vWEoOq8k9VAQ4oOwdEdRQmuLyeCSOH/yjk
osE0yUhuEP0qqOLBmWisX4ubAX8/rkVzpV5cMaQbakbFxsdE8eD84IyDvvDl9ZNx
77nZhSUAGLhGxZl0vmEjCtYn4eRUUfXonO+6Hq5MtlTkk7iej1DuiZsCgYEA61RH
+5XvbMZPhn6fVZAewZoXBW63Y51c443Edvfq7crWS5FiwwEQFUzwcIrzRAclzRCF
lypgjvE9Nks87GSfKP8npHnVldIU21ph7GJOvfH/OTNzYwiC5jHsxDWDe63DOdDl
Z73EMvc9/2KejVZJxsJSr8L8b9rELt8K1SqHLZ0CgYB1LG1+OPqGMr5C+V3rNc3b
5OvXpLOEhVW7UWOUv4gbKIX+sH5cN9vXPXOb2AZoTFcCHiBeNF8vzRM4tT63kGfC
Vpi3wJ/Kr2rqyWtaFu3D7tWKvCQZk5xf6uDQ3NUjUttaqJAEXvhAh/es2f+tHmR5
aFn6ZHRM5YpSKmOuznm/KQKBgEA9N5Ej8sG8uMS8F/6hYuPyjko7WPBmao7rii/3
qTfUYP8r5svYpG1MHW57X8C4OsdptS34sXcTv2Ps0eqt5Qyp6kZpw7YsLOlcGNMQ
WvJa9xLESsFD94ejJUDCYuwGGP7VWWoiUUagW1o1r5Zhca5CKZQ1I/fcviUVWb2q
6E+5AoGAFljHDtLoIwpuWWOjXMgqIBqfYxs+ZSDVRL7oq9KjcpCdQBhwB6aZ1g23
o6kbTwiroJBJegLGs3v8fytAp+7c8j6SGst4VdSlNBk9Illf13Jnnbv8kAf3L7nx
nWNn80THXeNv9gFWc0jOAdl4HwiHFopOdtvX/qPCWLqsFvdEkXY=
-----END RSA PRIVATE KEY-----
```

## 生成公钥（默认PKCS#8格式）

接着生成公钥，命令如下：

```bash
$ openssl rsa -in [rsa_private_key.pem] -out [rsa_public_key.pem] -pubout
```

`rsa_private_key.pem` 就是之前生成的私钥的文件名（公钥生成规则是通过计算私钥数值，所以必须是你之前生成的私钥文件）。

`rsa_public_key.pem` 表示要生成的公钥的文件名。

**示例：**

```bash
$ openssl rsa -in id_rsa.pem -out id_rsa_pub.pem -pubout
```

之后的就产生了对应的公钥文件 id_rsa_pub：

```bash
$ ls
id_rsa.pem  id_rsa_pub.pem
```

改示例中的公钥文件内容如下：

```
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2wmgGrNDxFWPEW/rTYRu
d1FI5sBmIFU9Ac943UUQHIfizWC39j2ZaLbM0eJGAzeYnaHlcM/DUW+dMs9hT9QN
gEwJaCeLJgcnROMuqwcu+fuVWtJqpGBOOkUDLfDpv6PQRlcM9V+odTcC3whxmZL7
IW/L8XmSc5oIjA+AenLSioJC+mSyr3x3G+xxgKBGh7F2UfNC8SHO5KurtTasynTV
MgsAboY+k+gTt9HkLMO6hWvtSq1wrocWeU2K4Lyg2Gx+XqnSvLy1xI9IWAbl1drH
AdGRjPXOxn89suie+kR+hNGiMBXtFwykZ4AoKvJBNf8ZjmDQtrG4qhyM0Mtzjzej
DwIDAQAB
-----END PUBLIC KEY-----
```

|**Note**|
|:-------|
|openssl从私钥中提取公钥比较特殊，他导出的其实是pkcs8格式的公钥（可能是为了迎合 Java）。|

## 私钥转 PKCS8 格式（Java使用）

PKCS全称就是公钥密码标准（[The Public-Key Cryptography Standards (PKCS）](https://baike.baidu.com/item/PKCS/1042350?fr=aladdin/PKCS/1042350%3Ffr%3Daladdin)），如果你使用的是 Java 开发语言就需要将私钥转换为 PKCS8 格式。

所以这步为可选操作，根据实际需要~

命令如下：

```bash
$ openssl pkcs8 -topk8 -inform PEM -in [rsa_private_key.pem] -outform PEM -nocrypt > [rsa_private_key_pkcs8.pem]
```

其中 `rsa_private_key_pkcs8.pem` 就是要转换输出的 PSCK8 文件。

|**Note**|
|:-----|
|注意将 `rsa_private_key.pem` 替换为你自己的私钥文件。|

**命令示例：**

```bash
$ openssl pkcs8 -topk8 -inform PEM -in id_rsa.pem -outform PEM -nocrypt > id_rsa_pkcs8.pem
```

现在除了私钥公钥之外，当前目录下还多了一个 PKCS8 格式私钥文件：

```bash
$ ls
id_rsa.pem  id_rsa_pkcs8.pem  id_rsa_pub.pem
```

PKCS8 私钥内容如下：

```
-----BEGIN PRIVATE KEY-----
MIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQDbCaAas0PEVY8R
b+tNhG53UUjmwGYgVT0Bz3jdRRAch+LNYLf2PZlotszR4kYDN5idoeVwz8NRb50y
z2FP1A2ATAloJ4smBydE4y6rBy75+5Va0mqkYE46RQMt8Om/o9BGVwz1X6h1NwLf
CHGZkvshb8vxeZJzmgiMD4B6ctKKgkL6ZLKvfHcb7HGAoEaHsXZR80LxIc7kq6u1
NqzKdNUyCwBuhj6T6BO30eQsw7qFa+1KrXCuhxZ5TYrgvKDYbH5eqdK8vLXEj0hY
BuXV2scB0ZGM9c7Gfz2y6J76RH6E0aIwFe0XDKRngCgq8kE1/xmOYNC2sbiqHIzQ
y3OPN6MPAgMBAAECggEAdwCVwavfh43MevbJaoYm1iCNcQDa+3oT+SXVTrI0/Ks/
J8ICcIArFt8qDGXZD+pmuk25MD9QqK/xQie1gP9+jlPBUJsbWH/Ob/jV5Am1Fg//
pyZm1gOUvMe94HnhJUNE5tJose9UwMowfyhBP+TBF/t7XChmzvPC7QWHEf/ML39w
wEGJYYqkHjEZq4+jHcYQgja92nUqDv7I8dCpxUZxljDh/6dgQtf61wZ+Tq3M5AYe
IpflAIivrNO5Xn9OXGRR4W3YABZBD5bILwahVpj8s8pCQrmu4ZHsrB4VUwQmrq5b
2v4YLb10LwlTto+LjNCxhZEG4trbqje6iKvecSeMkQKBgQDuRwDhAjLEh6UsQfgX
y9YSg6ryT1UBDig7B0R1FCa4vJ4JI4f/KOSiwTTJSG4Q/Sqo4sGZaKxfi5sBfz+u
RXOlXlwxpBtqRsXGx0Tx4PzgjIO+8OX1k3HvudmFJQAYuEbFmXS+YSMK1ifh5FRR
9eic77oerky2VOSTuJ6PUO6JmwKBgQDrVEf7le9sxk+Gfp9VkB7BmhcFbrdjnVzj
jcR29+rtytZLkWLDARAVTPBwivNEByXNEIWXKmCO8T02SzzsZJ8o/yekedWV0hTb
WmHsYk698f85M3NjCILmMezENYN7rcM50OVnvcQy9z3/Yp6NVknGwlKvwvxv2sQu
3wrVKoctnQKBgHUsbX44+oYyvkL5Xes1zdvk69eks4SFVbtRY5S/iBsohf6wflw3
29c9c5vYBmhMVwIeIF40Xy/NEzi1PreQZ8JWmLfAn8qvaurJa1oW7cPu1Yq8JBmT
nF/q4NDc1SNS21qokARe+ECH96zZ/60eZHloWfpkdEzlilIqY67Oeb8pAoGAQD03
kSPywby4xLwX/qFi4/KOSjtY8GZqjuuKL/epN9Rg/yvmy9ikbUwdbntfwLg6x2m1
LfixdxO/Y+zR6q3lDKnqRmnDtiws6VwY0xBa8lr3EsRKwUP3h6MlQMJi7AYY/tVZ
aiJRRqBbWjWvlmFxrkIplDUj99y+JRVZvaroT7kCgYAWWMcO0ugjCm5ZY6NcyCog
Gp9jGz5lINVEvuir0qNykJ1AGHAHppnWDbejqRtPCKugkEl6Asaze/x/K0Cn7tzy
PpIay3hV1KU0GT0iWV/Xcmedu/yQB/cvufGdY2fzRMdd42/2AVZzSM4B2XgfCIcW
ik5229f+o8JYuqwW90SRdg==
-----END PRIVATE KEY-----
```

--

正常来说到此就结束了，但是如果是给程序（如 Java）使用的话还不行，我们还需要对文件内容做下调整：删除文件头尾两段并去掉回车换行符。

|**Note**|
|:-------|
|程序（如Java）主要使用的是 PKCS8 私钥文件和公钥文件，因为 PKCS8 私钥文件本质上就是私钥文件内容，只是格式不同而已。所以我们调整私钥PKCS8文件内容和公钥内容即可。|

即删除 PKCS8 私钥文件（id_rsa_pkcs8.pem）首尾行：

```plain
-----BEGIN PRIVATE KEY-----
-----END PRIVATE KEY-----
```

删除公钥文件（id_rsa_pub.pem）首尾行：

```plain
-----BEGIN PUBLIC KEY-----
-----END PUBLIC KEY-----
```

最后再去掉两个文件中的换行符即可。

--

调整后的私钥PKCS8：

```
MIIEvAIBADANBgkqhkiG9w0BAQEFAASCBKYwggSiAgEAAoIBAQDbCaAas0PEVY8Rb+tNhG53UUjmwGYgVT0Bz3jdRRAch+LNYLf2PZlotszR4kYDN5idoeVwz8NRb50yz2FP1A2ATAloJ4smBydE4y6rBy75+5Va0mqkYE46RQMt8Om/o9BGVwz1X6h1NwLfCHGZkvshb8vxeZJzmgiMD4B6ctKKgkL6ZLKvfHcb7HGAoEaHsXZR80LxIc7kq6u1NqzKdNUyCwBuhj6T6BO30eQsw7qFa+1KrXCuhxZ5TYrgvKDYbH5eqdK8vLXEj0hYBuXV2scB0ZGM9c7Gfz2y6J76RH6E0aIwFe0XDKRngCgq8kE1/xmOYNC2sbiqHIzQy3OPN6MPAgMBAAECggEAdwCVwavfh43MevbJaoYm1iCNcQDa+3oT+SXVTrI0/Ks/J8ICcIArFt8qDGXZD+pmuk25MD9QqK/xQie1gP9+jlPBUJsbWH/Ob/jV5Am1Fg//pyZm1gOUvMe94HnhJUNE5tJose9UwMowfyhBP+TBF/t7XChmzvPC7QWHEf/ML39wwEGJYYqkHjEZq4+jHcYQgja92nUqDv7I8dCpxUZxljDh/6dgQtf61wZ+Tq3M5AYeIpflAIivrNO5Xn9OXGRR4W3YABZBD5bILwahVpj8s8pCQrmu4ZHsrB4VUwQmrq5b2v4YLb10LwlTto+LjNCxhZEG4trbqje6iKvecSeMkQKBgQDuRwDhAjLEh6UsQfgXy9YSg6ryT1UBDig7B0R1FCa4vJ4JI4f/KOSiwTTJSG4Q/Sqo4sGZaKxfi5sBfz+uRXOlXlwxpBtqRsXGx0Tx4PzgjIO+8OX1k3HvudmFJQAYuEbFmXS+YSMK1ifh5FRR9eic77oerky2VOSTuJ6PUO6JmwKBgQDrVEf7le9sxk+Gfp9VkB7BmhcFbrdjnVzjjcR29+rtytZLkWLDARAVTPBwivNEByXNEIWXKmCO8T02SzzsZJ8o/yekedWV0hTbWmHsYk698f85M3NjCILmMezENYN7rcM50OVnvcQy9z3/Yp6NVknGwlKvwvxv2sQu3wrVKoctnQKBgHUsbX44+oYyvkL5Xes1zdvk69eks4SFVbtRY5S/iBsohf6wflw329c9c5vYBmhMVwIeIF40Xy/NEzi1PreQZ8JWmLfAn8qvaurJa1oW7cPu1Yq8JBmTnF/q4NDc1SNS21qokARe+ECH96zZ/60eZHloWfpkdEzlilIqY67Oeb8pAoGAQD03kSPywby4xLwX/qFi4/KOSjtY8GZqjuuKL/epN9Rg/yvmy9ikbUwdbntfwLg6x2m1LfixdxO/Y+zR6q3lDKnqRmnDtiws6VwY0xBa8lr3EsRKwUP3h6MlQMJi7AYY/tVZaiJRRqBbWjWvlmFxrkIplDUj99y+JRVZvaroT7kCgYAWWMcO0ugjCm5ZY6NcyCogGp9jGz5lINVEvuir0qNykJ1AGHAHppnWDbejqRtPCKugkEl6Asaze/x/K0Cn7tzyPpIay3hV1KU0GT0iWV/Xcmedu/yQB/cvufGdY2fzRMdd42/2AVZzSM4B2XgfCIcWik5229f+o8JYuqwW90SRdg==
```

调整后的公钥：

```
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA2wmgGrNDxFWPEW/rTYRud1FI5sBmIFU9Ac943UUQHIfizWC39j2ZaLbM0eJGAzeYnaHlcM/DUW+dMs9hT9QNgEwJaCeLJgcnROMuqwcu+fuVWtJqpGBOOkUDLfDpv6PQRlcM9V+odTcC3whxmZL7IW/L8XmSc5oIjA+AenLSioJC+mSyr3x3G+xxgKBGh7F2UfNC8SHO5KurtTasynTVMgsAboY+k+gTt9HkLMO6hWvtSq1wrocWeU2K4Lyg2Gx+XqnSvLy1xI9IWAbl1drHAdGRjPXOxn89suie+kR+hNGiMBXtFwykZ4AoKvJBNf8ZjmDQtrG4qhyM0MtzjzejDwIDAQAB
```

# 关于RSA密钥的pkcs1与pkcs8格式互转

先说一下区别：

1. PKCS1是标准RSA私钥的格式规范（采用ASN1语法格式）。

2. PKCS8是对加密后的PKCS1私钥进行了描述，等于描述+PKCS1私钥（同样采用ASN1语法格式）。

通俗点讲吧，就是：PKCS1是一把钥匙，PKCS8就是把这个钥匙放到一个盒子里，并在盒子上贴个标签对这把钥匙做了说明，比如采用的是什么算法，密钥长度等，所以PKCS8不仅仅支持RSA算法，还支持其他算法的密钥。说白了，就是给这个钥匙捆绑了一个说明书，从而这个盒子不仅仅可装PKCS1的钥匙，还可以装其他类型的钥匙。

另外，java中的加解密用的包，默认生成的是私钥pkcs1，公钥pkcs8。另外，openssl最早生成的公钥私钥默认都是pkcs1格式的，但是在后来的版本中，公钥默认为了pkcs8格式（私钥还是pkcs1格式，或许是为了迎合java吧，个人猜测）。

## 将pkcs1格式的私钥转为pkcs8格式

```bash
openssl pkcs8 -topk8 -inform PEM -in id_rsa_cert.pem -outform PEM -nocrypt -out id_rsa_cert_pkcs8.pem
```

|**Note**|
|:-------|
|openssl的私钥默认是pkcs1结构的pem格式。|


## 将PKCS8格式私钥再转换为PKCS1格式

```bash
openssl rsa -in id_rsa_cert_pkcs8.pem -out id_rsa_cert.pem
```

## 将pkcs8公钥转pkcs1公钥

```bash
openssl rsa -pubin -in rsa_pub_pkcs8.pem -RSAPublicKey_out -out rsa_pub_pkcs1.pem
```

## 将pkcs1公钥转换为pkcs8公钥

```bash
openssl rsa -RSAPublicKey_in -in pub_pkcs1.pem -pubout -out pub_pkcs8.pem
```

# 扩展

## 使用Java生成RSA密匙对

Java 生成密匙对比较简单，直接上码：

```java
public static void main(String[] args) throws Exception {
    genKeyPair();
}

public static void genKeyPair() throws Exception {

    KeyPairGenerator keyPairGen = KeyPairGenerator.getInstance("RSA");

    keyPairGen.initialize(2048); // 密匙长度

    KeyPair keyPair = keyPairGen.generateKeyPair();

    RSAPublicKey publicKey = (RSAPublicKey) keyPair.getPublic();     // pkcs1
    RSAPrivateKey privateKey = (RSAPrivateKey) keyPair.getPrivate(); // pkcs8

    System.out.println("PrivateKey: \n" + encodeToString(privateKey.getEncoded()));
    System.out.println("PublicKey: \n" + encodeToString(publicKey.getEncoded()));
}

private static String encodeToString(byte[] encoded) {
    return Base64.getEncoder().encodeToString(encoded);
}

private static byte[] decodeToByte(String decoded) {
    return Base64.getDecoder().decode(decoded);
}
```

## 使用 Go 生成RSA密匙对

```go
package main

import (
	"crypto/rand"
	"crypto/rsa"
	"crypto/x509"
	"encoding/pem"
	"fmt"
)

func main() {
	// 生成私钥
	privateKey, _ := rsa.GenerateKey(rand.Reader, 2048)

	// 私钥转换为 pkcs1
	pkcs1Private := x509.MarshalPKCS1PrivateKey(privateKey)
	// 提取 pkcs1 公钥
	pkcs1Public := x509.MarshalPKCS1PublicKey(&privateKey.PublicKey)

	// 私钥转换为 pkcs8
	pkcs8Private, _ := x509.MarshalPKCS8PrivateKey(privateKey)

	// 输出 pkcs1 私钥
	pkcs1PrivateByte := pem.EncodeToMemory(&pem.Block{
		Type:    "PKCS1 PRIVATE",
		Headers: nil,
		Bytes:   pkcs1Private,
	})
	fmt.Println(string(pkcs1PrivateByte))

	// 输出 pkcs1 公钥
	pkcs1PublicByte := pem.EncodeToMemory(&pem.Block{
		Type:    "PKCS1 PUBLIC",
		Headers: nil,
		Bytes:   pkcs1Public,
	})
	fmt.Println(string(pkcs1PublicByte))

	// 输出 pkcs8 公钥
	pkcs8PrivateByte := pem.EncodeToMemory(&pem.Block{
		Type:    "PKCS8 PRIVATE",
		Headers: nil,
		Bytes:   pkcs8Private,
	})
	fmt.Println(string(pkcs8PrivateByte))
}
```