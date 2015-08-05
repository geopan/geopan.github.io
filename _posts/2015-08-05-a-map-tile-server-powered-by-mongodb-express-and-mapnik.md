---
layout: post
title: "A map tile server powered by MongoDB, Express and Mapnik"
modified: 2015-08-05 18:28:53 +1000
category: [blog]
tags: [mongodb, mapnik, express, nginx, node-mapnik, node]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: 
---

Using Mongo for spatial data is possible but most of the examples I have seen are limited to point data. Using it as primary database system for geometric data is still unorthodox. This post will describe how to use Mongo that way and how to serve map tiles with Node, Express and Mapnik.

## What is a map tiles server ?

JavaScript runs in the browser, which dynamically requests maps from a server in the background (without reloading the whole HTML page) to give a smooth slippy zoomy map browsing experience. The implementation of this is provided by a javascript library, either OpenLayers or Leaflet for example. The map image is built up of many little square images called "tiles". These are rendered and served from a "tile server".

## What is Mapnik ?

Mapnik is an open source toolkit for rendering maps. Among other things, it is used to render the five main layers on the OpenStreetMap website. It supports a variety of geospatial data formats and provides flexible styling options for designing many different kinds of maps.

## What exactly are we building ?

First we will load some spatial data into mongo and make the newly created collection working with spatial query.

Then we will make an application that serve

- GeoJson
- Map tiles
- Pbf (Vector tiles)

We will create a simple map portal using leaflet for testing this 3 formats.

The magic of this is that all the code will be written in Javascript :).