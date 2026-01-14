Routing is the key feature which distinguishes Grip from other Crystal based web frameworks.

```crystal
# DemoController handles HTTP requests for specific routes.
class DemoController
  include Grip::Controllers::HTTP

  # Responds to GET requests with a JSON response.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a JSON response (nil in this case).
  def get(context : HTTP::Server::Context) : HTTP::Server::Context
    context.json(nil)
  end
end

# Application configures and runs a Grip web application.
class Application
  include Grip::Application

  # List of HTTP handlers for processing incoming requests.
  property handlers : Array(HTTP::Handler) = [
    Grip::Handlers::HTTP.new # Handles core HTTP protocol logic.
  ] of HTTP::Handler

  # Initializes the application and configures routes.
  def initialize
    routes
  end

  # Defines routing structure and pipelines for the application.
  def routes
    # Maps GET "/" to DemoController#get
    get "/", DemoController
  end
end

# Instantiates and starts the Grip application.
app = Application.new
app.run
```
