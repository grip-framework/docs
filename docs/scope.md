Scope defines a prefix for routes which will be nested in a block.

```ruby
class DemoController < Grip::Controllers::Http
  def get(context : Context) : Context
    context
      .json(nil)
  end
end

class Application < Grip::Application
  def initialize
    super(
      environment: ENV["ENVIRONMENT"]? || "production"
      handlers: [
        Grip::Handlers::HTTP.new,
      ] of HTTP::Handler
    )

    routes()
  end

  def routes()
    # The route gets built from the ground starting from the lowest GET /, to the top /api/v1.
    scope "/api/v1" do
      get "/", DemoController

      scope "/users" do
        get "/", DemoController
      end
    end
  end
end
```
