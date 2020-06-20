# Appendix C - OpenSSL Client Certificates for Testing
# 附录 C - 测试的 OpenSSL 客户端认证证书

DISCLAIMER

声明

This page is provided for **testing purposes** only and the certificates are for **testing purposes only**.

提供此页仅供于测试目的和证书也仅用于测试目的

The following tutorial provides some basic steps for creating **test** x.509 certificates.

以下教程提供了创建测试 x.509 证书的一些基本步骤。

- Do not use these certificates for production. Instead, follow your security policies.
- 请勿将这些证书用于生产。 相反，请遵循您的安全策略。
- For information on OpenSSL, refer to the official OpenSSL docs. Although this tutorial uses OpenSSL, the material should not be taken as an authoritative reference on OpenSSL.
- 有关 OpenSSL 的信息，请参考官方的 OpenSSL 文档。 尽管本教程使用的是 OpenSSL ，但不应将其视为 OpenSSL 的权威参考。

## Prerequisite 

## 前提条件

The procedure outlined on this page uses the **test** intermediate authority certificate and key `mongodb-test-ia.crt` and `mongodb-test-ia.key` created in [Appendix A - OpenSSL CA Certificate for Testing](https://docs.mongodb.com/manual/appendix/security/appendixA-openssl-ca/).

此页面上概述的过程使用测试中间权限证书以及在[附录A-用于测试的 OpenSSL CA 证书](https://docs.mongodb.com/manual/appendix/security/appendixA-openssl-ca/)中创建的密钥 mongodb-test-ia.crt 和 mongodb-test-ia.key。

## Procedure
## 过程
The following procedure outlines the steps to create **test** certificates for MongoDB clients. For steps to create **test** certificates for MongoDB servers, see [Appendix B - OpenSSL Server Certificates for Testing](https://docs.mongodb.com/manual/appendix/security/appendixB-openssl-server/).

以下过程概述了为 MongoDB 客户端创建测试证书的步骤。 有关为 MongoDB 服务器创建测试证书的步骤，请参阅[附录B-用于测试的OpenSSL服务器证书](https://docs.mongodb.com/manual/appendix/security/appendixB-openssl-server/)

### A. Create the OpenSSL Configuration File
### A. 创建 OpenSSL 配置文件

1. Create a **test** configuration file `openssl-test-client.cnf` for your client with the following content: 
1. 用下面的内容为你的客户端创建一个测试配置文件 `openssl-test-client.cnf`  

   copy

   ```
   # NOT FOR PRODUCTION USE. OpenSSL configuration file for testing.
   
   [ req ]
   default_bits = 4096
   default_keyfile = myTestClientCertificateKey.pem    ## The default private key file name.
   default_md = sha256
   distinguished_name = req_dn
   req_extensions = v3_req
   
   
   [ v3_req ]
   subjectKeyIdentifier  = hash
   basicConstraints = CA:FALSE
   keyUsage = critical, digitalSignature, keyEncipherment
   nsComment = "OpenSSL Generated Certificate for TESTING only.  NOT FOR PRODUCTION USE."
   extendedKeyUsage  = serverAuth, clientAuth
   
   
   [ req_dn ]
   countryName = Country Name (2 letter code)
   countryName_default =
   countryName_min = 2
   countryName_max = 2
   
   stateOrProvinceName = State or Province Name (full name)
   stateOrProvinceName_default = TestClientCertificateState
   stateOrProvinceName_max = 64
   
   localityName = Locality Name (eg, city)
   localityName_default = TestClientCertificateLocality
   localityName_max = 64
   
   organizationName = Organization Name (eg, company)
   organizationName_default = TestClientCertificateOrg
   organizationName_max = 64
   
   organizationalUnitName = Organizational Unit Name (eg, section)
   organizationalUnitName_default = TestClientCertificateOrgUnit
   organizationalUnitName_max = 64
   commonName = Common Name (eg, YOUR name)
   commonName_max = 64
   ```


2. *Optional*. You can update the default Distinguished Name (DN) values. Ensure that client certificates differ from server certificates with regards to at least one of the following attributes: Organization (`O`), the Organizational Unit (`OU`) or the Domain Component (`DC`).
2. *可选的*。您可以更新默认专有名称(DN)值。确保客户端证书与服务器证书在以下至少一项属性上有所不同：组织(O)，组织单位(OU)或域组件(DC)

### B. Generate the Test PEM File for Client

### B. 为客户端创建测试的 PEM 文件

1. Create the **test** key file `mongodb-test-client.key`.

1. 创建测试密钥文件 `mongodb-test-client.key`。
   copy

   ```
   openssl genrsa -out mongodb-test-client.key 4096
   ```

3. Create the **test** certificate signing request `mongodb-test-client.csr`. When asked for Distinguished Name values, enter the appropriate values for your **test** certificate:
  创建测试的认证签名文件 `mongodb-test-client.csr`。当要求提供专有名称值时，为你的测试证书输入合适的值。

  IMPORTANT

  重要

  The client certificate subject must differ to a server certificate subject with regards to at least one of the following attributes: Organization (O), the Organizational Unit (OU) or the Domain Component (DC).

  客户端证书主题必须与服务器证书主题在以下属性中至少有一项要不同：组织 (O)，组织单位 (OU) 或域组件 (DC)

  copy

  ```
  openssl req -new -key mongodb-test-client.key -out mongodb-test-client.csr -config openssl-test-client.cnf
  ```

4. Create the **test** client certificate `mongodb-test-client.crt`.

   创建测试客户端证书 `mongodb-test-client.crt`。

   copy

   ```
   openssl x509 -sha256 -req -days 365 -in mongodb-test-client.csr -CA mongodb-test-ia.crt -CAkey mongodb-test-ia.key -CAcreateserial -out mongodb-test-client.crt -extfile openssl-test-client.cnf -extensions v3_req
   ```

5. Create the **test** PEM file for the client.

   为客户端创建测试的 PEM 文件

   copy

   ```
   cat mongodb-test-client.crt mongodb-test-client.key > test-client.pem
   ```

   You can use the **test** PEM file to configure the [`mongo`](https://docs.mongodb.com/manual/reference/program/mongo/#bin.mongo) shell for TLS/SSL **testing**. For example, to connect to a [`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) or a [`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos):

   你可以使用测试的 PEM 文件为 TLS/SSL 测试配置 mongo shell。例如，连接一个[`mongod`](https://docs.mongodb.com/manual/reference/program/mongod/#bin.mongod) 或者 [`mongos`](https://docs.mongodb.com/manual/reference/program/mongos/#bin.mongos)

   **For MongoDB 4.2 or greater**, include the following options for the client:

   **对于 MongoDB 4.2 或者更高版本**，对于客户端包含以下选项：

   copy

   ```
   mongo --tls --host <serverHost> --tlsCertificateKeyFile test-client.pem  --tlsCAFile test-ca.pem
   ```

   **For MongoDB 4.0 and earlier**, include the following options for the client:

   **对于 MongoDB 4.0 或者更早版本**，对于客户端包含以下选项：

   copy

   ```
   mongo --ssl --host <serverHost> --sslPEMKeyFile test-client.pem  --sslCAFile test-ca.pem
   ```

###  On macOS

### 在 macOS 系统中

   If you are **testing** with Keychain Access to manage certificates, create a PKCS 12 file to add to Keychain Access instead of a PEM file:
  
   如果你使用  Keychain Access 管理证书，创建一个 pkcs-12 而不是 PEM 文件添加到 Keychain Access 中。

   copy

   ```
   openssl pkcs12 -export -out test-client.pfx -inkey mongodb-test-client.key -in mongodb-test-client.crt -certfile mongodb-test-ia.crt
   ```

   Once added to Keychain Access, instead of specifying the Certificate Key file, you can use the [`--tlsCertificateSelector`](https://docs.mongodb.com/manual/reference/program/mongo/#cmdoption-mongo-tlscertificateselector) to specify the certificate to use. If the CA file is also in Keychain Access, you can omit `--tlsCAFile` as well as in the following example:

   将其添加到 Keychain Access 后，你无需指定证书密钥文件，就可以使用[`--tlsCertificateSelector`](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption-mongod-tlscertificateselector)来指定要使用的证书。如果 CA 文件也在 Keychain Access 中，你也可省略 --tlsCAFile。

   **For MongoDB 4.2 or greater** 
   **对于 MongoDB 4.2 或者更高版本**

   copy 

   ```
   mongo --tls --tlsCertificateSelector subject="<TestClientCertificateCommonName>"
   ```

   Although still available, [`--ssl`](https://docs.mongodb.com/manual/reference/program/mongo/#cmdoption-mongo-ssl) and [`--sslCertificateSelector`](https://docs.mongodb.com/manual/reference/program/mongo/#cmdoption-mongo-sslcertificateselector) are [deprecated as of MongoDB 4.2](https://docs.mongodb.com/manual/release-notes/4.2/#tls).

   虽然任然有效， [MongoDB 4.2 已不推荐 ](https://docs.mongodb.com/manual/release-notes/4.2/#tls) [`--sslMode`](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption-mongod-sslmode) 和 [`--sslCertificateSelector`](https://docs.mongodb.com/manual/reference/program/mongod/#cmdoption-mongod-sslcertificateselector) 。

   **For MongoDB 4.0 and earlier**
   **对于 MongoDB 4.0 和 更早版本**

   copy 

   ```
   mongo --ssl --sslCertificateSelector subject="<TestClientCertificateCommonName>"
   ```

   For adding certificates to Keychain Access, refer to your official documentation for Keychain Access.

   要向 Keychain Access 添加证书，请参阅 Keychain Access 的官方文档。

   ```
   mongod --tlsMode requireTLS --tlsCertificateSelector subject="<TestServerCertificateCommonName>"
   ```

SEE ALSO 

- [Appendix A - OpenSSL CA Certificate for Testing](https://docs.mongodb.com/manual/appendix/security/appendixA-openssl-ca/#appendix-ca-certificate)
- [Appendix B - OpenSSL Server Certificates for Testing](https://docs.mongodb.com/manual/appendix/security/appendixB-openssl-server/#appendix-server-certificate)
- [Client x.509 Certificate](https://docs.mongodb.com/manual/tutorial/configure-x509-client-authentication/#x509-client-authentication)

也可以看看

- [附录A-用于测试的OpenSSL CA证书](https://docs.mongodb.com/manual/appendix/security/appendixA-openssl-ca/#appendix-ca-certificate)
- [附录C-用于测试的OpenSSL客户端证书](https://docs.mongodb.com/manual/appendix/security/appendixC-openssl-client/#appendix-client-certificate)
- [会员x.509证书](https://docs.mongodb.com/manual/tutorial/configure-x509-member-authentication/#x509-member-certificate)

