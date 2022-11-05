Routing is the key feature which distinguishes Grip from other Crystal based web frameworks.

```ruby
class DemoController < Grip::Controllers::Http
  def get(context : Context) : Context
    context
      .json(nil)
  end
end

class Application < Grip::Application
  def initialize
    super(environment: "development", serve_static: false)

    # The routing occurs via the `get` macro which instantiates the controller class and assigns a route
    # to the routing mechanism.
    get "/", DemoController
  end
end
```
