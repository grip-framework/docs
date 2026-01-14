Grip comes with a pre-defined error handlers for the JSON response type. You can customize the built-in error pages or even add your own using the `error` macro.

```crystal
# IndexController handles HTTP requests and demonstrates raising a Grip-specific exception.
class IndexController
  include Grip::Controllers::HTTP

  # Handles GET requests by raising a Forbidden exception.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] Never returns normally due to raised exception.
  def get(context : HTTP::Server::Context) : HTTP::Server::Context
    raise Grip::Exceptions::Forbidden.new # Triggers the ForbiddenController via the error macro.
  end
end

# ForbiddenController handles Grip::Exceptions::Forbidden errors with a custom JSON response.
class ForbiddenController
  include Grip::Controllers::Exception

  # Custom handler for Forbidden exceptions, setting a 403 status and JSON error message.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a 403 status and JSON response.
  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    context
      .put_status(403) # Sets HTTP status to 403 (Forbidden), inherited from the exception.
      .json(
        {
          "error" => ["You lack privileges to access the current resource!"]
        }
      )
  end
end

# Application configures the Grip web application with error handlers and routes.
class Application
  include Grip::Application

  # Defines the array of HTTP handlers for processing requests.
  property handlers : Array(HTTP::Handler) = [
    Grip::Handlers::Exception.new, # Handles exceptions using defined error controllers.
  ] of HTTP::Handler

  # Initializes the application and sets up routes.
  def initialize
    routes # Calls the routes method to configure routing and error handling.
  end

  # Configures routes and error handlers for the application.
  def routes
    # Maps Grip::Exceptions::Forbidden to ForbiddenController for custom error handling.
    exception Grip::Exceptions::Forbidden, ForbiddenController
  end
end

# Demonstrates the difference between manual response handling and Grip exceptions.
# This controller shows a manual 404 response, bypassing error handlers.
class IndexController
  include Grip::Controllers::HTTP

  # Handles GET requests by manually setting a 404 response.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a 404 status and HTML response.
  def get(context : HTTP::Server::Context) : HTTP::Server::Context
    context
      .put_status(404) # Manually sets HTTP status to 404 (Not Found).
      .html("<p>404 Not found</p>") # Returns a simple HTML response.
      .halt # Stops further processing, bypassing error handlers.
  end
end

# Demonstrates raising a Grip-specific NotFound exception.
class IndexController
  include Grip::Controllers::HTTP

  # Handles GET requests by raising a NotFound exception.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] Never returns normally due to raised exception.
  def get(context : HTTP::Server::Context) : HTTP::Server::Context
    raise Grip::Exceptions::NotFound.new # Triggers the defined error handler for NotFound.
  end
end

# FallbackController handles arbitrary exceptions with a generic JSON response.
class FallbackController
  include Grip::Controllers::Exception

  # Generic handler for uncaught exceptions, returning a JSON error message.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a JSON error response.
  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    context.json({"error" => "An error occurred, please try again later."})
  end
end

# Application configures the Grip web application with a fallback error handler.
class Application
  include Grip::Application

  # Defines the array of HTTP handlers for processing requests.
  property handlers : Array(HTTP::Handler) = [
    Grip::Handlers::Exception.new, # Handles exceptions using defined error controllers.
  ] of HTTP::Handler

  # Initializes the application and sets up routes.
  def initialize
    routes # Calls the routes method to configure routing and error handling.
  end

  # Configures routes and error handlers for the application.
  def routes
    # Maps NotImplementedError to FallbackController for generic error handling.
    exception NotImplementedError, FallbackController
  end
end

# Instantiates and starts the Grip application.
app = Application.new
app.run
```