HTTP::Handler is the building block of the framework.

Creating a custom middleware is as easy as creating an `HTTP handler`:

```crystal
# src/application.cr: Defines a Grip application with custom middleware and routing.

# Imports the Grip framework for building the web application.
require "grip"

# Example is a custom middleware that processes HTTP requests.
class Example
  include HTTP::Handler

  # Processes the HTTP request context, returning it for further handling.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context, optionally modified for the next handler.
  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    context # Returns the context unchanged; can be extended to mutate headers or other properties.
  end
end

# DemoController handles HTTP GET requests for specific routes.
class DemoController
  include Grip::Controllers::HTTP

  # Responds to GET requests with a JSON response.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a JSON response.
  def get(context : Context) : Context
    context
      .json("Hello, World!") # Returns a JSON response with a greeting.
  end
end

# Application configures and runs a Grip web application.
class Application < Grip::Application
  # Defines the array of HTTP handlers for processing requests.
  property handlers : Array(HTTP::Handler) = [
    Grip::Handlers::Pipeline.new, # Manages the request pipeline for middleware execution.
    Grip::Handlers::HTTP.new      # Handles core HTTP protocol logic.
  ] of HTTP::Handler

  # Initializes the application and configures routes.
  def initialize
    routes # Calls the routes method to set up routing and pipelines.
  end

  # Defines routing structure and pipelines for the application.
  def routes
    # Defines a pipeline for web requests, applying the custom Example middleware.
    pipeline :web, [
      Example.new # Applies the custom middleware to process requests.
    ]

    # Defines a pipeline for API requests, also applying the Example middleware.
    pipeline :api, [
      Example.new # Applies the custom middleware to process requests.
    ]

    # Routes under the root path ("/").
    scope "/" do
      pipe_through :web # Applies the :web pipeline (Example middleware) to requests.

      # Maps GET "/" to DemoController’s get method.
      get "/", DemoController

      # Routes under the "/users" namespace.
      scope "/users" do
        # Applies both :web and :api pipelines (two instances of Example middleware).
        pipe_through :api

        # Maps GET "/users/" to DemoController’s get method.

        get "/", DemoController
      end
    end
  end
end

# Instantiates and starts the Grip application.
app = Application.new
app.run
```

Advantage of the pipe over a raw middleware is that you can controll which routes go through the middleware and which don't.
