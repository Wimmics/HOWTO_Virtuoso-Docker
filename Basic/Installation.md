### Virtuoso on docker installation

Depending on your use case different possibilities are offered to you :
* You just need to host a server > go to section 1.1.
* You had to respect the reproducibility rules of a complete data process that only need load your data > go to section 1.2.
* You are in the case where you need to extend the virtuoso application with another one > go to section 1.3.

#### Simple Docker image installation

The Openlink virtuoso image offers a mounting point `/database/` where the folder containing the database files and the `.ini` file can be mounted to make the data and configuration persistent.

As an example, an example with the database stored in the current folder:
```
docker run --rm \
    --name docker-virtuoso \
    --interactive --tty \
    --env DBA_PASSWORD=<YOUR_PASSWORD> \
    --publish 1111:1111 \
    --publish 8890:8890 \
    --volume `pwd`:/database \
    openlink/virtuoso-opensource-7:latest
```
Anna's version of the same thing
```
docker run --name virtuoso \
     --rm -d \
    -p 8890:8890 -p 1111:1111 \
    -e DBA_PASSWORD=pass \
    -e DEFAULT_GRAPH=http://data-issa.cirad.fr/graph \
    -v ~/ISSA/volumes/virtuoso/database:/database \
    -v ~/ISSA/data/dataset-0-0/en/rdf:/source \
    -v ~/ISSA/pipeline/virtuoso:/import \
     openlink/virtuoso-opensource-7:7.2
```

#### Docker compose (simple case)

The simple docker compose case is when you just need to respect a reproductibility data process. In that case you will "just" have to build an app composed by :
* the official virtuoso image
* the data process run via a new docker image that you will create by your own. This one will simply called a bash script operating the data process

We could find here a template of *docker-compose.yml* invested into the DBpedia project :

```
version: '3'
services:

  store:
    image: openlink/virtuoso-opensource-7
    ports: ["${VIRTUOSO_HTTP_PORT}:8890","127.0.0.1:${VIRTUOSO_ISQL_PORT}:1111"]
    environment:
            DBA_PASSWORD: ${VIRTUOSO_ADMIN_PASSWD}
    restart: always
    volumes:
      - ${VIRTUOSO_DATABASE_DIR}:/opt/virtuoso-opensource/database
      - ${DATA_DIR}:/usr/share/proj
  load:
    build :
       context: ./dbpedia-loader
       dockerfile: Dockerfile
    environment:
      STORE_DATA_DIR: /usr/share/proj
      DATABASE_DIR: ${VIRTUOSO_DATABASE_DIR}
      STORE_DBA_PASSWORD: ${VIRTUOSO_ADMIN_PASSWD}
      STORE_ISQL_PORT: 1111
      DATA_DIR: /root/data
      DOMAIN: ${DOMAIN}
      fileUPDT: ${fileUPDT}
      DBP_LANG: ${DBP_LANG}
      DBP_CATEGORY: ${DBP_CATEGORY}
    volumes:
      - ${DATA_DIR}:/root/data
      - ${VIRTUOSO_DATABASE_DIR}:/opt/virtuoso-opensource/database

    links:
      - store
```

The first layer of the docker-compose file called *store* is just very similar to use case illustrated in the section 1.1. including all the parameters of the previous docker-run command.

The second one is the new docker image that we will create. We are specifying into the build field where we can find the Dockerfile used for building the image. Notice here that we also list the environement variables leveraged intot the Unix environment of our docker app.

Concerning the Dockerfile, please find here a template for building your own app :
```
FROM debian:jessie

LABEL org.aksw.dld=true org.aksw.dld.type="import" org.aksw.dld.require.store="virtuoso" org.aksw.dld.config="{volumes_from: [store]}"

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update
RUN apt-get install -y git virtuoso-opensource pigz pbzip2 bzip2

ADD import.sh /
RUN chmod +x /import.sh


ENTRYPOINT /bin/bash import.sh

```
We using here a very light Unix image that we extends by installing docker and archive utilities. We importing inside the image a script (the one used for processing the data), and we run it.


#### Docker compose for extending the virtuoso image

In some case you could be in the situation where you must have to extends the virtuoso image. The best way for it would be to create a docker-compose file (you may simply create a Docker file but creating a docker-compose one prevent to build and run the app) :
```
version: '3'
services:

  store:
    build :
            context: ./virtuoso
            dockerfile: Dockerfile
    ports: ["${VIRTUOSO_HTTP_PORT}:8890","127.0.0.1:${VIRTUOSO_ISQL_PORT}:1111"]
    environment:
            DBA_PASSWORD: ${VIRTUOSO_ADMIN_PASSWD}
            VIRT_Parameters_DirsAllowed: ${VIRT_Parameters_DirsAllowed}
            VIRT_PARAMETERS_NUMBEROFBUFFERS: ${VIRT_PARAMETERS_NUMBEROFBUFFERS}
            VIRT_PARAMETERS_MAXDIRTYBUFFERS: ${VIRT_PARAMETERS_MAXDIRTYBUFFERS}
    image: custom:1
    restart: always
    volumes:
      - ${VIRTUOSO_DATABASE_DIR}:/opt/virtuoso-opensource/database
```
Comparing with the previous section exemple notice that we don't use directly use the virtuoso image. Because we will extend it by rewritting a Dockerfile. Let's see how to extend it via an exemple used by the DBpedia french chapter :
```
FROM openlink/virtuoso-opensource-7

USER root
RUN apt-get update
RUN apt-get -y install git python
RUN cd /opt && git clone https://github.com/datalogism/dbpedia-vad && cd dbpedia-vad && ./build.sh

```
Firstly we upload the virtuoso image that we simply extends by installing an application coming from a github repo

#### Virtuoso env variables

all the information of the virtuoso.ini could be updated :

https://people.cs.aau.dk/~matteo/notes/virtuoso-setup-on-docker.html
From the the docker image documentation, the naming convention are the followings :
`VIRT_$SECTION_$KEY=VALUE` Where :
*    VIRT is common prefix to group such  variables together (always in upper case)
*  SECTION is the name of the [section] in virtuoso.ini (case insensitive)
*   KEY is the name of a key within the section (case insensitive)
    VALUE is the text to be written into the key (case sensitive)
