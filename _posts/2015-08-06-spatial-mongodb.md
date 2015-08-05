---
layout: post
title: "A Spatial Mongodb database"
description: "A ton of text to test readability."
modified: 2015-08-05 18:28:53 +1000
category: [blog]
tags: [mongodb, geojson, qgis, postgis]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

The first step is to create a Mongodb database with some spatial data. For this example, we will use the Australian suburbs layer available on the [Australian Bureau of Statistics](http://www.abs.gov.au/AUSSTATS/abs@.nsf/DetailsPage/1270.0.55.003July%202011).

It took me a bit of time to figure out the right format in import for Mongo. It is actually the `Features` part of a GeoJson:

~~~ json
[
  {"geometry":{"type":"Polygon","coordinates":[...]},"properties":{...}},
  {"geometry":{"type":"Polygon","coordinates":[...]},"properties":{...}},
  {"geometry":{"type":"Polygon","coordinates":[...]},"properties":{...}}
]
~~~

Geometries have to be on 2 dimensions (no z or altitude).

Once your certain that your data is in the right format, you can launch the following command which will import each geojson object into the database.

~~~ shell
mongoimport --db geodb path/to/file.geojson --collection suburbs –-jsonArray
~~~

The next step is to create a spatial index on the field containing the geometry in our new collection.

~~~ shell
mongo
> use geodb
> db.suburbs.ensureIndex({geometry:'2dsphere'})
~~~ 