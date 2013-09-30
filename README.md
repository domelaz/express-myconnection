express-myconnection
============

Connect/Express middleware auto provides and auto closes/releases mysql connections. It has three different strategies of managing mysql connections during request/response life cycle.

### Strategies
*   `single` - creates single database connection for whole application instance. Connection is never closed. In case of disconnection it will try to reconnect again as described in [node-mysql docs](https://github.com/felixge/node-mysql).
*   `pool` - creates pool of connections on an app instance level, and serves a single connection from pool per request. The connections is auto released to the pool at the response end.
*   `request` - creates new connection per each request, and automatically closes it at the response end.

### Usage

Configuration is straightforward and you use it as any other middleware. First param it accepts is a  [node-mysql](https://github.com/felixge/node-mysql) module, second is a db options hash passed to [node-mysql](https://github.com/felixge/node-mysql) module when connection or pool are created. The third is string defining strategy type.

    // app.js
    ...
    var mysql = require('mysql'), // node-mysql module
        myConnection = require('express-myconnection'), // express-myconnection module
        dbOptions = {
          host: 'localhost',
          user: 'dbuser',
          password: 'password',
          port: 3306,
          database: 'mydb'
        };
      
    app.use(myConnection(mysql, dbOptions, 'single');
    ...
    
**express-myconnection** extends `request` object with `getConection(callback)` function, this way connection instance can be accessed anywhere in routers during request/response life cycle:

    // myrouter.js
    ...
    req.getConnection(function(err, connection) {
      if (err) return next(err);
      
      connection.query('SELECT 1 AS RESULT', [], function(err, results) {
        if (err) return next(err);
        
        results[0].RESULT;
        // 1
        
      });
      
    });
    ...
