---
title: 使用TLS对Docker Socket进行授权限制
date: 2017-03-27 15:20:23
tags: [docker,https,tls]
---
# 使用TLS对Docker Socket进行授权限制

本文翻译自：[Protect the Docker daemon socket](https://docs.docker.com/engine/security/https/)

通常情况下，Docker主要通过无网络的Unix socket与deamon进行通信。HTTP socket同样可以作为可选的方式进行交互。

如果需要将以安全的方式通过网络进行交互，可以通过设置`tlsverify`标识，并指定`tlscacert`为信任的CA证书来启用TLS。

服务端方面仅允许通过CA证书认证的客户端进行连接，客户端方面需要通过签名的证书与服务器进行通信。

> **警告**：使用TLS及CA证书管理具有一定深度.在生产环境使用之前，请确保自己已掌握OpenSSL、x509及TLS相关知识。

> **警告**：下述TLS命令仅可在Linux下生成可用的秘钥集。macOS的OpenSSL版本与Docker使用的证书存在兼容性问题。

## 使用OpenSSL创建服务端、客户端秘钥及服务端证书

> **提示**：使用Docker daemon的域名替换下述样例中的`$HOST`

首先生成CA证书的公、私秘钥:

``` bash
$ openssl genrsa -aes256 -out ca-key.pem 4096
Generating RSA private key, 4096 bit long modulus
............................................................................................................................................................................................++
........++
e is 65537 (0x10001)
Enter pass phrase for ca-key.pem:
Verifying - Enter pass phrase for ca-key.pem:
$ openssl req -new -x509 -days 365 -key ca-key.pem -sha256 -out ca.pem
Enter pass phrase for ca-key.pem:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:
State or Province Name (full name) [Some-State]:Queensland
Locality Name (eg, city) []:Brisbane
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Docker Inc
Organizational Unit Name (eg, section) []:Sales
Common Name (e.g. server FQDN or YOUR name) []:$HOST
Email Address []:Sven@home.org.au
```

获得CA证书后，可以生成服务端key及授权签名请求（CSR）。确保之前输入的Common Name（例如：完全域名或你的名字）输入的内容与用于连接docker的hostname匹配。

> **提示**：使用Docker daemon的域名替换下述样例中的`$HOST`

``` bash
$ openssl genrsa -out server-key.pem 4096
Generating RSA private key, 4096 bit long modulus
.....................................................................++
.................................................................................................++
e is 65537 (0x10001)
$ openssl req -subj "/CN=$HOST" -sha256 -new -key server-key.pem -out server.csr
```

接下来我们将使用CA证书对共有秘钥进行签名：

鉴于IP地址可以像域名一样建立TLS链接，所以我们需要在生成证书时对其显式声明。例如，允许通过`10.10.10.20`和`127.0.0.1`进行连接：

``` bash
$ echo subjectAltName = DNS:$HOST,IP:10.10.10.20,IP:127.0.0.1 > extfile.cnf

$ openssl x509 -req -days 365 -sha256 -in server.csr -CA ca.pem -CAkey ca-key.pem \
  -CAcreateserial -out server-cert.pem -extfile extfile.cnf
Signature ok
subject=/CN=your.host.com
Getting CA Private Key
Enter pass phrase for ca-key.pem:
```

客户端认证需要客户端key及认证签名请求：

``` bash
$ openssl genrsa -out key.pem 4096
Generating RSA private key, 4096 bit long modulus
.........................................................++
................++
e is 65537 (0x10001)
$ openssl req -subj '/CN=client' -new -key key.pem -out client.csr
```

为了使key适配客户端认证，需要创建额外的配置文件：

``` bash
$ echo extendedKeyUsage = clientAuth > extfile.cnf
```

对公有key进行签名：

``` bash
$ openssl x509 -req -days 365 -sha256 -in client.csr -CA ca.pem -CAkey ca-key.pem \
  -CAcreateserial -out cert.pem -extfile extfile.cnf
Signature ok
subject=/CN=client
Getting CA Private Key
Enter pass phrase for ca-key.pem:
```

生成`cert.pem`及`server-sert.pem`后，可以安全的将两个认证签名文件进行删除：

``` bash
$ rm -v client.csr server.csr
```

在`umask`默认值022的作用下，秘钥文件可以被你和你的用户组全局读写。

为了避免意外产生，需要移除秘钥文件的写权限。下面这条命令会将权限更改为当前用户可读：

``` bash
$ chmod -v 0400 ca-key.pem key.pem server-key.pem
```

如果想使证书全局可读，仅仅移除证书的写权限来防止意外：

``` bash
$ chmod -v 0444 ca.pem server-cert.pem cert.pem
```

现在就可以设置Docker daemon，使其仅能通过客户端提供受认证的CA证书进行连接：

``` bash
$ dockerd --tlsverify --tlscacert=ca.pem --tlscert=server-cert.pem --tlskey=server-key.pem \
  -H=0.0.0.0:2376
```

现在你需要在执行docker命令时提供你的客户端key、证书以及瘦信任的CA：

> **提示**：使用Docker daemon的域名替换下述样例中的`$HOST`

``` bash
$ docker --tlsverify --tlscacert=ca.pem --tlscert=cert.pem --tlskey=key.pem \
  -H=$HOST:2376 version
```

> **提示**：在TLS上的Docker服务应该使用TPC 2376端口。

> **警告**：上述展示的样例在使用证书认证方式时，不需要`sudo`或者`docker`用户组就可以执行。这意味着任何持有keys的人都具备操作正在运行的Docker daemon的权限，请和root密码一样保护好这些证书！

## 将安全连接设为默认设置

如果想将安全连接设为默认设置，可以通过将上述文件移动到用户文件夹的`.docker`文件夹下，然后设置`DOCKER_HOST`和`DOCKER_TLS_VERIFY`参数替代每次执行命令时传递的`-H=tcp://$HOST:2376`和`--tlsverify`。

``` bash
$ mkdir -pv ~/.docker
$ cp -v {ca,cert,key}.pem ~/.docker
$ export DOCKER_HOST=tcp://$HOST:2376 DOCKER_TLS_VERIFY=1
```

Docker命令就可以通过默认的安全设置进行连接：

``` bash
$ docker ps
```

## 其他安全模式

如果不想使用完整的双工授权，可以通过混合下述标识实现其他的模式。

### 守护进程安全模式

* `tlsverify`，`tlscacert`，`tlscert`，`tlskey`用来设置认证的客户端
* `tls`，`tlscert`，`tlskey`：不对客户端进行认证

### 客户端安全模式

* `tls`:基于公有/默认的CA池认证服务端
* `tlsverify`，`tlscacert`：使用经过CA证书认证的服务端进行认证
* `tls`，`tlscert`，`tlskey`：使用客户端证书替代受CA证书认证的服务端进行认证
* `tls`，`tlscacert`，`tlscert`，`tlskey`：同时使用客户端证书和受CA证书认证的服务端证书进行认证

如果识别到上述标识，客户端就会使用客户端证书进行连接，因此需要将秘钥文件放到`~/.docker/{ca,cert,key}.pem`。如果想将秘钥文件保存在其他路径，可以通过`DOCKER_CERT_PATH`环境变量指定路径。

``` bash
$ export DOCKER_CERT_PATH=~/.docker/zone1/
$ docker --tlsverify ps
```

## 使用`curl`请求安全的Docker端口

使用`curl`测试安全的API请求时，需要附加二外的三个标识：

``` bash
$ curl https://$HOST:2376/images/json \
  --cert ~/.docker/cert.pem \
  --key ~/.docker/key.pem \
  --cacert ~/.docker/ca.pem
```