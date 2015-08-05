---
layout: post
title: "Part 1 - A Spatial Mongodb database"
modified: 2015-08-05 18:28:53 +1000
category: [blog]
tags: [mongodb, geojson, qgis, postgis]
image:
  feature: texture-feature-01.jpg
  credit: "&copy;2015 DigitalGlobe" 
  creditlink: http://earthview.withgoogle.com/
comments: true
share: true
---

The first step is to create a Mongodb database with some spatial data. For this example, we will use the Australian suburbs layer available on the [Australian Bureau of Statistics](http://www.abs.gov.au/AUSSTATS/abs@.nsf/DetailsPage/1270.0.55.003July%202011).

## Get the right format

It took me a bit of time to figure out the right format to use with the [mongoimport](http://docs.mongodb.org/manual/reference/program/mongoimport/) command. It is actually the `Features` part of a [GeoJson](http://geojson.org/) and the geometries have to be in 2 dimensions (no z or altitude). You can export the data in the right format by using [QGis](http://www.qgis.org/) and/or [ogr2ogr](http://www.gdal.org/ogr2ogr.html).

~~~ json
[
  {"geometry":{"type":"Polygon","coordinates":[...]},"properties":{...}},
  {"geometry":{"type":"Polygon","coordinates":[...]},"properties":{...}},
  {"geometry":{"type":"Polygon","coordinates":[...]},"properties":{...}}
]
~~~

## Import the data into mongo

Once your data is in the right format, run the following command. It will create a new document for each geojson object. Each document will have the same structure as the original GeoJson. It will make our job easier when we will need to export this data into proper GeoJson format.

~~~ shell
mongoimport --db geodb path/to/file.geojson --collection suburbs –-jsonArray
~~~

## Create the spatial index

The next step is to [create the spatial index](http://docs.mongodb.org/manual/tutorial/build-a-2dsphere-index/) on the geometric field (in our case "geometry") of our new collection.

~~~ shell
mongo
> use geodb
> db.suburbs.ensureIndex({geometry:'2dsphere'})
~~~

If nothing went wrong, you should now be able to query your collection using the [Mongodb geospatial query operators](http://docs.mongodb.org/manual/reference/operator/query-geospatial/).