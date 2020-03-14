# myca
v1.0

## Overview

Mini certificate authority.

Scripts and config for setting up and running a two layer CA hierarchy with an offline root and online issuing CA.

As of this version, the only kind of end entity certificate issued is a server certificate, enabled for TLS client and server authentication.

You can be up and running in 3 steps:

```
$ git clone https://github.com/christian-brindley/myca
$ cd myca
$ bin/mkpki "Test Platform"
Creating CA databases
Creating root CA
Creating issuing CA
```

Now you can create server keys and certificates - e.g.

```
$ bin/mkcert www.example.com
Generating key pair
Issuing certificate
$ openssl x509 -in ee/www.example.com/cert.pem -issuer -subject -noout
issuer= /CN=Test Platform Issuing CA
subject= /CN=www.example.com
$ keytool -list -keystore ee/www.example.com/keystore -storepass Passw0rd
Keystore type: jks
Keystore provider: SUN

Your keystore contains 1 entry

server-cert, 14-Mar-2020, PrivateKeyEntry, 
Certificate fingerprint (SHA1): B5:8E:B0:8B:1F:FF:0D:9D:35:00:9A:20:89:68:38:52:8F:34:D3:A1
```

## Requirements

Requires `bash` and `openssl`

## Installation

Clone this repo into a new directory

```
git clone https://github.com/christian-brindley/myca
cd myca
```

## Setup

Run the `mkpki` script to create the new root and issuing certificate authorities. Specify the prefix for the common name: this will be appended with ` Root CA` and ` Issuing CA` for the commonName fields of the root and issuing CA respectively. E.g.

```
bin/mkpki "Test Platform"
```

This creates the root CA certificate in the directory `root/cacerts` as follows

- `cert.pem` the root CA certificate)
- `truststore` a PKCS12 file containing the root CA certificate
- `truststore.pin` the password for truststore

The issuing CA certificate is written to `issuing/cacerts/cert.pem`.

## Issuing certificates

Run the `mkcert` script to issue a new end entity certificate, specifying the value to use for the commonName field. This version creates a server certificate, with extended key usage for client and server authentication. E.g.

```
bin/mkcert "www.example.com"
```

Once the certificate is issued, the following files are created in `ee/{commonName}`

- `cert.pem` the server certificate
- `chain.pem` the server certificate and issuing CA
- `csr.pem` the server certificate request
- `key.pem` the server private key
- `keystore` a PKCS12 keystore with the server certificate chain and private key, with alias `server-cert` (suitable for use by ForgeRock DS as an LDAPS server certificate)
- `keystore.pin` the password for `keystore`
- `ads-truststore` a PKCS12 keystore with the server certificate chain and private key, with alias `ads-certificate` (for use by [ForgeRock DS replication](https://backstage.forgerock.com/knowledge/kb/article/a33131480))
- `ads-truststore.pin` the password for `ads-truststore`
- `ads-truststore.ldif` an LDAP modify input script for [replacing the certificate used for ForgeRock DS replication](https://backstage.forgerock.com/knowledge/kb/article/a20516091)

## Re-intialising

To wipe out all CA and end-entity keys and certificates, run the `rmpki` script as follows

```
bin/rmpki -f
```
**Caution**: this is irreversible. 

