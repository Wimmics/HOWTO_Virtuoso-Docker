# Running commands
Running commands on a docker is done with the `docker exec` command line. To change the running environment of the docker, please refer to the [docker documentation](https://docs.docker.com/engine/reference/commandline/exec/).

To change the behavior of the virtuoso server using the command line, use the `isql` command inside the docker.
isql needs to connect to the server as an admin.

As an example, the following line displays the status of the virtuoso server:
```
sudo docker exec -it virtuoso \
    isql -H localhost -S 1111 -U dba -P $VIRTUOSO-DBA-PWD exec="status();"
```

## Some useful commands

Run a SPARQL query in the command line:
```
sudo docker exec -it virtuoso \
    isql -H localhost -S 1111 -U dba -P $VIRTUOSO-DBA-PWD exec="SPARQL SELECT * { ?s ?p ?o };"
```

Run isql instructions in a `command.isql` file present in the shared folder `/database`:
```
docker exec -it virtuoso \
    isql localhost:1111 dba dekalog /database/command.isql
```
or
```
docker exec -it virtuoso \
    isql localhost:1111 dba dekalog exec="LOAD database/command.isql"
```
