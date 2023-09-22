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

## Beware of the ordering of property path

For reasons unknown, the ordering of property paths in a basic graph pattern may change the number of results returned by a query.

For example, the following query sent to :
```sparql
PREFIX skos: <http://www.w3.org/2004/02/skos/core#>
PREFIX prov:   <http://www.w3.org/ns/prov#>
PREFIX oa:     <http://www.w3.org/ns/oa#>

SELECT discint ?document
FROM <http://data-issa.cirad.fr/graph/documents>
FROM <http://data-issa.cirad.fr/graph/thematic-descriptors>
FROM <http://data-issa.cirad.fr/graph/annif-descriptors>
FROM <http://agrovoc.fao.org/graph>
WHERE {
    ?document a prov:Entity.

    ?document ^oa:hasTarget [ oa:hasBody/skos:broader* <http://aims.fao.org/aos/agrovoc/c_1666> ].
    ?document ^oa:hasTarget [ oa:hasBody/skos:broader* <http://aims.fao.org/aos/agrovoc/c_29172> ].
    ?document ^oa:hasTarget [ oa:hasBody/skos:broader* <http://aims.fao.org/aos/agrovoc/c_34024> ].
}
```
returns 1 result.
The same query with:
```sparql
    ?document ^oa:hasTarget [ oa:hasBody/skos:broader* <http://aims.fao.org/aos/agrovoc/c_29172> ].
    ?document ^oa:hasTarget [ oa:hasBody/skos:broader* <http://aims.fao.org/aos/agrovoc/c_34024> ].
    ?document ^oa:hasTarget [ oa:hasBody/skos:broader* <http://aims.fao.org/aos/agrovoc/c_1666> ].
```
returns 7 results and with:
```sparql
    ?document ^oa:hasTarget [ oa:hasBody/skos:broader* <http://aims.fao.org/aos/agrovoc/c_34024> ].
    ?document ^oa:hasTarget [ oa:hasBody/skos:broader* <http://aims.fao.org/aos/agrovoc/c_29172> ].
    ?document ^oa:hasTarget [ oa:hasBody/skos:broader* <http://aims.fao.org/aos/agrovoc/c_1666> ].
```
it returns 6 results
