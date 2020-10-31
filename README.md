# Coronavirus Dresden Data

[![Go to data collection code](https://img.shields.io/badge/corona-data-yellow)](https://github.com/jdieg0/coronavirus-dresden)
[![License](https://img.shields.io/badge/license-DL--DE--BY--2.0-green)](https://github.com/jdieg0/coronavirus-dresden-data/blob/main/LICENCE)
[![Last commit](https://img.shields.io/github/last-commit/jdieg0/coronavirus-dresden-data)](https://github.com/jdieg0/coronavirus-dresden-data/commits/main)

The present raw data is published daily around midday by the [city of Dresden](https://www.dresden.de/de/leben/gesundheit/hygiene/infektionsschutz/corona.php). This is the data set that the city uses for its official [dashboard](https://stva-dd.maps.arcgis.com/apps/opsdashboard/index.html#/3eef863531024aa4ad0c4ac94adc58e0).

There is no history of the data from the city side, as the same data set, which is offered for download, is always overwritten by the daily updated state of data.

This repository contains up-to-the-minute data, which I have been recording since 18.10.2020 in the course of developing my own [SARS-CoV-2 dashboard](https://github.com/jdieg0/coronavirus-dresden). On the project site you will also find further information on how you can keep track of the data changes yourself.

## Data source and license

The data sets are obtained from the following source:

- [Overview over all tables and data fields](https://services.arcgis.com/ORpvigFPJUhb8RDF/ArcGIS/rest/services/corona_DD_7_Sicht/FeatureServer/layers)
- [Web form for queries](https://services.arcgis.com/ORpvigFPJUhb8RDF/ArcGIS/rest/services/corona_DD_7_Sicht/FeatureServer/query)
- [Download the latest JSON dump](https://services.arcgis.com/ORpvigFPJUhb8RDF/arcgis/rest/services/corona_DD_7_Sicht/FeatureServer/0/query?f=json&where=ObjectId>=0&outFields=*)

The Data is available under an open licence compatible with CC-BY: *Landeshauptstadt Dresden, [dl-de/by-2-0](https://www.govdata.de/dl-de/by-2-0), [opendata.dresden.de](https://opendata.dresden.de/)*.

## Files

The data is archived in two different JSON styles in this repo:

- Under [```/json```](json) are the JSON files without line breaks and indentations, which can potentially be processed faster.
- Under [```/pjson```](pjson) there is "pretty printed" JSON, which is **human-readable and more convenient for browsing** and debugging.

The version of the JSON files is stored in the file name as an [ISO-8601](https://en.wikipedia.org/wiki/ISO_8601) (UTC) date, that is, in the form:

    YYYY-MM-DDTHH:MM:SSZ.json

The date is the time of detection and download of a new data set. Every minute a [cronjob](https://github.com/jdieg0/coronavirus-dresden#cron) searches for modifications, i.e. the data set was usually updated by the city within the minute before.

## Processing the data

### „Corona Dresden“ Python script + InfluxDB

The same [script](https://github.com/jdieg0/coronavirus-dresden) I use to collect the data can also be used to import the JSON files into [InfluxDB](https://www.influxdata.com/products/influxdb-overview/). From there it can be processed and visualised using the SQL-like query language [InfluxQL](https://docs.influxdata.com/influxdb/v1.8/query_language/spec/) and, for instance, [Grafana](https://grafana.com/docs/grafana/latest/datasources/influxdb/).

Installation instructions can be found on the [*Coronavirus Dresden* project site](https://github.com/jdieg0/coronavirus-dresden).

#### Usage

When everything is [set up](https://github.com/jdieg0/coronavirus-dresden#install), the JSON file can be read and written to InfluxDB with the following command:

    python collect.py --file YYYY-MM-DDTHH:MM:SSZ.json

If you want to import a file without a valid ISO-8601 timestamp or if you want to use a different date, you can pass it along with:

    python collect.py --file file.json --date YYYY-MM-DDTHH:MM:SSZ

To use the current date and ignore the file name, type:

    python collect.py --file file.json --auto-date


Note that the script uses a cache file to track changes. If you have previously imported the same file, you must force the import with

    python collect.py --file YYYY-MM-DDTHH:MM:SSZ.json --force-collect

or prevent the saving of a cache file during the initial import:

    python collect.py --file YYYY-MM-DDTHH:MM:SSZ.json --no-cache

To display all data collection options, type:

    python collect.py --help
