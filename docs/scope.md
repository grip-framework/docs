Scope defines a prefix for routes which will be nested in a block.

```crystal
# src/application.cr: Defines a Grip application with nested routing.

# Imports the Grip framework for building the web application.
require "grip"

# DemoController handles HTTP GET requests for specific routes.
class DemoController
  include Grip::Controllers::HTTP

  # Responds to GET requests with a JSON response.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a JSON response (null in this case).
  def get(context : Context) : Context
    context
      .json(nil) # Returns a JSON response with null content.
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

  # Defines nested routing structure for the application.
  def routes
    # Routes under the "/api/v1" namespace.
    scope "/api/v1" do
      # Maps GET "/api/v1/" to DemoController’s get method.
      get "/", DemoController

      # Routes under the "/api/v1/users" namespace.
      scope "/users" do
        # Maps GET "/api/v1/users/" to DemoController’s get method.
        get "/", DemoController

        # Empty scope allows you to pipe_through routes you do not want to branch out
        scope do
          # Maps GET "/api/v1/users/1/" to DemoController’s get method.
          get "/1", DemoController
        end
      end
    end
  end
end

# Instantiates and starts the Grip application.
app = Application.new
app.run
```
