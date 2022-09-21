## Obtain a SSL sertificates 

2 certificates

## Run or Update your docker image 

If your docker image is already running, you should delete it and run it again.  
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

```
<VirtualHost *:443>
     ServerName erebe-vm16.i3s.unice.fr
     ServerAlias weakg.i3s.unice.fr

    SSLEngine on
    SSLCertificateFile      /etc/apache2/certificate/20220829_XXX_cert.cer
    SSLCertificateKeyFile   /etc/apache2/certificate/XXXX.key
    SSLCertificateChainFile /etc/apache2/certificate/XXXXX_CA.cer
    
    SSLProxyEngine on
  

    RewriteEngine on

     #--- No access to the home page of Virtuoso, redirect to the sparql
     #interface
     #ProxyPassMatch ^/$ !
     #RewriteRule "^/$" "http://%{SERVER_NAME}/sparql" [R=303,L]
     Header set Access-Control-Allow-Origin "*"


     #--- Proxy anything to Virtuoso port 8890
     ProxyPass /sparql https://localhost:4443/sparql
     ProxyPassReverse /sparql https://localhost:4443/sparql

    ProxyPass /describe/ https://localhost:4443/describe/
    ProxyPassReverse /describe/ https://localhost:4443/describe/

    ProxyPass /statics/ https://localhost:4443/statics/
    ProxyPassReverse /statics/ https://localhost:4443/statics/

    ProxyPass /fct/ https://localhost:4443/fct/
    ProxyPassReverse /fct/ https://localhost:4443/fct/

    ProxyPass /services/ https://localhost:4443/services/
    ProxyPassReverse /services/ https://localhost:4443/services/

     #--- Forbid access to Conductor, only allowed through direct access
     #to Virtuoso port 8890 or 4443 which is not open on internet
     <Location /conductor>
       Order deny,allow
       Deny from all
     </Location>
</VirtualHost>
```

Restart your apache server and try it !! 

You may get the following error 

```
Proxy Error
The proxy server could not handle the request GET /web_app.
Reason: Error during SSL Handshake with remote server
```

So you just need to add to your configuration file the following directives:

``` 
SSLProxyVerify none
SSLProxyCheckPeerCN off
SSLProxyCheckPeerName off
SSLProxyCheckPeerExpire off
```
## Create redirection rules for DESCRIBE



