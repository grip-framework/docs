WebSockets are easy to define and use thanks to the core team of the Crystal Programming Language.

```ruby
class DemoController < Grip::Controllers::WebSocket
  def on_open(context : Context, socket : Socket) : Void
    # Executed when a client opens a connection to the server.
  end

  def on_message(context : Context, socket : Socket, message : String) : Void
    # Executed when a client sends a message.
  end

  def on_ping(context : Context, socket : Socket, message : String) : Void
    # Executed when a client pings the server.
  end

  def on_pong(context : Context, socket : Socket, message : String) : Void
    # Executed when a server receives a pong.
  end

  def on_binary(context : Context, socket : Socket, binary : Bytes) : Void
    # Executed when a client sends a binary message.
  end

  def on_close(context : Context, socket : Socket, close_code : HTTP::WebSocket::CloseCode | Int?, message : String) : Void
    # Executed when a client closes the connection to the server.
  end
end

class PoweredByHeader
  include HTTP::Handler

  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    context
      .put_resp_header("Server", "grip")
  end
end

class SecureHeaders
  include HTTP::Handler

  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    context
      .put_resp_header("Security", "Absolutely")
  end
end

class Application < Grip::Application
  def initialize
    super(environment: "development", serve_static: false)

    pipeline :api, [
      PoweredByHeader.new
    ]

    pipeline :web, [
      SecureHeaders.new
    ]

    # WebSockets support the pipeline routing, keep in mind that
    # the `:id` URL here gives an option to fetch the `id` using the
    # `context : Context.fetch_path_params/0` function.
    scope "/" do
      pipe_through :web
      pipe_through :api

      ws "/:id", DemoController
    end
  end
end
```
