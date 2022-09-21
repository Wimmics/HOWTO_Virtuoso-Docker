## Obtain a SSL sertificates 

2 certificates

## Open SSL port 443

Ask a system administrator with permissions to do it.

## Update virtuoso.ini

Copy the certificate files to the volume shared with Virtuoso Docker container and update the virtuoso.ini.

```
SSLCertificate              = /database/20220829_XXX_cert.cer
SSLPrivateKey               = /database/XXXX.key
X509ClientVerifyCAFile      = /database/XXXXX_CA.cer
```

```
[URIQA]
DynamicLocal = 1
DefaultHost  = localhost:8890
```
restart the Virtuoso image, and look at the log file. Once HTTPS is up, you should see something like:

```
HTTPS Using X509 Client CA ....
HTTPS/X509 server online at 4443
```


## Configure Virtuoso via Conductor

Copy HTTP configurations for HTTPS. 

## Update Apache web server configuration

virtuoso.conf

## Create redirection rules for DESCRIBE



