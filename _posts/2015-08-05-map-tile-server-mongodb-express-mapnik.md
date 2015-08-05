---
layout: post
title: "A map tile server powered by MongoDB, Express and Mapnik"
modified: 2015-08-05 18:28:53 +1000
category: [blog]
tags: [mongodb, mapnik, express, nginx, node-mapnik, node]
image:
  feature: texture-feature-01.jpg
  credit: "&copy;2015 DigitalGlobe"
  creditlink: http://earthview.withgoogle.com/
comments: true
share: true
---

Using Mongo for spatial data is possible but most of the examples on the Internet are limited to point data. Using it as datastore for geometric data is still unorthodox. This post will describe how to use Mongo that way and how to serve map tiles with Node, Express and Mapnik.

## What is a tile server ?

JavaScript runs in the browser, which dynamically requests maps from a server in the background (without reloading the whole HTML page) to give a smooth slippy zoomy map browsing experience. The implementation of this is provided by a javascript library, either OpenLayers or Leaflet for example. The map image is built up of many little square images called "tiles". These are rendered and served from a "tile server".

## What is Mapnik ?

Mapnik is an open source toolkit for rendering maps. Among other things, it is used to render the five main layers on the OpenStreetMap website. It supports a variety of geospatial data formats and provides flexible styling options for designing many different kinds of maps.

## What exactly are we building ?

We will start by loading some spatial data into mongo and make the newly created collection working with spatial query. Then we will make an application that serve:

* GeoJson
* Map tiles
* Pbf (Vector tiles)

We will create a simple map portal using leaflet and mapbox-gl for testing this 3 formats. All the code will be written in Javascript.