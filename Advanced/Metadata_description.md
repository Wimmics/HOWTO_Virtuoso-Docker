### Metadata description

A dataset can be described using the [VoID](https://www.w3.org/TR/void/), [DCAT](https://www.w3.org/TR/vocab-dcat-2/) and [SPARQL-SD](https://www.w3.org/TR/sparql11-service-description/) vocabularies, with other more general vocabularies as complements.
* VoID is used to describe the dataset hosted by the server,
* SPARQL-SD is used to describe the endpoint of the server,
* DCAT is used to describe sets of datasets.

Other general recommended vocabularies include:
* [Schema](http://schema.org/), [DCTerms](http://purl.org/dc/terms/) and [SKOS](http://www.w3.org/2004/02/skos/core#) to write general descriptions.
* [FoaF](http://xmlns.com/foaf/0.1/) to write descriptions of peoples and organizations.
* [Prov-O](http://www.w3.org/ns/prov#) to write about the provenance of elements.

As a general rule, the metadata of RDF server must at least answer the questions of :
* Who ?
    * Who is responsible for the server ?
    * Who is contributing to the content of the server ?
* What ?
    * Is there a description of the content of the server ?
    * What is the name of the server ?
* When ?
    * When was the server last updated ?
    * When was the server first published ?
* How ?
    * What are the endpoints and/or dumps available to access the content of the server ?
    * How was the content of the data generated ?

A template for a basic description can be found [in this file on GitHub](https://github.com/Wimmics/dekalog/blob/master/template-description.ttl).

The description can be accessible in at least one of the three following manners :
* In the dataset itself
The description can be stored with the data in the server, possibly in its own graph. This as the advantage to facilitate the updates of the metadata.
* Reachable at `/sparql-sd`
According to the SPARQL-SD recommendation, any SPARQL server must make a SPARQL-SD description available at the `/sparql` if it is called without `query` argument. However, Virtuoso places its SPARQL query editor interface at this address. The Virtuoso server stores an automatically generated description in a graph named with the server's URL. Replacing or augmenting the content of this graph makes it reachable at `/sparql-sd`. As the generation of the content of this graph is not well documented, it is preferable to gather all description metadata in one file and to redirect the `/sparql-sd` URL in the apache configuration with the following line, as shown in the Apache server configuration section:
```
Redirect permanent /sparql-sd <URL/description.ttl>
```
* Reachable at `/.well-known/void`
According to the VoID recommendation, the metadata description of a SPARQL server should preferably be accessible as a file at the `/.well-known/void` URL. To make it possible, we recommend the use of the Apache configuration with the following line, as shown in the Apache server configuration section:
```
Redirect permanent /.well-known/void <URL/description.ttl>
```
