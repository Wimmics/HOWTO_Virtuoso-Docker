# Loading data on the server

## Loading data via the Conductor web interface

Go the [http://localhost:8890/conductor](http://localhost:8890/conductor) URL

## Loading data via the Docker exec command
The [docker exec](https://docs.docker.com/engine/reference/commandline/exec/) command in combination with [Interactice SQL Utility (ISQL)](https://docs.openlinksw.com/virtuoso/invokingisql/)  can be used to connect to the running Virtuoso instance and perform data load and update.

The RDF data files have to be placed in the `import` folder that was created during the [installation](Installation.md). The easiest way to upload this file is to create an *isql* script file in the same `import` folder.

For example, to load *test.ttl* file to the *ht<span>tp://</span>data.namespace.fr/graph/test* graph, the minimal *import-rdf.isql* script should look like this:

```
log_enable(3,1);

SPARQL CLEAR GRAPH  <http://data.namespace.fr/graph/test>;

ld_dir ('', 'test.ttl', 'http://data.namespace.fr/graph/test');
rdf_loader_run ();

checkpoint;
```
And the command running it should be like this:
```
docker exec virtuoso \
       isql -H localhost -U dba -P $VIRTUOSO-DBA-PWD exec="LOAD import/import-rdf.isql"

```
Note that the second parameter of *ld_dir* command can be a file mask to load multiple files into the same graph (see command documentation [here](https://docs.openlinksw.com/virtuoso/fn_ld_dir/) . Also multiple *ld_dir* commands can be in the same script before *rdf_loader_run* call.

For an incremental update to a graph remove `SPARQL CLEAR GRAPH...` line.  


There also exists a mechanism to pass arguments to an *isql* script.

For example to make a previous example more generic where the rdf file and a graph can be passed as arguments the *import-rdf.isql* script would look like this:
```
log_enable(3,1);

SPARQL CLEAR GRAPH  <$ARGV[$+ $I 1]>;

ld_dir ('', '$ARGV[$I]', '$ARGV[$+ $I 1]');
rdf_loader_run ();
checkpoint;
```
And the command running it would look like this:
```
docker exec virtuoso \
       isql -H localhost -U dba -P $VIRTUOSO-DBA-PWD exec="LOAD import/import-rdf.isql" \
            -i test.ttl http://data.namespace.fr/graph/test

```
