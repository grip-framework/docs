This example demonstrates Grip’s WebSocket capabilities, made simple by the Crystal Programming Language’s core team. It defines a WebSocket controller, custom middleware, and pipeline-based routing to handle WebSocket connections efficiently.

## Application Code

```crystal
# src/application.cr: Defines a Grip application with WebSocket support and middleware.

# Imports the Grip framework for building the web application.
require "grip"

# DemoController handles WebSocket connections and events.
class DemoController
  include Grip::Controllers::WebSocket

  # Executed when a client opens a WebSocket connection.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @param socket [Socket] The WebSocket connection.
  def on_open(context : Context, socket : Socket) : Void
    # Handle connection opening logic here.
  end

  # Executed when a client sends a text message.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @param socket [Socket] The WebSocket connection.
  # @param message [String] The received text message.
  def on_message(context : Context, socket : Socket, message : String) : Void
    # Handle text message logic here.
  end

  # Executed when a client sends a ping message.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @param socket [Socket] The WebSocket connection.
  # @param message [String] The ping message.
  def on_ping(context : Context, socket : Socket, message : String) : Void
    # Handle ping logic here.
  end

  # Executed when the server receives a pong message.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @param socket [Socket] The WebSocket connection.
  # @param message [String] The pong message.
  def on_pong(context : Context, socket : Socket, message : String) : Void
    # Handle pong logic here.
  end

  # Executed when a client sends a binary message.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @param socket [Socket] The WebSocket connection.
  # @param binary [Bytes] The binary message.
  def on_binary(context : Context, socket : Socket, binary : Bytes) : Void
    # Handle binary message logic here.
  end

  # Executed when a client closes the WebSocket connection.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @param socket [Socket] The WebSocket connection.
  # @param close_code [HTTP::WebSocket::CloseCode | Int?] The close code (optional).
  # @param message [String] The close message.
  def on_close(context : Context, socket : Socket, close_code : HTTP::WebSocket::CloseCode | Int?, message : String) : Void
    # Handle connection closing logic here.
  end
end

# PoweredByHeader adds a Server header to HTTP responses.
class PoweredByHeader
  include HTTP::Handler

  # Adds a Server header to the response.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The modified context with the added header.
  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    context
      .put_resp_header("Server", "grip") # Sets the Server header to "grip".
  end
end

# SecureHeaders adds a Security header to HTTP responses.
class SecureHeaders
  include HTTP::Handler

  # Adds a Security header to the response.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The modified context with the added header.
  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    context
      .put_resp_header("Security", "Absolutely") # Sets a custom Security header.
  end
end

# Application configures and runs a Grip web application.
class Application
  include Grip::Application
  
  # Defines the array of HTTP handlers for processing requests.
  property handlers : Array(HTTP::Handler) = [
    Grip::Handlers::Pipeline.new, # Manages the request pipeline for middleware execution.
    Grip::Handlers::WebSocket.new # Handles WebSocket protocol logic.
    Grip::Handlers::HTTP.new,     # Handles core HTTP protocol logic, it must be the last one as it handles all the requests.
  ] of HTTP::Handler

  # Initializes the application and configures routes.
  def initialize
    routes # Calls the routes method to set up routing.
  end

  # Defines routing structure and pipelines for WebSocket connections.
  def routes
    # Pipeline for API requests, applying the PoweredByHeader middleware.
    pipeline :api, [
      PoweredByHeader.new # Adds a Server header to responses.
    ]

    # Pipeline for web requests, applying the SecureHeaders middleware.
    pipeline :web, [
      SecureHeaders.new # Adds a Security header to responses.
    ]

    # Routes under the root path ("/").
    scope do
      pipe_through :web # Applies the SecureHeaders middleware.
      pipe_through :api # Applies the PoweredByHeader middleware.

      # Maps WebSocket connections to "/:id" to DemoController’s WebSocket methods.
      # The :id parameter can be accessed via context.fetch_path_params.
      ws "/:id", DemoController
    end
  end
end

# Instantiates and starts the Grip application.
app = Application.new
app.run
```
