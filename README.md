# node-handlersocket - Pure JavaScript client for HandlerSocket

node-handlersocket is a pure JavaScript client for HandlerSocket.

[HandlerSocket](https://github.com/ahiguti/HandlerSocket-Plugin-for-MySQL)
is a NoSQL plugin for MySQL.
See [auther's blog](http://yoshinorimatsunobu.blogspot.com/2010/10/using-mysql-as-nosql-story-for.html)
for more information.

# Requirements

- [Node.js](http://nodejs.org/) (> 0.3.0)
- [HandlerSocket](https://github.com/ahiguti/HandlerSocket-Plugin-for-MySQL) (tested with v1.0.6)

# Installation

    npm install node-handlersocket

# Examples

## find (select)

    var hs = require('node-handlersocket');

    var con = hs.connect();
    con.on('connect', function() {
    con.openIndex('test', 'EMPLOYEE', 'PRIMARY', [ 'EMPLOYEE_ID', 'EMPLOYEE_NO', 'EMPLOYEE_NAME' ],
      function(err, index) {
        index.find('=', 1, function(err, results) {
          console.log(results[0]);
          con.end();
        });
      });
    });

## insert

    var hs = require('node-handlersocket');

    var con = hs.connect({port : 9999});
    con.on('connect', function() {
      con.openIndex('test', 'EMPLOYEE', 'PRIMARY', [ 'EMPLOYEE_ID', 'EMPLOYEE_NO',
        'EMPLOYEE_NAME' ], function(err, index) {
        index.insert([100, 9999, 'KOICHIK'], function(err) {
          con.end();
        });
      });
    });

## update

    var hs = require('node-handlersocket');

    var con = hs.connect({port : 9999});
    con.on('connect', function() {
      con.openIndex('test', 'EMPLOYEE', 'PRIMARY', [ 'EMPLOYEE_ID', 'EMPLOYEE_NO',
        'EMPLOYEE_NAME' ], function(err, index) {
        index.update('=', 100, [100, 9999, 'EBIYURI'], function(err, rows) {
          console.log(rows);
          con.end();
        });
      });
    });

## remove (delete)

    var hs = require('node-handlersocket');

    var con = hs.connect({port : 9999});
    con.on('connect', function() {
      con.openIndex('test', 'EMPLOYEE', 'PRIMARY', [ 'EMPLOYEE_ID', 'EMPLOYEE_NO',
        'EMPLOYEE_NAME' ], function(err, index) {
        index.remove('=', 100, function(err, rows) {
          console.log(rows);
          con.end();
        });
      });
    });

# API

## Function : connect([ options ])

Open a connection to HandlerSocket server.

* Parameters
    * `options` (optional) : An object with the following properties:
        * `host` : Host name or address (default is `'localhost'`).
        * `port` : Port number (default is `9998`).

        **Note, the port 9998 only allows read operations, and the port 9999 allows write operations also.**
        See [HandlerSocket installation document](https://github.com/ahiguti/HandlerSocket-Plugin-for-MySQL/blob/master/docs-en/installation.en.txt) for more information.
* Returns
    * A new `Connection` object.

## Object : Connection

An object representing connection to HandlerSocket.
It is an instance of `EventEmitter`.

### Event : 'connect'

Emitted when a connection is established.

* Callback function: ` function()`

### Envent : 'end'

Emitted when the other end of the stream sends a FIN packet.

* Callback function: ` function()`

### Event : 'close' (hadError)

Emitted once the connection is fully closed.

* Callback function: ` function(hadError)`
    * Parameters
        * `hadError` : A boolean which says if the stream was closed due to a transmission error.

### Event : 'error'

Emitted when an error occurs.

* Callback function: ` function(err)`
    * Parameters
        * `err` : An error that occurred.

### Method : Connection.openIndex(database, table, index, columns, callback)

Open an index.

* Parameters
    * `database` : A database name.
    * `table` : A table name.
    * `index` : An index name. If 'PRIMARY' is specified, the primary index is open.
    * `columns` : An array of columns names.
    * `callback` : A function to be called when the response received.
* Callback function : `function(err, index)`
    * Parameters
        * `err` : An `Error` object when the request failed, otherwise `null`.
        * `index` : A new `Index` object.

### Method : Connection.end()

Half-closes the connection.

## Object : Index

An object representing MySQL's index.

### Method : Index.find(op, keys, [ limit, [ offset ] ], callback)

To read a records from a table using the index.

* Parameters
    * `op` : A search operation, one of `'='`, `'>'`, `'>='`, `'<'` and `'<='`.
    * `keys` : An array of index values.
    * `limit` (optional) : A maximum number of records to be retrieved (default is 1).
    * `offset` (optional) : A number of records skipped before retrieving records (default is 0)．
    * `callback` : A function to be called when the response received.
* Callback Function : `function(err, results)`
    * Parameters
        * `err` : An `Error` object when the request failed, otherwise `null`.
        * `results` : An array of records.
        Each recored is array of column values which correspond to `columns` parameter of `Connection.openIndex()`.

### Method : Index.insert(values, callback)

To add a records.

* Parametes
    * values : An array of new column values which correspond to `columns` parameter of `Connection.openIndex()`.
    * `callback` : A function to be called when the response received.
* Callback Function : `function(err)`
    * Parameters
        * `err` : An `Error` object when the request failed, otherwise `null`.

### Method : Index.update(op, keys, [ limit, [ offset ] ], values, callback)

To update a records.

* Parametes
    * `op` : A search operation, one of `'='`, `'>'`, `'>='`, `'<'` and `'<='`.
    * `keys` : An array of index values.
    * `limit` (optional) : A maximum number of records to be retrieved (default is 1).
    * `offset` (optional) : A number of records skipped before retrieving records (default is 0)．
    * values : An array of new column values which correspond to `columns` parameter of `Connection.openIndex()`.
    * `callback` : A function to be called when the response received.
* Callback Function : `function(err, rows)`
    * Parameters
        * `err` : An `Error` object when the request failed, otherwise `null`.
        * `rows` : A number of updated rows.

### Method : Index.remove(op, keys, [ limit, [ offset ] ], callback)

To delete a records.

* Parametes
    * `op` : A search operation, one of `'='`, `'>'`, `'>='`, `'<'` and `'<='`.
    * `keys` : An array of index values.
    * `limit` (optional) : A maximum number of records to be retrieved (default is 1).
    * `offset` (optional) : A number of records skipped before retrieving records (default is 0)．
    * `callback` : A function to be called when the response received.
* Callback Function : `function(err, rows)`
    * Parameters
        * `err` : An `Error` object when the request failed, otherwise `null`.
        * `rows` : A number of deleted rows.

# Test

node-handlersocket depends on [Vows](http://vowsjs.org/) for testing.

    mysql -u root -p test < sql/create.sql
    vows test/*.js
    mysql -u root -p test < sql/drop.sql

# Limitations

The encoding of MySQL server (`default-character-set` parameter in `[mysqld]` section)
which node-handlersocket supports is **only UTF-8**.

# License

node-handlersocket is licensed under the [MIT license](http://www.opensource.org/licenses/mit-license.php).
