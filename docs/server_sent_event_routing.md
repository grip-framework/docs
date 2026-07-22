This example demonstrates Grip’s Server-Sent Events (SSE) capabilities. It defines an SSE controller, custom middleware, pipeline-based routing, backpressure strategies, and fiber blocking to handle real-time HTTP streaming connections efficiently.

## Application code

```crystal
# src/application.cr: Defines a Grip application with Server-Sent Events support and middleware.

# Imports the Grip framework for building the web application.
require "grip"

# Wallet handles Server-Sent Event (SSE) streams and real-time domain events.
class Wallet
  include Grip::Controllers::ServerSent

  # Executed when a client establishes an SSE connection.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The modified context after streaming.
  def stream(context : Context) : Context
    # Initializes the SSE stream with backpressure configuration.
    # capacity: Sets the buffer size for pending events.
    # strategy: Defines how overflow is handled when the queue is full (e.g., Strategy::DropOldest).
    stream = context.stream(
      capacity: 100,
      strategy: Strategy::DropOldest,
    )

    # 1. Send initial connection handshake frame to the client.
    stream.connected(connection_id: ::UUID.random.to_s)

    # 2. Emit structured domain data payload.
    payload = {
      "amount"       => 0.25,
      "currency"     => "USD",
      "totalBalance" => 10.50,
    }

    stream.emit(
      Event::Type::Data,
      data: payload,
      id: 1001,
    )

    # 3. Emit a protocol error frame if an error header is present.
    if context.request.headers.has_key?("X-Simulate-Error")
      stream.error(
        {
          "message" => "Simulated error occurred",
          "code"    => 500,
        }
      )
    end

    # Blocks the fiber, sends ping responses and keeps the HTTP connection open until the client disconnects.
    stream.await

    # Gracefully halts processing after stream closure.
    context.halt
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
    Grip::Handlers::Pipeline.new,   # Manages the request pipeline for middleware execution.
    Grip::Handlers::ServerSent.new, # Handles Server-Sent Events protocol logic.
    Grip::Handlers::HTTP.new,       # Handles core HTTP protocol logic, it must be the last one as it handles all the requests.
  ] of HTTP::Handler

  # Initializes the application and configures routes.
  def initialize
    routes # Calls the routes method to set up routing.
  end

  # Defines routing structure and pipelines for SSE streaming endpoints.
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

      # Maps Server-Sent Event streaming endpoints to Wallet's stream method.
      sse "/wallet", Wallet, as: :stream
    end
  end
end

# Instantiates and starts the Grip application.
app = Application.new
app.run
```