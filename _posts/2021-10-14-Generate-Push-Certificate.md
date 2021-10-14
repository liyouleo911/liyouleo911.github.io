---
layout: post
title:  "Push证书生成与导出"
categories: iOS 
---

* 使用Mac OS X上的钥匙串工具制作 CSR文件 (CertificateSigningRequest.certSigningRequest)
钥匙串访问-> 证书助理 -> 从证书颁发机构请求证书 -> 填写邮箱 weather@91.com，常用名 91weather -> 存储到磁盘

* 上传CSR文件，生成Apple iOS Push证书 (在 https://developer.apple.com操作)
调试证书： aps_development.cer
生产证书： aps_production.cer
（以上证书可用于PushMeBaby工具进行推送测试）

# 生成提供给PHP服务器使用的p12文件 => (aps_development_php.p12、aps_production_php.p12)

1. 将cer证书导入Mac钥匙串，(先打开证书!!!),并右键导出钥匙标志（Private Key）的 p12文件
> aps_development_key.p12、 aps_production_key.p12 （密码为： apple）

2. 将cer证书转换成pem格式文件：（aps_development_cer.pem、aps_production_cer.pem）
```
openssl x509 -in aps_development.cer -inform DER -out aps_development_cer.pem -outform PEM
openssl x509 -in aps_production.cer -inform DER -out aps_production_cer.pem -outform PEM
```

3. 将Private Key的p12文件转换成pem格式文件:（aps_development_key.pem、aps_production_key.pem）
```
openssl pkcs12 -nocerts -in aps_development_key.p12 -out aps_development_key.pem
openssl pkcs12 -nocerts -in aps_production_key.p12 -out aps_production_key.pem
```

4. 将生成的 cer 和 key的pem文件合并成 p12文件 （php服务器使用！！！）
```
openssl pkcs12 -export -in aps_development_cer.pem -inkey aps_development_key.pem -certfile CertificateSigningRequest.certSigningRequest -name "aps_development" -out aps_development_php.p12
openssl pkcs12 -export -in aps_production_cer.pem -inkey aps_production_key.pem -certfile CertificateSigningRequest.certSigningRequest -name "aps_production" -out aps_production_php.p12
```
!!! masOS High Sierra 改成：
```
openssl pkcs12 -export -in aps_development_cer.pem -out aps_development_php.p12 -inkey aps_development_key.pem
openssl pkcs12 -export -in aps_production_cer.pem -out aps_production_php.p12 -inkey aps_production_key.pem
```

# 生成提供给Baidu Push使用的PEM文件 => (baidu_aps_development.pem、baidu_aps_production.pem)

1. 将cer证书导入Mac钥匙串，(不要打开证书!!!),直接右键导出（Push Services）证书为P12文件
> baidu_aps_development.p12、 baidu_aps_production.p12 （密码为： apple）

2. 将Push Services 的p12证书转换成 pem证书
```
openssl pkcs12 -in baidu_aps_development.p12 -out baidu_aps_development.pem -nodes
openssl pkcs12 -in baidu_aps_production.p12 -out baidu_aps_production.pem -nodes
```

# 总结：

* PushMeBaby使用的cert证书文件 (aps_development.cer、aps_production.cer)

* PHP服务器使用的是p12证书文件
> (cert->pem + key->pem) => p12 (aps_development_php.p12、aps_production_php.p12)

* Baidu Push使用的是PEM证书文件
> cert -> p12(Push Services) -> pem (baidu_aps_development.pem、baidu_aps_production.pem)