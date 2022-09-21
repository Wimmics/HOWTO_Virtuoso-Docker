## Obtain a SSL sertificates 

2 certificates

## Open SSL port 443

Ask a system administrator with permissions to do it.

## Update virtuoso.ini

Copy the certificate files to the volume shared with Virtuoso Docker container and update the virtuoso.ini.

SSLCertificate              = /database/20220829_XXX_cert.cer
SSLPrivateKey               = /database/XXXX.key
X509ClientVerifyCAFile      = /database/XXXXX_CA.cer

[URIQA]
DynamicLocal = 1
DefaultHost  = localhost:8890

## Configure Virtuoso via Conductor

Copy HTTP configirations for HTTPS. 

## Update Apache web server configuration

virtuoso.conf

## Create redirection rules for DESCRIBE



