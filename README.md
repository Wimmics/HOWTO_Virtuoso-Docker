# HOWTO: Using OpenLink Virtuoso OS + Docker + Apache

[![License: CC BY 4.0](https://img.shields.io/badge/License-CC_BY_4.0-lightgrey.svg)](https://creativecommons.org/licenses/by/4.0/)

This repository centralizes technical guidelines for deploying knowledge graphs using the [Openlink Virtuoso](https://virtuoso.openlinksw.com/) OS server.
These guidelines stem from the experience gained by [Inria](https://www.inria.fr/)'s [WIMMICS team](https://www.inria.fr/fr/wimmics) on five different projects, each with a different use case, and cover the functionalities that were common to most of the five projects.

Salient elements are: Docker-based deployment, setup of an Apache web server front-end, HTTPS configuration, RDF bulk loading, providing rich metadata and making it available via .well-known or endpoint URL dereferencing. 

This is continuously improved, feed-back is most welcome!


## Basic

- [Installation](Basic/Installation.md#virtuoso-on-docker-installation)
- [Running commands](Basic/Run_commands.md)
- [Loading RDF data into Virtuoso](Basic/Loading_data.md#loading-data-on-the-server)
- [Installing the Faceted Browser](Basic/Facets.md#installing-the-faceted-browsing-service)
- [Running Virtuoso as system daemon](Basic/Create_system_service.md#virtuoso-as-a-system-service-on-linux)

## Advanced

- [Apache web server configuration](Advanced/Apache_configuration.md#web-server-configuration)
- [Apache web server configuration for IRI dereferencing](Advanced/Dereferencement.md)
- [Making HTTPS work, with SSL](Advanced/SSL.md)
- [Metadata description](Advanced/Metadata_description.md#metadata-description)
- [Tips and Tricks](Advanced/TipsTricks.md)
- [Export RDF dump](Advanced/Dump%20export.md)
- [Add custom prefixes to query GUI](Advanced/AddCustomNsPrefix.md)
- [Advanced Virtuoso configuration](Advanced/Advanced_Virtuoso_config.md)

## Motivating projects

- [Covid-on-the-Web Dataset](https://github.com/Wimmics/CovidOnTheWeb/)
- [French DBpedia Chapter](https://github.com/Wimmics/dbpedia-virtuoso-sparql-endpoint-quickstart)
- [WeatherKG meteorological measurements dataset](https://github.com/Wimmics/weather-kg/tree/main/meteo)
- [ISSA scientific publications dataset](https://github.com/issa-project)
- [Wasabi song and music dataset](https://github.com/micbuffa/WasabiDataset)
- [IndeGx: Framework for an index of SPARQL endpoints](https://github.com/Wimmics/dekalog)
