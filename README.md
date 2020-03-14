# myca

Mini certificate authority.

Scripts and config for setting up and running a two layer CA hierarchy with an offline root and online issuing CA.

## Requirements

Requires bash and openssl

## Installation

Clone this repo into a new directory

```
git clone https://github.com/christian-brindley/myca
cd myca
```

## Setup

Run the `mkpki` script to create the new root and issuing certificate authorities. Specify the prefix for the common name: this will be appended with " Root CA" and " Issuing CA" for the commonName fields of the root and issuing CA respectively. E.g.

```
bin/mkpki "Test Platform"
```

Once the script has run, the root CA certificate is created in the directory root/cacerts as follows

- cert.pem
- truststore
- truststore.pin

## Issuing certificates

Run the `mkcert` script to issue a new end entity certificate, specifying the value to use for the commonName field. This version creates a server certificate, with extended key usage for client and server authentication. E.g.

```
bin/mkcert "www.example.com"
```

Once the certificate is issued, the following files are created in ee/{commonName}

- ads-truststore
- ads-truststore.ldif
- ads-truststore.pin
- cert.pem
- chain.pem
- csr.pem
- key.pem
- keystore
- keystore.pin
