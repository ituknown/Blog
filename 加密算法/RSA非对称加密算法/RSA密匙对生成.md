生成 RSA 密匙对有许多中方式，而在操作系统中使用最多的就是大名鼎鼎的 `openssl`。如果你的操作系统还没有 openssl，就需要手动安装：

Windows 用户需要到官网 [https://www.openssl.org](https://www.openssl.org) 进行下载安装。

Unix-Like 操作系统只需要一条命令即可按照。可以点击链接查看各种发行版对应的安装命令：[https://command-not-found.com/openssl](https://command-not-found.com/openssl)

# OpenSSL 生成 RSA 密匙对

## 生成私钥（默认PKCS#1格式）

openssl 生成的私钥默认格式为 PKCS#1，命令如下：

```bash
$ openssl genrsa -out rsa_private_key.pem rsa_length
```

`rsa_private_key.pem` 是要生成的私钥文件名。

`rsa_length` 则用于指定私钥，越长表示加密性越强（当前 1024 长度已经被证实可被破解）。

|**Note**|
|:-------|
|通过openssl生成密钥对，默认私钥是pkcs1格式，不输出公钥（因为公钥可以通过私钥导出）。|

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

## 生成公钥（默认X.509格式）

从RSA私钥中提取公钥，命令如下：

```bash
$ openssl rsa -in rsa_private_key.pem -out rsa_public_key.pem -pubout
```

`rsa_private_key.pem` 就是之前生成的私钥的文件名（公钥生成规则是通过计算私钥数值，所以必须是你之前生成的私钥文件）。

`rsa_public_key.pem` 表示要生成的公钥的文件名。

|**Note**|
|:-------|
|openssl从私钥中提取公钥比较特殊，他导出的其实是X.509格式的公钥。|

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

## 私钥转 PKCS8 格式（Java使用）

PKCS全称就是公钥密码标准（[The Public-Key Cryptography Standards (PKCS）](https://baike.baidu.com/item/PKCS/1042350?fr=aladdin/PKCS/1042350%3Ffr%3Daladdin)），如果你使用的是 Java 开发语言就需要将私钥转换为 PKCS8 格式。

所以这步为可选操作，根据实际需要~

命令如下：

```bash
$ openssl pkcs8 -topk8 -inform PEM -in rsa_private_key.pem -outform PEM -nocrypt > rsa_private_key_pkcs8.pem
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

# 关于RSA密钥格式互转以及格式确认

先说一下区别：

1. PKCS1是标准RSA私钥的格式规范（采用ASN1语法格式）。

2. PKCS8是对加密后的PKCS1私钥进行了描述，等于描述+PKCS1私钥（同样采用ASN1语法格式）。

通俗点讲吧，就是：PKCS1是一把钥匙，PKCS8就是把这个钥匙放到一个盒子里，并在盒子上贴个标签对这把钥匙做了说明，比如采用的是什么算法，密钥长度等，所以PKCS8不仅仅支持RSA算法，还支持其他算法的密钥。说白了，就是给这个钥匙捆绑了一个说明书，从而这个盒子不仅仅可装PKCS1的钥匙，还可以装其他类型的钥匙。

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

## 如何确认密匙对文件格式

生成 RSA 密匙对时在文件收尾都有形如 `-----BEGIN XXXX-----` 的内容，其实一个文件是什么格式我们完全可以通过这些信息进行确认：

**Public key formats supported（公钥文件支持的格式）：**

- PKCS#1 RSAPublicKey* (PEM header: BEGIN RSA PUBLIC KEY)
- X.509 SubjectPublicKeyInfo** (PEM header: BEGIN PUBLIC KEY)

**Private key formats supported (unencrypted)（私钥未加密文件支持的格式）：**

- PKCS#1 RSAPrivateKey** (PEM header: BEGIN RSA PRIVATE KEY)
- PKCS#8 PrivateKeyInfo* (PEM header: BEGIN PRIVATE KEY)
- JSON Web Key (JWK) Plaintext RSA Private Key "kty":"RSA"

**Encrypted private key formats supported（私钥加密文件支持的格式）：**

- PKCS#8 EncryptedPrivateKeyInfo** (PEM header: BEGIN ENCRYPTED PRIVATE KEY)
- PKCS#12 (PFX) with PKCS-8ShroudedKeyBag


--

因此，如果你不知道一个rsa密匙对文件是什么格式，可以通过这些信息进行确认。


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

    RSAPrivateKey privateKey = (RSAPrivateKey) keyPair.getPrivate();
    RSAPublicKey publicKey = (RSAPublicKey) keyPair.getPublic();

    System.out.println("私钥格式:" + privateKey.getFormat()); // PKCS#8
    System.out.println("公钥格式:" + publicKey.getFormat());  // X.509

    System.out.println("私钥内容: \n" + encodeToString(privateKey.getEncoded()));
    System.out.println("公钥内容: \n" + encodeToString(publicKey.getEncoded()));
}

private static String encodeToString(byte[] encoded) {
    return Base64.getEncoder().encodeToString(encoded);
}

private static byte[] decodeToByte(String decoded) {
    return Base64.getDecoder().decode(decoded);
}
```

输出示例：

```
私钥格式:PKCS#8
公钥格式:X.509

私钥内容:
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQCrYVd9I348JQVUfvnDR+BYrAf+zK1v+HFjwBZJYX0vmmlC9vSlR5jZt805ZqIDvV3HmlDscDkJdq1BYhZ33bqP3VQIujeuV+bsc4PRsVbANxhJkBYqfNa7eRZONv8RZv5UEq4OElbt0uA02EfZmr+cjC5EkmFn8HHH9j9yFXCBMAJbywn8BLTYfD8+LHGRVvC9VBAUN2F1AbQounZG77vEuo2q6B7h8bg05AdSe8ioMBzbzHPs1NNvtd2jQ+wix762++UUaX5XoHn1+46NwTyiWddypeW6kBgOqeE9NuyJiMksjnSQLIfuNTDjLalK7xYuO2CZLMdq6v/a9e+VqSn9AgMBAAECggEADR9E7aX8EmrbP58BTLyCJwf1DMNrPDmPpQ2eJzw5s51lFeWG0ejrgTAbnJuJKDQlrBTawV/OXjUiCvGRutfVYxKVTp3DiavdWJl3rGShudH+IBPSPqqUXoJk/NQlL5XtP0622Qj6cvdEh4dP6Q96QGhTWpjCsIyuiZsHPJr7pEiCgqsEm4GRwmm6/FndJjzO6EhxJAoJ3h26O9TpMvxxz2uKMFr+7d18z8mc33pq/MxQ4k73mSmaXsqXIZ8WA6v70Jb8tlqXytNyE1EUXz2i1cAZUZdDnkn59VXyDn8m2LgF6cuvex10Rs8NZl8pZEd6KjPD7cynN9/m0mV6siuKWQKBgQDpQTcyOZUVNOnkqHtoD8pOZH9nbRNbKasDTtNo+62gQ0V6/Xg2RZpFs/x6pCy7JLTOo1bjicKgssBEuhkN2uTXjrrTbNVoxtWt3LvnCZyQjEXrUhFwQ3tX/0zYKlqFm+YodquxV1sUF/MstYd8zc+wgvMwwJwvkY2i6ZzaE4jBywKBgQC8F4pkBxhSMnGHrdR3zFdqrvq/E1ZU2gBAVOVDyODuhCSw735vC71yB8UhfazmK0JJFkkvPsnHYS7Xci+useX9eSALxTQQZD4mde/7kyRCnaGxeWuvpYbPZmb2r2tiI6sQcg31ldMDlmtuUlor57PVAMn3PXIIkXn/q3CzKqwqVwKBgHEFK4SHiCglYmtsA5exhvojEeSAYo+aigVpTWCiCkgMMH9ej7/kS9MYWe7m29G2AHHbjT9lQcvxsN3cOkWPWbDktLu7EVDafzDmMpnI+3bCsSJLuq1l1T8XMhnPs9rnZkcW8SxVwoji7e4s60BazRae7M0jlsopwbI9dcXpDyuvAoGAQxqO5/LUDXcro8EzfO3s/gWhNYwbj7gawNjI1m8TOGTyhiFbVRi2JLl67eDdT1x9kH+f6ngXGsekI378XfHf4JdXBHv9gB+TQLtwu6saE54TMrLj5U1DlDivYTDiOagJ/psCOeyS1MPUXg6JVwKZ2NMoOsm0JjRodvteAL2noI8CgYEAvsiKEy3RUnZydDA///Yg+SAz55fpiO1M/Vx1BVNLGJv7fRcTf2cPUDvAOI/ZSQq8+QqT5nSfuJG+yQDex82bsSHfpYF6ETTSz7WLkGjaUSqwfgBivLzPu1HuZHYchwdfumY6bbOd2v2G/In2lM0bw2qbhaNhauq9gy7WuWzKSlE=

公钥内容:
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAq2FXfSN+PCUFVH75w0fgWKwH/sytb/hxY8AWSWF9L5ppQvb0pUeY2bfNOWaiA71dx5pQ7HA5CXatQWIWd926j91UCLo3rlfm7HOD0bFWwDcYSZAWKnzWu3kWTjb/EWb+VBKuDhJW7dLgNNhH2Zq/nIwuRJJhZ/Bxx/Y/chVwgTACW8sJ/AS02Hw/PixxkVbwvVQQFDdhdQG0KLp2Ru+7xLqNquge4fG4NOQHUnvIqDAc28xz7NTTb7Xdo0PsIse+tvvlFGl+V6B59fuOjcE8olnXcqXlupAYDqnhPTbsiYjJLI50kCyH7jUw4y2pSu8WLjtgmSzHaur/2vXvlakp/QIDAQAB
```

## 使用 Go 生成RSA密匙对

```go
package main

import (
	"crypto/rand"
	"crypto/rsa"
	"crypto/x509"
	"encoding/pem"
	"os"
)

func main() {
	// 生成私钥
	privateKey, _ := rsa.GenerateKey(rand.Reader, 2048)

	// 输出 pkcs1 私钥
	pem.Encode(os.Stdout, &pem.Block{
		Type:    "RSA PRIVATE KEY",
		Headers: nil,
		Bytes:   x509.MarshalPKCS1PrivateKey(privateKey),
	})

	// 输出 pkcs1 公钥
	pem.Encode(os.Stdout, &pem.Block{
		Type:    "RSA PUBLIC KEY",
		Headers: nil,
		Bytes:   x509.MarshalPKCS1PublicKey(&privateKey.PublicKey),
	})

	// 输出 pkcs8 私钥
	pkcs8Private, _ := x509.MarshalPKCS8PrivateKey(privateKey)
	pem.Encode(os.Stdout, &pem.Block{
		Type:    "PRIVATE KEY",
		Headers: nil,
		Bytes:   pkcs8Private,
	})

	// 输出 X.509 公钥
	x509Public, _ := x509.MarshalPKIXPublicKey(&privateKey.PublicKey)
	pem.Encode(os.Stdout, &pem.Block{
		Type:    "PUBLIC KEY",
		Headers: nil,
		Bytes:   x509Public,
	})
}
```

输出示例：

```
pkcs1 私钥内容:

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


pkcs1 公钥内容:

-----BEGIN RSA PUBLIC KEY-----
MIIBCgKCAQEA1/kSthok32KKq/rSeeBkGJBWsRxq74ahToYYx9j2UcFLifE/SuEJ
Jz54/Qplzg/rYQypUfxixwr1flFYpnJOrXAP+JBM1cpNovLHOukUr093jqWi+8It
rIMbMZwOCFd1GdTGIvrTeqVZ5Hv1X6YVX2Q4auzuYAhYz7/U0Ybkdymm7zalB8nW
eB6WjkSb2lPBAnx+9EWSft8VYkr76YO9qbu98Yntt/fPPYsdMToyWb34Echf0E7H
2ZJuTik01IqUB/xlBGRVjXYO96OZ/krwQbUMrJW5zl7jRBjPCeD30lhSfUv/YMwA
znz5V5SJFbJH1jGdZmcw8Y1PefFH1nXz9QIDAQAB
-----END RSA PUBLIC KEY-----


pkcs8 私钥内容:

-----BEGIN PRIVATE KEY-----
MIIEvQIBADANBgkqhkiG9w0BAQEFAASCBKcwggSjAgEAAoIBAQDX+RK2GiTfYoqr
+tJ54GQYkFaxHGrvhqFOhhjH2PZRwUuJ8T9K4QknPnj9CmXOD+thDKlR/GLHCvV+
UVimck6tcA/4kEzVyk2i8sc66RSvT3eOpaL7wi2sgxsxnA4IV3UZ1MYi+tN6pVnk
e/VfphVfZDhq7O5gCFjPv9TRhuR3KabvNqUHydZ4HpaORJvaU8ECfH70RZJ+3xVi
Svvpg72pu73xie239889ix0xOjJZvfgRyF/QTsfZkm5OKTTUipQH/GUEZFWNdg73
o5n+SvBBtQyslbnOXuNEGM8J4PfSWFJ9S/9gzADOfPlXlIkVskfWMZ1mZzDxjU95
8UfWdfP1AgMBAAECggEBALgImOYtrv/fM3VK/TE3gXPhAs5W/dRvb2K/J7yb54FQ
6fJ4bUt8KNWVCXnt80Q9EoCXTmz7WJxtfTWPUbA7tUlbNXDEXYTLp45TvGs5lBOy
7pjSEejf1RWsfm6uoNKA29sQEWpbO/vFjK0C59sCZjHr75tFm/afBja5KzsD7AiR
FzQinWDI68xVEVMjUCcrm8cGkIyK+Yqq5WDXuu76NyeqiLPAZ2DOjMeHP8QioJ03
EqDCssZgv4e/t/oGilctx+iMJfN++cmcRGh5PD/6OyiF64PZ6rjB0G1EGgd32unM
usBai72tveY1ZJBrQR8lBOesCX4Gz2wL2PsATUgMVr0CgYEA3eaRD/bVrnUoON8M
2BZ6OsL4cB+ZnGsQFwSvcX5nu4c2VhfFeT75FG2Wd8Y5PsrBY0WaCswFUhqICfQK
zoi2lpNaj3ubORxhKskWM5eLlnLqsZykIC9d6f/93UJpmstgKRtQdzGR4nlWP/SF
+6PVwcvUES+0j2pjyVAS2Y50RVcCgYEA+SlQZdtuzA2TQRxW/YlUeHz/F85TLPlL
7vpcZ5lKA5DNebfeDCj79CLntVwagSFrJEWj5y9/wAQNpw6Cssm+efVtYDNt/vMM
3CD1He4ZuLIOja5fmRFjiTPNPNwb7Bld+Ox1S3sHG74pshqISS0gZCbKswlIE2E3
3h6kHWzOFZMCgYBxpM/OOUAeZOoAllthn8FhrtDFu6lULpDC+SdRHMfLhugRF4OP
IO03xRg2p1JSEk0QpmjJ9ssSzXu2fUG7nNDGNRlAOrG4ggxReULhkU9v8dK4u9xi
j1+to+lEE+s518abxt1i7gQk1LeTcYvc4H9tDRLC6oeym24Xf+bnfMmXaQKBgDjl
AzPPEJTNwMNMalzIoB5HRKS1+244mus+Bh2t9iWsSg8yOiUCss3Drq9OEctMyz9k
G5It2XkKM+fiu1uCD9mfflNKSyK9c0FM0Uv9U6hGGGan27KDMvxgdMwh4spd3IR7
0ijjNfNS0AiYYGh9Qnk8FmsdgnTSbURuMMF7V++7AoGAGFQ4WRnLaOsCJi4UZlBu
o7y8o/ZxpeiYBGpLUXYjPYWH6gbOZMmJFW3i6vDk8tI8Yd6JEDwZLHbQXsxysWg8
6qMS7ZjaS6+r03KYxfD1nPhTJxdvq5LOjfq/GfXs3BbuL9lx+T3HeIudDE8WT9fn
vZe155WF7kEcJ7kQJqQ4Mpw=
-----END PRIVATE KEY-----


X.509 公钥:

-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA1/kSthok32KKq/rSeeBk
GJBWsRxq74ahToYYx9j2UcFLifE/SuEJJz54/Qplzg/rYQypUfxixwr1flFYpnJO
rXAP+JBM1cpNovLHOukUr093jqWi+8ItrIMbMZwOCFd1GdTGIvrTeqVZ5Hv1X6YV
X2Q4auzuYAhYz7/U0Ybkdymm7zalB8nWeB6WjkSb2lPBAnx+9EWSft8VYkr76YO9
qbu98Yntt/fPPYsdMToyWb34Echf0E7H2ZJuTik01IqUB/xlBGRVjXYO96OZ/krw
QbUMrJW5zl7jRBjPCeD30lhSfUv/YMwAznz5V5SJFbJH1jGdZmcw8Y1PefFH1nXz
9QIDAQAB
-----END PUBLIC KEY-----
```

# 参考资料

https://www.cryptosys.net/pki/rsakeyformats.html