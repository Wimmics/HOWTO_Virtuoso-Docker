# Dump export

When you enrich your knowledge base directly from Update SPARQL queries or just by computing and saving statistics on it,
It must be interresting for you to export a dump file directly from your virtuoso database.

For example the French DBpedia chapter is based on the dump hosted on the Databus but a lot of refiments are made on the database, 
all that works are after it recorded into a turtle file.

1. Declaration of the procedure

You first need to declare a new database procedure into your conductor.
Several procedures are already given into the virtuoso documentation :  
https://docs.openlinksw.com/virtuoso/rdfperfdumpandreloadgraphs/ 


We slightly simply the namedgraph export in the use case of the french DBpedia for being able to save it into a single file :  
https://github.com/Wimmics/dbpedia-virtuoso-sparql-endpoint-quickstart/blob/master/dbpedia-loader/virtuoso_proc/create_dump


2.  Docker app configuration

 * Add into your docker configuration the destination file to the VIRT_Parameters_DirsAllowed parameter
 * Do not forget to add it into a shared volume
 * Declare the procedure : you can do it via the conductor GUI or via a script 
  
