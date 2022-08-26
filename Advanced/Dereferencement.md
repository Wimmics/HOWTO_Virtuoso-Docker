# Enabling dereferencing

To enable dereferencing of the URIs of your dataset, you need access to the server at the address of the URIs.

Using a file manager, you then can upload a `.htaccess` file as follows (replace [SERVER_NAME] and [NAMESPACE] by their respective values):

```
RewriteEngine On

# ---- Rule for prod-dekalog.inria.fr
RewriteCond "%{HTTP_ACCEPT}" "text/html" [nocase]
RewriteRule "^(.*)$" "[SERVER_NAME]?url=[NAMESPACE]$1" [R=303,L]

RewriteCond "%{HTTP_ACCEPT}" "text/turtle" [nocase]
RewriteRule "^#(.*)$" "[SERVER_NAME]/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<[NAMESPACE]$1>&output=text/turtle" [R=303,L]

RewriteCond "%{HTTP_ACCEPT}" "text/plain" [nocase]
RewriteRule "^#(.*)$" "[SERVER_NAME]/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<[NAMESPACE]$1>&output=text/plain" [R=303,L]

RewriteCond "%{HTTP_ACCEPT}" "application/json" [nocase]
RewriteRule "^#(.*)$" "[SERVER_NAME]/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<[NAMESPACE]$1>&output=application/json" [R=303,L]

RewriteCond "%{HTTP_ACCEPT}" "application/rdf\+json" [nocase]
RewriteRule "^#(.*)$" "[SERVER_NAME]/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<[NAMESPACE]$1>&output=application/rdf\%2bjson" [NE,R=303,L]

RewriteCond "%{HTTP_ACCEPT}" "application/ld\+json" [nocase]
RewriteRule "^#(.*)$" "[SERVER_NAME]/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<[NAMESPACE]$1>&output=application/ld\%2bjson" [NE,R=303,L]

RewriteCond "%{HTTP_ACCEPT}" "application/rdf\+xml" [nocase]
RewriteRule "^x#(.*)$" "[SERVER_NAME]/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<[NAMESPACE]$1>&output=application/rdf\%2bxml" [NE,R=303,L]
```

Note:
For namespaces in the `ns.inria.fr` domain, you need to use a DAVS client with your INRIA credentials.
For windows and mac, "Cyberduck" is recommended. 
