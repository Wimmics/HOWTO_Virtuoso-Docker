# Adding a new namespace into the Ns prefixes list

If you wanna help your user to query easily you KB with externals ontology or vocabulary, you can add in the [Namespace Prefixes lists](https://fr.dbpedia.org/sparql/?help=nsdecl) 
custom prefixes. 

You just need to add if as follow into you isql command-line  :
```
DB.DBA.XML_SET_NS_DECL ('tag-fr', 'http://fr.dbpedia.org/tag/', 2);
```
