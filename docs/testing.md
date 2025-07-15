## 1. Add Spectator to `shard.yml`
```yaml
# shard.yml: Configuration file for project dependencies and metadata.
dependencies:
  grip: # Grip microframework for building RESTful APIs.
    github: grip-framework/grip
  spectator: # Spectator testing framework for writing tests.
    github: grip-framework/spectator
```

## 2. Install Dependencies
```bash
# Installs dependencies specified in shard.yml to the lib/ directory.
shards install
```

## 3. Configure Test Environment
```crystal
# spec/spec_helper.cr: Sets up the test environment for the Grip application.
require "../src/your-grip-app" # Requires the main application code.
require "spectator" # Requires the Spectator testing framework.
```

## 4. Grip Application Code
```crystal
# src/your-grip-app.cr: Defines a simple Grip application with a single route.
require "grip"

# Index controller handles HTTP GET requests for the root path.
class Index
  include Grip::Controllers::HTTP

  # Responds to GET requests with a text response.
  # @param context [HTTP::Server::Context] The HTTP request context.
  # @return [HTTP::Server::Context] The context with a text response.
  def get(context : Context) : Context
    context
      .text("Hello, World!") # Returns a plain text response.
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

  # Defines routes for the application.
  def routes
    # Maps GET "/" to Index controller’s get method.
    get "/", Index
  end
end
```

## 5. Write Tests
```crystal
# spec/your-grip-app-spec.cr: Tests for the Grip application’s Index controller.
describe Index do
  # Tests the GET "/" route.
  it "renders /" do
    response = get Application.new, "/" # Sends a GET request to the root path.
    response.status_code.should eq 200 # Verifies the response status is 200 (OK).
  end
end
```

## 6. Run Tests
```bash
# Runs the test suite defined in spec/ with the test environment.
ENVIRONMENT=TEST crystal spec --verbose
```