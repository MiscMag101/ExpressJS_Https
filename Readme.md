
# How to test it

## Download prototype

```console
$ git clone https://github.com/MiscMag101/ExpressJS_Https.git
```

* Install NPM Packages

```console
$ npm install
```

## Create a self-signed certificat

```console
$ mkdir tls
$ openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls/key.pem -out tls/cert.pem
```

For this certificat, a hostname will be required (such as app.example.com).
/!\ This self-signed certificat should be used only for testing purpose.

## Start Application

```console
$ npm start
```

## Test

Open [https://app.example.com:3000](https://app.example.com:3000) And bypass security warning... (only for testing purpose).


## Audit with CipherScan

Github link to download CipherScan: [https://github.com/mozilla/cipherscan](https://github.com/mozilla/cipherscan)

```console
./cipherscan app.example.com:3000
```

```
..........
Target: app.example.com:3000

prio  ciphersuite                  protocols  pfs                 curves
1     ECDHE-RSA-AES256-GCM-SHA384  TLSv1.2    ECDH,P-256,256bits  prime256v1
2     ECDHE-RSA-AES128-GCM-SHA256  TLSv1.2    ECDH,P-256,256bits  prime256v1
3     ECDHE-RSA-AES256-SHA384      TLSv1.2    ECDH,P-256,256bits  prime256v1
4     ECDHE-RSA-AES128-SHA256      TLSv1.2    ECDH,P-256,256bits  prime256v1

Certificate: untrusted, 4096 bits, sha256WithRSAEncryption signature
TLS ticket lifetime hint: 7200
NPN protocols: http/1.1,http/1.0
OCSP stapling: not supported
Cipher ordering: server
Curves ordering: server - fallback: no
Server supports secure renegotiation
Server supported compression methods: NONE
TLS Tolerance: yes

Intolerance to:
 SSL 3.254           : absent
 TLS 1.0             : PRESENT
 TLS 1.1             : PRESENT
 TLS 1.2             : absent
 TLS 1.3             : absent
 TLS 1.4             : absent
```


# How I did it

## Create ExpressJS App 

This prototype is created with Express Generator

```console
# npm install -g express-generator
$ express -v pug
$ npm install
```

## Edit startup script

file: "bin/wwww"

```
/**
 * Module dependencies.
 */

...
var fs = require('fs');
var https = require('https');
...


/**
 * Create HTTP(s) server.
 */

// HTTPS setup
var options = {

  // Private key
  key: fs.readFileSync('./tls/key.pem'),

  // Certificate
  cert: fs.readFileSync('./tls/cert.pem'),

  // Protocol : only TLS 1.2
  secureProtocol: 'TLSv1_2_method',

  // Cipher Suites
ciphers:"ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA256",

};

// Create HTTPS server
var server = https.createServer(options, app);

```
