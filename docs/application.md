Application class is a building class which initializes the crucial parts of the web-framework, it contains the main handler stack and the HTTP server.

## Available methods

[`host/0`](#host)
[`root/0`](#root)
[`custom/0`](#custom)
[`port/0`](#port)
[`reuse_port/0`](#reuse_port)
[`router/0`](#router)
[`server/0`](#server)
[`key_file/0`](#key_file)
[`cert_file/0`](#cert_file)
[`run/0`](#run)


### host

The host of the server.

```ruby
class Application < Grip::Application
  def host : String
    "127.0.0.1"
  end
end
```

### port

The port of the server.

```ruby
class Application < Grip::Application
  def port : Int32
    6969
  end
end
```

### reuse_port

The reuse_port boolean of the server.

```ruby
class Application < Grip::Application
  def reuse_port : Bool
    true
  end
end
```

### server

The server of the application.

```ruby
class Application < Grip::Application
  def server : HTTP::Server
    HTTP::Server.new(router)
  end
end
```

### key_file

The SSL key file location of the application.

```ruby
class Application < Grip::Application
  def key_file : String
    "SSL_KEY_FILE_LOCATION"
  end
end
```

### cert_file

The SSL certificate file location of the application.

```ruby
class Application < Grip::Application
  def cert_file : String
    "SSL_CERTIFICATE_FILE_LOCATION"
  end
end
```

### run

The run of the application executes the main application loop.
