# Simple-Amqplib-Wrapper 

[Simple Amqplib Wrapper]() is a wrapper around [amqplib](https://www.npmjs.com/package/amqplib) abstracting all the complexity.

## Amqplib vs Simple-Amqplib-Wrapper ?

### Using Amqplib you do this

```
var q = 'tasks';
 
function bail(err) {
  console.error(err);
  process.exit(1);
}
 
// Publisher 
function publisher(conn) {
  conn.createChannel(on_open);
  function on_open(err, ch) {
    if (err != null) bail(err);
    ch.assertQueue(q);
    ch.sendToQueue(q, new Buffer('something to do'));
  }
}
 
// Consumer 
function consumer(conn) {
  var ok = conn.createChannel(on_open);
  function on_open(err, ch) {
    if (err != null) bail(err);
    ch.assertQueue(q);
    ch.consume(q, function(msg) {
      if (msg !== null) {
        console.log(msg.content.toString());
        ch.ack(msg);
      }
    });
  }
}
 
require('amqplib/callback_api')
  .connect('amqp://localhost', function(err, conn) {
    if (err != null) bail(err);
    consumer(conn);
    publisher(conn);
  });
```

### With [Simple-Amqplib-Wrapper]() it's super simple

```
const Amqp = require('simple-amqplib-wrapper');
const amqp = new Amqp('amqp://localhost');

const q = 'tasks';
const testTask = { message: 'First Task' };

const publisher = () => {
  return amqp.sendToQueue(q, testTask);
};

const consumer = () => {
  amqp.consumeStream(q).take(1).each((msg) => {
    console.log(msg);
    amqp.acknowledge(msg);
  });
};

publisher();
consumer();
```