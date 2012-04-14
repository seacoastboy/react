# SocketServer

Stream-powered library for creating a socket server in PHP.

[![Build Status](https://secure.travis-ci.org/igorw/SocketServer.png)](http://travis-ci.org/igorw/SocketServer)

## Install

The recommended way to install SocketServer is [through composer](http://getcomposer.org).

```JSON
{
    "require": {
        "igorw/socket-server": "dev-master"
    }
}
```

## Usage

### Events

The `Igorw\SocketServer\Server` class extends [événement](https://github.com/igorw/evenement)
and allows you to bind to events.

* `connection`: Triggered whenever a new client connects to the server. Arguments: $conn.
* `data`: Triggered whenever a client sends data. Arguments: $data, $conn.
* `disconnect`: Triggered whenever a client disconnects. Arguments: $conn.

### Input

In order to communicate with the server, you can pass an input stream in
the constructor and bind to the `input` event. This allows you to trigger
custom events and run custom code when they happen.

### Running

The `run` method will start the event loop. The server will process connections,
data and input until it dies.

### Example

Here is an example of a simple HTTP server listening on port 8000:

    use Igorw\SocketServer\Server;

    $server = new Server('localhost', 8000);

    $i = 1;

    $server->on('data', function ($data, $conn) use (&$i) {
        $lines = explode("\r\n", $data);
        $requestLine = reset($lines);

        if ('GET /favicon.ico HTTP/1.1' === $requestLine) {
            $response = '';
            $length = 0;
        } else {
            $response = "This is request number $i.\n";
            $length = strlen($response);
            $i++;
        }

        $conn->write("HTTP 1.1 200 OK\r\n");
        $conn->write("Content-Type: text/html\r\n");
        $conn->write("Content-Length: $length\r\n");
        $conn->write("\r\n");
        $conn->write($response);
        $conn->close();
    });

    $server->run();

## Tests

To run the test suite, you need PHPUnit.

    $ phpunit

## License

MIT, see LICENSE.