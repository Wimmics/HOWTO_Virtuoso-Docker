## Obtain a SSL sertificates 

2 certificates

## Run or Update your docker image 

Make sure to have ``` -p 4443:4443 ``` option in your commande to activate HTTPS virtuoso.

 ```
 sudo docker run --name myvirtuoso -p 8890:8890 -it \
  -p 1111:1111 \
  -p 4443:4443 \
  -e DBA_PASSWORD=dba \
  -e SPARQL_UPDATE=true \
  -v ~/../virtuoso-openlink2:/database \
  -d  openlink/virtuoso-opensource-7:latest ```


## Update the virtuoso.ini file

Copy the certificate files to the volume shared with Virtuoso Docker container and update the  [HTTPServer] section section in your```virtuoso.ini```.

```
SSLCertificate              = /database/20220829_XXX_cert.cer
SSLPrivateKey               = /database/XXXX.key
X509ClientVerifyCAFile      = /database/XXXXX_CA.cer
```

Also update/check the [URIQA] section:

```
[URIQA]
DynamicLocal = 1
DefaultHost  = localhost:8890
```
Restart the Virtuoso image, and look at the log file. Once HTTPS is up, you should see something like:

```
HTTPS Using X509 Client CA ....
HTTPS/X509 server online at 4443
```

## Open SSL port 443

Ask a system administrator with permissions to do it.


## Configure Virtuoso via Conductor

Copy HTTP configurations for HTTPS. 

## Update Apache web server configuration

virtuoso.conf

## Create redirection rules for DESCRIBE



