### Web server configuration
Below is an example of a simple Apache configuration `.conf` file. In this configuration, the access to the admin interface Conductor from the outside is blocked and only most of the useful services are accessible.
```
<VirtualHost *:80>
    ServerName <SERVER_SUBDOMAIN>.inria.fr
    RewriteEngine on

    #--- No access to the home page of Virtuoso, redirect to the sparql interface
    #---  ProxyPassMatch ^/$ !
    #---  RewriteRule "^/$" "http://%{SERVER_NAME}/sparql" [R=303,L]

    Header set Access-Control-Allow-Origin *

    #--- Proxy anything to Virtuoso port 8890
    ProxyPass /sparql http://localhost:8890/sparql
    ProxyPassReverse /sparql http://localhost:8890/sparql
    ProxyPass /fct http://localhost:8890/fct
    ProxyPassReverse /fct http://localhost:8890/fct
    ProxyPass /describe http://localhost:8890/describe
    ProxyPassReverse /describe http://localhost:8890/describe
    ProxyPass /services/ http://localhost:8890/services/
    ProxyPassReverse /services/ http://localhost:8890/services/

    #--- Forbid access to Conductor, only allowed through direct access to Virtuoso port 8890 or 4443 which is not open on internet
    <Location /conductor>
      Order deny,allow
      Deny from all
    </Location>
</VirtualHost>
```

#### How to access to the conductor web interface ?
Create an SSH tunnel using the following command:
`ssh -L 8890:localhost:8890 <Server domain>`
The Conductor interface will be reachable at [http://localhost:8890/conductor](http://localhost:8890/conductor).
