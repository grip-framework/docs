Pipe middleware is the building block of the framework.

Creating a custom pipe is as easy as creating an `HTTP handler`:

```ruby
class DemoPipe
  include HTTP::Handler

  # Keep in mind calling the call_next() function is prohibited here.
  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    # Mutate the context and pass it on to the next handler.
  end
end

class DemoController < Grip::Controllers::Http
  def get(context : Context) : Context
    context
      .json("Hello, World!")
  end
end

class Application < Grip::Application
  def initialize
    super(environment: "development", serve_static: false)

    # Creating a pipeline with a single pipe to be routed through.
    pipeline :web, [
      DemoPipe.new
    ]

    pipeline :api, [
      DemoPipe.new
    ]

    scope "/" do
      pipe_through :web

      get "/", DemoController

      scope "/users" do
        # This will go through [:web, :api].
        pipe_through :api

        get "/", DemoController
      end
    end
  end
end
```

Advantage of the pipe over a raw middleware is that you can controll which routes go through the middleware and which don't.
