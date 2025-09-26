# Loading data on the server

## Manually loading data via the web interface

If your docker is hosted on a server, you may need to create an SSH tunnel `ssh -L 8890:localhost:8890` to access the web interface.

- Go the [http://localhost:8890/conductor](http://localhost:8890/conductor) URL.
- In the web interface,connect as an admin (user dba) with the password you set when starting the container (variable `$VIRTUOSO_DBA_PWD`).
- Go to the "Linked Data" tab and use the form to upload files to graph.
- Alternatively, in the "Database" tab, use the "Interactive SQL" sub-tab to send ISQL commands to the server.


## Loading data via the Docker exec command

The [docker exec](https://docs.docker.com/engine/reference/commandline/exec/) command in combination with [Interactice SQL Utility (ISQL)](https://docs.openlinksw.com/virtuoso/invokingisql/)  can be used to connect to the running Virtuoso instance and upload/update data.

The RDF data files must be located in the `import` folder created during the [installation](Installation.md). The easiest way to upload this file is to create an *isql* script file in the same `import` folder.


For example, to load *test.ttl* file to the *ht<span>tp://</span>data.namespace.fr/graph/test* named graph, the *import.isql* script should look like this:

```
log_enable(3,1);

SPARQL CLEAR GRAPH  <http://data.namespace.fr/graph/test>;

ld_dir ('import', 'test.ttl', 'http://data.namespace.fr/graph/test');
rdf_loader_run ();
SELECT * FROM DB.DBA.load_list;
checkpoint;

SPARQL SELECT DISTINCT ?g (COUNT(*) as ?triples) WHERE { GRAPH ?g {?s ?p ?o} };
```

The 2nd parameter of *ld_dir* command can take wildcard `%` to load multiple files (e.g. `%.ttl`, see [documentation](https://docs.openlinksw.com/virtuoso/fn_ld_dir/). 
Multiple *ld_dir* commands can be added before calling *rdf_loader_run*.


Then, run the command running:
```bash
sudo docker exec virtuoso \
       isql -H localhost -U dba -P $VIRTUOSO_DBA_PWD exec="LOAD import/import.isql"
```

For an incremental update to a named graph, simply comment out the `SPARQL CLEAR GRAPH...` line. use `--` to comment out a line.

### Passing arguments to the isql script

There exists a mechanism to pass arguments to an *isql* script.

For example to make a previous example more generic, the rdf file and named graph can be passed as arguments. The *import.isql* script would look like this:
```
log_enable(3,1);

SPARQL CLEAR GRAPH  <$ARGV[$+ $I 1]>;

ld_dir ('import', '$ARGV[$I]', '$ARGV[$+ $I 1]');
rdf_loader_run ();
checkpoint;
```
And the command running it would look like this:
```
sudo docker exec virtuoso \
       isql -H localhost -U dba -P $VIRTUOSO_DBA_PWD exec="LOAD import/import.isql" \
            -i test.ttl http://data.namespace.fr/graph/test
```
