# Installing the Faceted Browsing Service

## 1. Install the package
You can consult the official documentation here : http://vos.openlinksw.com/owiki/wiki/VOS/VirtuosoFacetsWebService
The Faceted Browsing service gives you access to a search engine interface. It can also be used for describing your resource.

That is a Virtuoso package that is by default given within the app into a .dav file format. For installing it you need to run the following command into the isql interface :
`vad_install('/opt/virtuoso-opensource/vad/fct_dav.vad', 0);`

The application needs to index your resources for activating the search engine. The best option is to index your data when you finish loading them. A good practice is to do it after each data load by running the following commands :
* `urilbl_ac_init_db();`
* `s_rank();`

## 2. Describe behavior

Describing a resource via the facet app could be time-consuming. When we ask to describe ?s, the service will list every ongoing and outgoing property returning all the triples shaped as *<?s> <?p> <?o>* and as *<?o> <?p> <?s>*.

A tip for speeding up it is to change the describe behavior as follows with isql ([see this post](https://community.openlinksw.com/t/how-to-change-default-describe-mode-in-faceted-browser/1691/3)) :
* `INSERT INTO DB.DBA.SYS_SPARQL_HOST VALUES ('*',null,null,null,'DEFINE sql:describe-mode \"CBD\"')`
