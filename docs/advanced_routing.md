The more advanced the route is the more time it takes when executed, since the call to the index function is an indirect call, meaning that the `index/1` never existed in the `Grip::Controllers::HTTP` class it needs to be wrapped in a `Proc` which then needs to be called.

```crystal
# AuthorizationHandler adds an Authorization header to incoming HTTP requests.
class AuthorizationHandler
  include HTTP::Handler

  # Adds a Bearer token to the request's Authorization header.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The modified context with the added header.
  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    context.put_req_header("Authorization", "Bearer eyMANEmJeFF=")
  end
end

# DemoController handles HTTP requests for specific routes.
class DemoController
  include Grip::Controllers::HTTP

  # Responds to GET requests with a JSON response.
  # The index method is wrapped in a Proc for routing, as it is indirectly called by Grip.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a JSON response (nil in this case).
  def index(context : HTTP::Server::Context) : HTTP::Server::Context
    context.json(nil)
  end
end

# Application configures and runs a Grip web application.
class Application < Grip::Application
  # List of HTTP handlers for processing incoming requests.
  property handlers : Array(HTTP::Handler) = [
    Grip::Handlers::Pipeline.new, # Manages the request pipeline.
    Grip::Handlers::HTTP.new     # Handles core HTTP protocol logic.
  ] of HTTP::Handler

  # Initializes the application and configures routes.
  def initialize
    routes
  end

  # Defines routing structure and pipelines for the application.
  def routes
    # Pipeline for API requests, applying authorization logic.
    pipeline :api, [
      AuthorizationHandler.new # Adds Bearer token to requests.
    ]

    # Pipeline for web requests, enabling response compression.
    pipeline :web, [
      HTTP::CompressHandler.new # Compresses HTTP responses.
    ]

    # Routes under the root path ("/").
    scope "/" do
      pipe_through :web # Applies compression to responses.
      pipe_through :api # Applies authorization header.

      # Maps GET "/" to DemoController#index, wrapped in a Proc for indirect dispatch.
      get "/", DemoController, as: :index
    end

    # Routes under the "/api" namespace.
    scope "/api" do
      pipe_through :api # Applies authorization header.

      # Routes under the "/api/v1" namespace.
      scope "/v1" do
        # Maps GET "/api/v1/" to DemoController#index, wrapped in a Proc for indirect dispatch.
        get "/", DemoController, as: :index
      end
    end
  end
end

# Instantiates and starts the Grip application.
app = Application.new
app.run
```
