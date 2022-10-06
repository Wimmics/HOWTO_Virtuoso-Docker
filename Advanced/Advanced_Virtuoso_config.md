# Advanced Virtuoso configuration


## Virtuoso parameters

We already introduced into the [Basic/Installation](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Basic/Installation.md) doc
how to configure the virtuoso parameters with VIRT_$SECTION_$KEY=VALUE  convention. We will talk here about the other interresting parameters that could be taken into account for a better resource usage.

* VIRT_PARAMETERS_NUMBEROFBUFFERS and VIRT_PARAMETERS_MAXDIRTYBUFFERS: these two parameters depends of your the memory available on your server, you could check it with the ```free -h``` commands. The best parameters are by default commented into the virtuoso.ini : https://github.com/tenforce/docker-virtuoso/blob/master/virtuoso.ini
            
* VIRT_PARAMETERS_MAXVECTORSIZE: This parameter will affect the number of triples that could manage virtuoso (usefull when you update your base) : The default is 1,000,000 and the largest allowed value is about 3,500,000.
* VIRT_HTTPSERVER_SERVERTHREADS : Configure the number of query that could manage in the same time a virtuoso instance (AKA VIRT_HTTPSERVER_MAXCLIENTCONNECTION)
* VIRT_HTTPSERVER_THREADSPERQUERY: Number of thread that could be leveraged by virtuoso for a query
* VIRT_HTTPSERVER_ASYNCQUEUEMAXTHREADS

Available configs : 
https://docs.google.com/spreadsheets/d/1-stlTC_WJmMU3xA_NxA1tSLHw6_sbpjff-5OITtrbFw/edit#gid=812792186

 ## Index and log 
 
 
