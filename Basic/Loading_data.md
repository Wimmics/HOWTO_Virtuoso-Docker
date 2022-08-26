### Loading data on the server

#### Loading data via the Conductor web interface

Go the [http://localhost:8890/conductor] URL

#### Updating virtuoso data via the Docker image
The isql-v command can be used from inside the virtuoso image using
`docker exec -it virtuoso isql --tty `.

The command to import data is as follow:
`docker exec -it virtuoso isql-v -H localhost -U dba -P pass exec="LOAD import/import-data.isql"`

#### Uploading virtuoso data outside virtuoso

Another option to consider can be scripting complex actions via bash files. For it, you need to install first the package *virtuoso-opensource*.

:exclamation: The virtuoso-opensource library also installs a virtuoso service that will lunch at every reboot a virtuoso app at port 1111. We generally use the same part in our Docker app. For avoiding collision of service a good practice could be to disable directly this one :
* Check your /etc/init.d directory and spot the name of the virtuoso service
* And simply disable it via : ```systemctl disable virtuoso-opensource-VERSION_NUMBER```

You are now able to interact with isql by using the following function  :
```
run_virtuoso_cmd () {
 VIRT_OUTPUT=`echo "$1" | "$bin" -H "$host" -S "$port" -U "$user" -P "$STORE_DBA_PASSWORD" 2>&1`
 VIRT_RETCODE=$?
 if [[ $VIRT_RETCODE -eq 0 ]]; then
   echo "$VIRT_OUTPUT" | tail -n+5 | perl -pe 's|^SQL> ||g'
   return 0
 else
   echo -e "[ERROR] running the these commands in virtuoso:\n$1\nerror code: $VIRT_RETCODE\noutput:"
   echo "$VIRT_OUTPUT"
   let 'ret = VIRT_RETCODE + 128'
   return $ret
 fi
}
```
* By declaring first the following parameters :
```
host=localhost  
port=1111 (default value)
user=dba
STORE_DBA_PASSWORD= *** (this is your own secret)
```

Exemple of use :
```
run_virtuoso_cmd "SPARQL SELECT ?s ?o ?p WHERE {?s ?o ?p} LIMIT 10;"
```
