# Installing the Faceted Browsing Service

## 1. Install the package
You can consult the official documentation here : http://vos.openlinksw.com/owiki/wiki/VOS/VirtuosoFacetsWebService
The Faceted Browsing service gives you access to a search engine interface. It can also be used for describing your resource.

That is a Virtuoso package that is by default given within the app into a .dav file format. To install it, use the vad_install isql command:
```
sudo docker exec virtuoso isql -H localhost -U dba -P $VIRTUOSO_DBA_PWD exec="vad_install('/opt/virtuoso-opensource/vad/fct_dav.vad', 0);"
```

Virtuoso needs to index the resources for the search engine to work. The best option is to index the data at loading time by running the following isql commands:
* `urilbl_ac_init_db();`
* `s_rank();`


## 2. Describe behavior

Describing a resource via the facet app could be time-consuming. Whe describing a resrouce ?s, the service will list every ongoing and outgoing property returning all the triples shaped as *<?s> <?p> <?o>* and as *<?o> <?p> <?s>*.

A tip to speed this up is to change the describe behavior as follows with isql ([see this post](https://community.openlinksw.com/t/how-to-change-default-describe-mode-in-faceted-browser/1691/3)) :
* `INSERT INTO DB.DBA.SYS_SPARQL_HOST VALUES ('*',null,null,null,'DEFINE sql:describe-mode \"CBD\"')`
