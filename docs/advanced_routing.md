The more advanced the route is the more time it takes when executed, since the call to the index function is an indirect call, meaning that the `index/1` never existed in the `Grip::Controllers::Http` class it needs to be wrapped in a `Proc` which then needs to be called.

```ruby
class AuthorizationHandler
  include HTTP::Handler

  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    context
      .put_req_header("Authorization", "Bearer eyMANEmJeFF=")
  end
end

class DemoController < Grip::Controllers::Http
  def index(context : Context) : Context
    context
      .json(nil)
  end
end

class Application < Grip::Application
  def initialize
    super(environment: "development", serve_static: false)

    pipeline :api, [
      AuthorizationHandler.new
    ]

    pipeline :web, [
      HTTP::CompressHandler.new
    ]

    # The routing occurs via the `get` macro which instantiates the controller class and assigns a route
    # to the routing mechanism.
    #
    # You can route the request through unlimited amounts of pipelines.
    scope "/" do
      pipe_through :web
      pipe_through :api

      get "/", DemoController, as: :index
    end

    scope "/api" do
      pipe_through :api

      scope "/v1" do
        get "/", DemoController, as: :index
      end
    end
  end
end
```
