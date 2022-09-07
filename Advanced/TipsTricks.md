# Tips and tricks for Virtuoso

Below are several observations that are not documented about the behavior of Virtuoso.

## In Virtuoso, triples are actually quads

When querying a virtuoso server, it should be considered that Virtuoso stores triples `<s , p, o>` as quads `<s ,p ,o ,g>`, with the fourth element indicating the named graph of the triple.
This choice is why there is no nameless default graph in a virtuoso server, it is necessary to choose a named graph as the default graph. By default, querying a virtuoso server without specifying a default graph will query the union of all the named graphs in the server.

Sending a query to a server that contains two graphs with some shared content such as:
```
SELECT * {
?s ?p ?o
}
```
will return results containing duplicate triples. Graphs are supposed to be sets of triples, as such, querying the graph resulting from the union of two graphs should not have duplicated values. Because Virtuoso considers triples as quads in its inner workings, it considers identical triples from different graphs as different quads and duplicates them in the results.
When possible, the `DISTINCT` keyword should be used to remove duplicate values in the results of a query.

## The "ResultSetMaxRows" parameter applies to inner queries

The configuration parameter "`ResultSetMaxRows`" of the virtuoso server limits the number of results to a query.
It is important to note that this parameter also applies to inner queries.

For example, in a server with 2000 triples and a "`ResultSetMaxRows`" set to 1000, the following query should count the number of triples:
```
SELECT (COUNT(*) AS ?count) {
	SELECT DISTINCT ?s ?p ?o {
		?s ?p ?o
	}
}
```
Because of the limit set by "`ResultSetMaxRows`", the query will return 1000 instead of 2000.
