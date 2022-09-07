# Metadata description

A dataset can be described using the [VoID](https://www.w3.org/TR/void/), [DCAT](https://www.w3.org/TR/vocab-dcat-2/) and [SPARQL-SD](https://www.w3.org/TR/sparql11-service-description/) vocabularies, with other more general vocabularies as complements.
* VoID is used to describe the dataset hosted by the server,
* SPARQL-SD is used to describe the endpoint of the server,
* DCAT is used to describe sets of datasets.

Other general recommended vocabularies include:
* [Schema](http://schema.org/), [DCTerms](http://purl.org/dc/terms/) and [SKOS](http://www.w3.org/2004/02/skos/core#) to write general descriptions.
* [FoaF](http://xmlns.com/foaf/0.1/) to write descriptions of people and organizations.
* [Prov-O](http://www.w3.org/ns/prov#) to write about the provenance of elements.

As a general rule, the metadata of the RDF server must at least answer the questions:
* Who?
    * Who is responsible for the server?
    * Who is contributing to the content of the server?
* What?
    * Is there a description of the content of the server?
    * What is the name of the server?
* When?
    * When was the server last updated?
    * When was the server first published?
* How?
    * What are the endpoints and/or dumps available to access the content of the server?
    * How was the content of the data generated?


## Template

The metadata description of a dataset can be created by following this template:
```
@prefix rdfs:       <http://www.w3.org/2000/01/rdf-schema#> .
@prefix owl:        <http://www.w3.org/2002/07/owl#> .
@prefix xsd:        <http://www.w3.org/2001/XMLSchema#> .
@prefix dcat:       <http://www.w3.org/ns/dcat#>.
@prefix foaf:       <http://xmlns.com/foaf/0.1/> .
@prefix prov:       <http://www.w3.org/ns/prov#> .
@prefix schema:		<http://schema.org/> .
@prefix void:       <http://rdfs.org/ns/void#> .
@prefix sd:     	<http://www.w3.org/ns/sparql-service-description#> .
@prefix dce:        <http://purl.org/dc/elements/1.1/>.
@prefix dct:        <http://purl.org/dc/terms/> .
@prefix skos:       <http://www.w3.org/2004/02/skos/core#> .
@prefix pav:        <http://purl.org/pav/> .
@prefix mod:        <https://w3id.org/mod#> .

<dataset-uri>
    a                       dcat:Dataset , void:Dataset , sd:Dataset ;
    dct:title               "Name of the dataset" ;
    dct:description         "Long description of the dataset" ;
    void:sparqlEndpoint     <Url of the SPARQL endpoint> ;
    dct:creator             <Uri of creator(s)> , "Avoid string literals when possible" ;
    dct:issued              "Date time of publication"^^xsd:dateTime ;
    dcat:version            "Version number" ;
    dct:license             <Uri/Url of license> ;
    dcat:keyword            "Keywords" , "describing" , "the" , "content" , "of" , "the" , "dataset" , "strings" , "or" , <URIs> ;
    void:vocabulary         <Uris of vocabularies used in the dataset> ;
	dct:language            "Lang" , "tags" , "used" , "in" , "the" , "literals" , "if" , "any" ;
    rdfs:seeAlso            <Uris of other web resources related to the dataset> ;
    pav:createdOn           "Date time of creation"^^xsd:dateTime ;
    dct:modified            "Date time of the last modification"^^xsd:date ;
    prov:hasPrimarySource   <Uri(s) of the source(s) of the data in the dataset> ;
    dct:conformsTo          <Uri(s) of the ontology(ies) that the dataset conforms to> ;
    mod:sampleQueries       "SPARQL query example returning interesting results in the dataset." ;
    void:exampleResource    <Uri(s) of a sample resource interesting to understand the content of the dataset> ;
    void:uriRegexPattern    "Namespace of the uris of the resources created in the dataset, if any, as a regex pattern" ;
    dcat:service            <endpoint-uri> ;
    dcat:distribution       [
            a                       dcat:Distribution ;
            dct:title               "This dataset's endpoint" ;
            dcat:accessURL          <Url of the SPARQL endpoint> ;
            dcat:mediaType          "application/sparql-query"
        ] ;
    dcat:distribution       [
            a                       dcat:Distribution ;
            dct:title               "This dataset's archive title" ;
            dcat:downloadUrl        <Url where the dataset can be downloaded as a file, if any> ;
            dct:format              "Format of the dump file" ;
            dcat:version            "Version number of the file" ;
            pav:createdOn           "Date time of creation of the file"^^xsd:dateTime
    ] ;
    sd:namedGraph           <Named graphs uris> .

<Named graphs uris>
    a                       sd:Graph, sd:NamedGraph ;
    sd:name                 <URI of the named graph> .

<endpoint-uri>
    a                       sd:Service, dcat:DataService ;
    dcat:servesDataset      <dataset-uri> ;
    sd:endpoint             <Url of the SPARQL endpoint> ;
    sd:availableGraphs      <dataset-uri> .
```
The template comes from [this file on GitHub](https://github.com/Wimmics/dekalog/blob/master/template-description.ttl).

## Tool

The WIMMICS team hosts a [website](https://wimmics.github.io/voidmatic/) to easily generate a basic description of a dataset without preliminary knowledge.

## Make the metadata description available

The description can be accessible in at least one of the three following manners :
* In the dataset itself
The description can be stored with the data in the server, possibly in its own graph. This is the advantage of facilitating the updates of the metadata.
* Reachable at `/sparql` if no parameters are passed
According to the SPARQL-SD recommendation, any SPARQL server must make a SPARQL-SD description available at the `/sparql`. To respect that, we use the dereferencing feature of Virtuoso to redirect the `/sparql` URL toward the description of the resource describing the service present in the dataset, when there are no parameters and the expected content is an RDF file. This is done by adding the following rules to the Apache configuration, after replacing `ENDPOINT-URI` with the right URI:
```
     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "text/turtle" [nocase]
     RewriteRule "^/sparql/?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<ENDPOINT-URI>&output=text/turtle" [R=303,L]

     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "text/plain" [nocase]
     RewriteRule "^/sparql/?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<ENDPOINT-URI>&output=text/plain" [R=303,L]

     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "application/json" [nocase]
     RewriteRule "^/sparql/?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<ENDPOINT-URI>&output=application/json" [R=303,L]

     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "application/rdf\+json" [nocase]
     RewriteRule "^/sparql/?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<ENDPOINT-URI>&output=application/rdf\%2bjson" [R=303,L]

     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "application/ld\+json" [nocase]
     RewriteRule "^/sparql/?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<ENDPOINT-URI>&output=application/ld\%2bjson" [R=303,L]

     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "application/rdf\+xml" [nocase]
     RewriteRule "^/sparql/?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<ENDPOINT-URI>&output=application/rdf\%2bxml" [R=303,L]
```
* Reachable at `/.well-known/void`
According to the VoID recommendation, the metadata description of a SPARQL server should preferably be accessible as a file at the `/.well-known/void` URL. To make it possible, we add the following rules to the Apache configuration, after replacing `DATASET-URI` with the right URI:
```
     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "text/html" [nocase]
     RewriteRule "^/.well-known/void?$" "/describe/?url=DATASET-URI" [R=303,L]

     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "text/turtle" [nocase]
     RewriteRule "^/.well-known/void?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<DATASET-URI>&output=text/turtle" [R=303,L]

     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "text/plain" [nocase]
     RewriteRule "^/.well-known/void?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<DATASET-URI>&output=text/plain" [R=303,L]

     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "application/json" [nocase]
     RewriteRule "^/.well-known/void?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<DATASET-URI>&output=application/json" [R=303,L]

     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "application/rdf\+json" [nocase]
     RewriteRule "^/.well-known/void?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<DATASET-URI>&output=application/rdf\%2bjson" [R=303,L]

     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "application/ld\+json" [nocase]
     RewriteRule "^/.well-known/void?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<DATASET-URI>&output=application/ld\%2bjson" [R=303,L]

     RewriteCond "%{REQUEST_METHOD}" "GET" [nocase]
     RewriteCond "%{QUERY_STRING}" "^$"
     RewriteCond "%{HTTP_ACCEPT}" "application/rdf\+xml" [nocase]
     RewriteRule "^/.well-known/void?$" "/sparql?query=define+sql:describe-mode+'CBD'+DESCRIBE+<DATASET-URI>&output=application/rdf\%2bxml" [R=303,L]
```
Note that in these rules, the `text/html` requests are redirected to the facets.
