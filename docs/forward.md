Forward forwards a request to a specific route, mainly a HTTP::Handler class.

```crystal
# Authorization is an HTTP handler that processes incoming requests.
# It accepts a username and password for potential authentication logic.
class Authorization
  include HTTP::Handler

  # Initializes the handler with a username and password.
  # @param username [String] The username for authentication.
  # @param password [String] The password for authentication.
  def initialize(@username : String, @password : String)
  end

  # Processes the HTTP request context, currently returning it unchanged.
  # This method can be extended to add authentication logic using @username and @password.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The unmodified context (placeholder for future logic).
  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    context # Returns the context as-is; can be modified to add headers or logic.
  end
end

# Application configures the Grip web application with handlers and routes.
class Application < Grip::Application
  # Defines the array of HTTP handlers for processing requests.
  property handlers : Array(HTTP::Handler) = [
    Grip::Handlers::HTTP.new, # Handles core HTTP protocol logic.
  ] of HTTP::Handler

  # Initializes the application and sets up routes.
  def initialize
    routes # Calls the routes method to configure routing and forwarding.
  end

  # Configures routes for the application using the forward macro.
  def routes
    # Forwards all requests to the root path ("/") to the Authorization handler.
    # The forward macro routes matched requests to a handler's call/1 method.
    # @param path [String] The root path "/" to match requests.
    # @param handler [Authorization] The handler class to process requests.
    # @param username [String] The username passed to the Authorization handler.
    # @param password [String] The password passed to the Authorization handler.
    forward "/", Authorization, username: "admin", password: "admin"
  end
end

# Instantiates and starts the Grip application.
app = Application.new
app.run
```



