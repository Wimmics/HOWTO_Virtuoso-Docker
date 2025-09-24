# Virtuoso on docker installation

Depending on your use case different possibilities are offered to you :
* You just need to host a server > go to [section 1.1](#1-simple-docker-image-installation).
* You had to respect the reproducibility rules of a complete data process that only needs to load your data > go to [section 1.2](#2-docker-compose-simple-case).
* You are in the case where you need to extend the virtuoso application with another one > go to [section 1.3](#3-docker-compose-for-extending-the-virtuoso-image).


#### Virtuoso env variables

all the information of the virtuoso.ini could be updated :

https://people.cs.aau.dk/~matteo/notes/virtuoso-setup-on-docker.html
From the docker image documentation, the naming convention are the followings :
`VIRT_$SECTION_$KEY=VALUE` Where :
*    VIRT is a common prefix to group such  variables together (always in upper case)
*  SECTION is the name of the [section] in virtuoso.ini (case insensitive)
*   KEY is the name of a key within the section (case insensitive)
    VALUE is the text to be written into the key (case sensitive)

## 1. Simple Docker image installation

To load Openlink virtuoso image:
```
docker pull openlink/virtuoso-opensource-7:latest
```
for a specific version:
```
docker pull openlink/virtuoso-opensource-7:7.2
```
Before running the container for the first time it is necessary to create a dba password and store it in the $VIRTUOSO_DBA_PWD env variable. (We choose to set the variable in the user's .bashrc file. If you do the same remember to restart the user's session.)

The Openlink virtuoso image offers a mounting point `/database/` where the folder containing the database files and the `.ini` file can be mounted to make the data and configuration persistent.

As an example, an example with the database stored in the current folder:
```
docker run  \
    --name docker-virtuoso \
    --detach \
    --restart always \
    --env DBA_PASSWORD=$VIRTUOSO_DBA_PWD \
    --publish 1111:1111 \
    --publish 8890:8890 \
    --volume `pwd`:/database \
    openlink/virtuoso-opensource-7:latest
```
For sharing data and scripts between the host and the container create an `/import` directory under the directory mapped to `/database` directory on the container. Add this directory to the allowed directories list in *virtuoso.ini file*.

The option `--restart always` ensures that the Vrtuoso service restarts as the Docker deamon restarts.

```
DirsAllowed = ., ../vad, /usr/share/proj, /database/import
```
To restart virtuoso container:
```
docker restart  docker-virtuoso
```

## 2. Docker compose (simple case)

The simple docker-compose case is when you just need to respect a reproducible data process. In that case, you will "just" have to build an app composed by :
* The official virtuoso image
* The data process run via a new docker image that you will create on your own. This one will simply be called a bash script operating the data process

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

The first layer of the docker-compose file called *store* is similar to the use case illustrated in section 1.1. including all the parameters of the previous docker-run command.

The second one is the new docker image that we will create. We are specifying into the build field where we can find the Dockerfile used for building the image. Notice here that we also list the environment variables leveraged into the Unix environment of our docker app.

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


## 3. Docker compose for extending the virtuoso image

In some cases, you could be in a situation where you must have to extend the virtuoso image. The best way for it would be to create a docker-compose file (you may simply create a Docker file but creating a docker-compose one prevents to build and run the app) :
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
Compared with the previous section example, notice that we don't use the virtuoso image. We extend it by rewriting a Dockerfile. Let's see how to extend it via an example used by the DBpedia french chapter :
```
FROM openlink/virtuoso-opensource-7

USER root
RUN apt-get update
RUN apt-get -y install git python
RUN cd /opt && git clone https://github.com/datalogism/dbpedia-vad && cd dbpedia-vad && ./build.sh

```
Firstly we upload the virtuoso image that we extend by installing an application coming from a GitHub repo
