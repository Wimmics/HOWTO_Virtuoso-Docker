# Installing the Faceted Browsing Service

## 1. Install the package 
You can consult the official documentation here : http://vos.openlinksw.com/owiki/wiki/VOS/VirtuosoFacetsWebService
The Faceted Browsing service give you access to a search engine interface, and can also be used for descriebing your ressource.

That is a Virtuoso package that is by default given within the app into a .dav file format. For installing it you need to run the following command into the isql interface :
`vad_install('/opt/virtuoso-opensource/vad/fct_dav.vad', 0);`

The application need to index your resources for activacting the search engine, the best option is to indexing your data when you finished to load them. A good practice is to do it after each data load by running the following commands :
* `urilbl_ac_init_db();`
* `s_rank();`

## 2. Describe behavior

Describing a ressource via the facet app could be time consuming. In fact when we ask to describe ?s, the service will list every ongoing and outgoing properties returning all the triples shaped as *<?s> <?p> <?o>* and as *<?o> <?p> <?s>*.

A tips for speed up it is to disabling change the describe behavior as follow with isql ([see this post](https://community.openlinksw.com/t/how-to-change-default-describe-mode-in-faceted-browser/1691/3) ) :
* `INSERT INTO DB.DBA.SYS_SPARQL_HOST VALUES ('*',null,null,null,'DEFINE sql:describe-mode \"CBD\"')`
