#### openSSL

```shell
OpenSSL> genrsa -out alipay_test_private_key.pem    
Generating RSA private key, 2048 bit long modulus (2 primes)
.....................................+++++
.........................................+++++
e is 65537 (0x010001)
OpenSSL> pkcs8 -topk8 -inform PEM -in alipay_test_private_key.pem -outform PEM -nocrypt -out alipay_test_private_key_pkcs8.pem
OpenSSL> rsa -in alipay_test_private_key.pem -pubout -out alipay_test_public_key.pem
writing RSA key
OpenSSL> exit

```

