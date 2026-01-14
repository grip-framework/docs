This guide provides a complete setup for a Crystal web application using the Grip framework, including project initialization, dependency setup, application code, server execution, testing, and auto-restart configuration.

## 1. Initialize a Crystal Project

```shell
# Initializes a new Crystal application named "demo" and navigates to its directory.
crystal init app demo && cd demo
```

## 2. Add Dependency to `shard.yml` and Install

```yaml
# shard.yml: Specifies project dependencies for the Crystal application.
name: demo
version: 0.1.0

authors:
  - Your Name <your.email@example.com>

targets:
  demo:
    main: src/demo.cr

crystal: 1.0.0

dependencies:
  grip: # Adds the Grip web framework as a dependency.
    github: grip-framework/grip
```

```shell
# Installs the dependencies listed in shard.yml
shards install
```

## 3. Write Application Code in `src/demo.cr`
```crystal
# src/demo.cr: Defines the main application logic using the Grip framework.

# Imports the Grip framework for building the web application.
require "grip"

# DemoController handles HTTP GET requests for the root path.
class DemoController
  include Grip::Controllers::HTTP

  # Handles GET requests, returning a JSON response with a custom status and header.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a 201 status, custom header, and JSON response.
  def get(context : Context)
    context
      .put_status(201) # Sets the HTTP response status to 201 (Created).
      .put_resp_header("Server", "TornadoServer/6.0.4") # Adds a custom Server header.
      .json( # Returns a JSON response with a simple key-value pair.
        {
          "id" => 1
        }
      )
  end
end

# Application configures the Grip web application with handlers and routes.
class Application
  include Grip::Application

  # Defines the array of HTTP handlers for processing requests.
  property handlers : Array(HTTP::Handler) = [
    Grip::Handlers::HTTP.new, # Handles core HTTP protocol logic.
  ] of HTTP::Handler

  # Initializes the application and sets up routes.
  def initialize
    routes # Calls the routes method to configure routing.
  end

  # Configures routes for the application.
  def routes
    # Maps GET requests to the root path ("/") to DemoController's get method.
    # The get method is indirectly called via a Proc, as Grip wraps controller methods.
    get "/", DemoController
  end
end

# Instantiates and starts the Grip application.
app = Application.new
app.run
```

## 4. Run the Server

```shell
# Compiles and runs the Crystal application, starting the Grip server.
# The server listens on 0.0.0.0:4004 by default unless configured otherwise.
crystal src/demo.cr
```

## 5. Send a Request

```shell
# Sends an HTTP GET request to the root path of the running server.
# Expects a 201 response with a JSON body {"id": 1} and a Server header.
curl "http://0.0.0.0:4004/"
```

## 6. Auto-Restart Server

```shell
# Installs nodemon globally to watch for file changes and auto-restart the server.
sudo npm install -g nodemon

# Runs nodemon to monitor .cr and .yml files, automatically restarting the server
# by executing "crystal run src/demo.cr" when changes are detected.
nodemon -e "cr,yml" --exec "crystal run" src/demo.cr
```
