
# Elasticsearch Scroll Stream

[![Build Status](https://travis-ci.org/alcacoop/elasticsearch-scroll-stream.svg?branch=master)](https://travis-ci.org/alcacoop/elasticsearch-scroll-stream)

Elasticsearch Scroll query results as a Node.js Readable Stream.

This module works with the following Elasticsearch nodejs clients:

 - [elasticsearch](https://www.npmjs.org/package/elasticsearch) (official Elasticsearch js API)
 - [elastical](https://www.npmjs.org/package/elastical) (Discontinued support)


## Installing

Latest released version:

    npm install elasticsearch-scroll-stream --save


## Usage with the official Elasticsearch js API

Example with a [simple query strings](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/search-uri-request.html) query. 

```js
var elasticsearch = require('elasticsearch');
var ElasticsearchScrollStream = require('elasticsearch-scroll-stream');

var client = new elasticsearch.Client();

// Create index and add documents here...

// You need to pass the client instance and the query object
// as parameters in the constructor
var es_stream = new ElasticsearchScrollStream(client, {
  index: 'elasticsearch-test-scroll-stream',
  type: 'test-type',
  search_type: 'scan',
  scroll: '10s',
  size: '50',
  fields: ['name'],
  q: 'name:*'
});

// Pipe the results to other writeble streams..
es_stream.pipe(process.stdout);

es_stream.on('end', function() {
  console.log("End");
});

```

Example with a full request definition using the [Elasticsearch Query DSL](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl.html).

```js
var elasticsearch = require('elasticsearch');
var ElasticsearchScrollStream = require('elasticsearch-scroll-stream');

var client = new elasticsearch.Client();

// Create index and add documents here...

// You need to pass the client instance and the query object
// as parameters in the constructor
var es_stream = new ElasticsearchScrollStream(client, {
  index: 'elasticsearch-test-scroll-stream',
  type: 'test-type',
  search_type: 'scan',
  scroll: '10s',
  size: '50',
  fields: ['name'],
  body: {
    query: {
      bool: {
        must: [
          {
            query_string: {
              default_field: "_all",
              query: 'name:*'
            }
          }
        ]
      }
    }
  }
});

// Pipe the results to other writeble streams..
es_stream.pipe(process.stdout);

es_stream.on('end', function() {
  console.log("End");
});

```

See test files for more examples.


Developing
----------

Fork the git repo, clone it, then install the dev dependencies.

    cd elasticsearch-scroll-stream
    npm install

Make your changes, add tests, then run the tests to make sure nothing broke.

    make

NOTE: Tests require an ElasticSearch server running at http://127.0.0.1:9200/.
You can spawn a docker container for the purpose with:

    docker-compose up -d

which maps host port `127.0.0.1:9200` to container port `9200`


LICENSE
--------

The MIT License (MIT)

Copyright (c) 2014 Francesco Valente

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

