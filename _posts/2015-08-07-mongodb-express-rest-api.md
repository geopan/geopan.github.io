---
layout: post
title: "Part 2 - A Rest API using Node and Express"
modified: 2015-08-22 16:45:53 +1000
category: [blog]
tags: [mongodb, node, nodejs, express, rest, restfull, api]
image:
  feature: texture-feature-03.jpg
  credit: "&copy;2015 DigitalGlobe"
  creditlink: http://earthview.withgoogle.com/
comments: true
share: true
---

There are plenty of ways of doing that...

First of all I like to organise my apps the following way:

~~~ code
app.js
api/
  suburb/
    index.js
    suburb.model.js
    suburb.controller.js
package.json
~~~

## Package.json

~~~ json
{
  "name": "my-rest-api",
  "version": "0.1.0",
  "main": "app.js",
  "engines": {
    "node": ">=0.10.0"
  },
  "dependencies": {
    "body-parser": "1.12.x",
    "compression": "1.5.x",
    "express": "4.13.x",
    "mongoose": "4.1.x",
    "morgan": "1.6.x"
  }
}
~~~

## App.js

Here is a basic exemple of an express application:

~~~ javascript
var express = require('express'),
    mongoose = require('mongoose'),
    bodyParser = require('body-parser'),
    compression = require('compression'), // will automatically compress the response
    morgan = require('morgan'); // logs in console

var app = express();

app.use(compression());
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended:false}));
app.use(morgan('dev'));

// Look into the index.js to set the routes /suburbs
app.use('/suburbs', require('./api/suburb'));

var server = require('http').createServer(app);

mongoose.connect('mongodb://localhost/sub', function() {

  server.listen(8080, function () {
    console.log('Express server listening on %d, in %s mode', 8080, 'dev');
  });

});

// Expose app
exports = module.exports = app;
~~~

## Mongoose model

~~~ javascript
var mongoose = require('mongoose');

var SuburbSchema = new mongoose.Schema({
  type: { type: String, trim: true },
  properties: {
    ssc_code: { type: String, trim: true },
    ssc_name: { type: String, trim: true },
    conf_value: { type: String, trim: true },
    id: { type: Number },
  },
  geometry: {
    type: { type: String, enum: ['Polygon'] },
    coordinates: Array
  }
});

// Define spatial index
SuburbSchema.index({ geometry: '2dsphere' });

// Export Model
module.exports = mongoose.model('Suburb', SuburbSchema);
~~~

## Controller

~~~ javascript
var Suburb = require('./suburb.model');

// Define the getAll function
exports.getAll = function(req, res, next) {

  Suburb
    .find()
    .limit(100) // defines a limit for testing.
    .lean() // returns plain javascript objects instead of MongooseDocuments.
    .exec(function(err, docs) {
    if (err) return next(err);
    if (!docs) return next(new Error('Oops nothing found.'));
    return res.json(docs);
  });

};

// Defines the getOne function
exports.getOne = function(req, res, next) {

  Suburb.findById(req.params.id, function(err, doc) {
    if (err) return next(err);
    if (!doc) return next(new Error('Oops nothing found.'));
    return res.json(doc);
  });

};
~~~

## Router

For this example, we ll put all the routes directly in api/suburbs/index.js.

~~~ javascript
var express = require('express'),
    ctrl = require('./suburb.controller');

var router = express.Router();

router.get('/', ctrl.getAll);
router.get('/:id', ctrl.getOne);

module.exports = router;
~~~