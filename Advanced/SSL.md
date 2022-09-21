## Obtain a SSL sertificates 

2 certificates

## Run or Update your docker image 

If you have your docker image is already running, you should delete it and run it again. 
 ```
 sudo docker run --name myvirtuoso -p 8890:8890 -it \
  -p 1111:1111 \
  -p 4443:4443 \
  -e DBA_PASSWORD=dba \
  -e SPARQL_UPDATE=true \
  -v ~/../virtuoso-openlink2:/database \
  -d  openlink/virtuoso-opensource-7:latest
 ```
Note we have now the ``` -p 4443:4443 ``` option in your command to activate HTTPS virtuoso.

## Update the virtuoso.ini file

Copy the certificate files to the volume shared with Virtuoso Docker container and update the [HTTPServer] section in your```virtuoso.ini```.

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
13:37:26 HTTPS: Using X509 Client CA /C=GB/ST=Greater Manchester/L=Salford/O=Comodo CA Limited/CN=AAA Certificate Services
13:37:26 HTTPS: Using X509 Client CA /C=US/ST=New Jersey/L=Jersey City/O=The USERTRUST Network/CN=USERTrust RSA Certification Authority
13:37:26 HTTPS: Using X509 Client CA /C=NL/O=GEANT Vereniging/CN=GEANT OV RSA CA 4
13:37:26 HTTPS server online at 4443
13:37:26 Server online at 1111 (pid 1)
```

## Open SSL port 443

Ask a system administrator with permissions to do it.


## Configure Virtuoso via Conductor

Copy HTTP configurations for HTTPS. 

## Update Apache web server configuration

virtuoso.conf

## Create redirection rules for DESCRIBE



