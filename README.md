### Express Pouchdb Replication Stream

This module provides an express endpoint for streaming bulk couchdb changes to pouchdb. It makes use of the great [PouchDB Replication Stream](https://github.com/nolanlawson/pouchdb-replication-stream) module.

### Usage

Basic usage enables streaming of one db to the client:

```javascript
var express = require('express');
var repStream = require('express-pouchdb-replication-stream');
var app = express();

app.use('/api/couchdb', repStream('http://user:pass@localhost:5984/db'));
```

### Database in Request

If you have per-user databases, or you want to get changes from different databases based on the request, this will work:

```javascript
app.use('/api/couchdb/:db', repStream({
  url     : 'http://user:pass@localhost:5984/',
  dbReq   : true
});
```

### Filtered Replication

In order to do filtered replication, there are two options. If the parameters are known beforehand, then this method can be used:

```javascript
app.use('/api/couchdb', repStream({
  url           : 'http://user:pass@localhost:5984/',
  filter        : 'myFilterName',
  query_params  : {
    prop1       : 'myFilterParameter1'
  }
});
```

However, if the filter options are also dynamic/based on the request, then the request data must be parsed first:

```javascript
app.use('/api/couchdb/:filterFunc/:filterBy', function(req, res, next){
  var filterFunc = req.params.filterFunc;
  var filterBy = req.params.filterBy;
  repStream({
    url           : 'http://user:pass@localhost:5984/',
    filter        : filterFunc,
    query_params  : {
      docName     : filterBy
    }
  })(req, res, next);
});
```

