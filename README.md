# HOWTO: Using OpenLink Virtuoso OS + Docker + Apache

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

This repository centralizes technical guidelines for deploying knowledge graphs using the [Openlink Virtuoso](https://virtuoso.openlinksw.com/) OS server.
These guidelines stem from the experience gained by [Inria](https://www.inria.fr/)'s [WIMMICS team](https://www.inria.fr/fr/wimmics) on five different projects, each with a different use case, and cover the functionalities that were common to most of the five projects.

Salient elements are: Docker-based deployment, setup of an Apache web server front-end, HTTPS configuration, RDF bulk loading, providing rich metadata and making it available via .well-known or endpoint URL dereferencing. 

This is certainely not perfect, feed-back is obviously welcome!


## Basic

- [Installation](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Basic/Installation.md#virtuoso-on-docker-installation)
- [Running commands](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Basic/Run_commands.md)
- [Loading RDF data into Virtuoso](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Basic/Loading_data.md#loading-data-on-the-server)
- [Installing the Faceted Browser](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Basic/Facets.md#installing-the-faceted-browsing-service)
- [Running Virtuoso as system daemon](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Basic/Create_system_service.md#virtuoso-as-a-system-service-on-linux)

## Advanced

- [Apache web server configuration](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Advanced/Apache_configuration.md#web-server-configuration)
  - [Dereferencing](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Advanced/Dereferencement.md)
- [Making HTTPS work, with SSL](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Advanced/SSL.md)
- [Metadata description](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Advanced/Metadata_description.md#metadata-description)
- [Tips and Tricks](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Advanced/TipsTricks.md)
- [Export RDF dump](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Advanced/Dump%20export.md)
- [Add custom prefixes to query GUI](https://github.com/Wimmics/HOWTO_Virtuoso-Docker/blob/main/Advanced/AddCustomNsPrefix.md)
