This example demonstrates Grip’s support for multiple HTTP verbs in its routing mechanism. The router maps various HTTP methods (GET, POST, PUT, DELETE, PATCH, HEAD, OPTIONS) to corresponding methods in a controller, enabling flexible handling of RESTful API requests.

## Application Code

```crystal
# src/application.cr: Defines a Grip application with routing for multiple HTTP verbs.

# Imports the Grip framework for building the web application.
require "grip"

# DemoController handles HTTP requests for various HTTP methods.
class DemoController
  include Grip::Controllers::HTTP

  # Handles GET requests to the root path.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a response.
  def get(context : Context) : Context
    context
  end

  # Handles POST requests to the root path.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a response.
  def post(context : Context) : Context
    context
  end

  # Handles PUT requests to the root path.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a response.
  def put(context : Context) : Context
    context
  end

  # Handles DELETE requests to the root path.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a response.
  def delete(context : Context) : Context
    context
  end

  # Handles PATCH requests to the root path.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a response.
  def patch(context : Context) : Context
    context
  end

  # Handles HEAD requests to the root path.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a response.
  def head(context : Context) : Context
    context
  end

  # Handles OPTIONS requests to the root path.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a response.
  def options(context : Context) : Context
    context
  end
end

# Application configures and runs a Grip web application.
class Application
  include Grip::Application
  # Defines the array of HTTP handlers for processing requests.
  property handlers : Array(HTTP::Handler) = [
    Grip::Handlers::HTTP.new # Handles core HTTP protocol logic.
  ] of HTTP::Handler

  # Initializes the application and configures routes.
  def initialize
    routes # Calls the routes method to set up routing.
  end

  # Defines routes for multiple HTTP verbs.
  def routes
    # Maps GET "/" to DemoController’s get method, wrapped in a Proc for indirect dispatch.
    get "/", DemoController

    # Maps POST "/" to DemoController’s post method, wrapped in a Proc for indirect dispatch.
    post "/", DemoController

    # Maps PUT "/" to DemoController’s put method, wrapped in a Proc for indirect dispatch.
    put "/", DemoController

    # Maps DELETE "/" to DemoController’s delete method, wrapped in a Proc for indirect dispatch.
    delete "/", DemoController

    # Maps PATCH "/" to DemoController’s patch method, wrapped in a Proc for indirect dispatch.
    patch "/", DemoController

    # Maps HEAD "/" to DemoController’s head method, wrapped in a Proc for indirect dispatch.
    head "/", DemoController

    # Maps OPTIONS "/" to DemoController’s options method, wrapped in a Proc for indirect dispatch.
    options "/", DemoController
  end
end

# Instantiates and starts the Grip application.
app = Application.new
app.run
```
