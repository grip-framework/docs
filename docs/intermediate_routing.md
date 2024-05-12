The routing mechanism is based on `Kemal` which uses the radix tree implementation to identify the URL patterns.

```ruby
class DemoController < Grip::Controllers::Http
  def index(context : Context) : Context
    context
      .json(nil)
  end

  def get_name(context : Context) : Context
    params =
      context
        .fetch_path_params

    context
      .json(params) # => {"name" => "John"} for '/John', {"name" => "Jane"} for '/Jane'
  end
end

class Application < Grip::Application
  def initialize
    super(environment: "development")

    # The routing occurs via the `get` macro which instantiates the controller class and assigns a route
    # to the routing mechanism, the `as` keyword creates a Proc(Context, Context) and wraps it around
    # the index/1 function of the DemoController.
    get "/", DemoController, as: :index
    get "/:name", DemoController, as: :get_name
  end
end
```
