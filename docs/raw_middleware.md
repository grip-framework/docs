In Grip raw middlewares are mentioned as handlers, when creating a handler you include the `HTTP::Handler` module and define a call/1 function.

```ruby
class DemoHandler
  include HTTP::Handler

  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    # Mutate the context and pass it on to the next handler.
  end
end

class Application < Grip::Application
  def initialize
    super(environment: "development", serve_static: false)

    # By default the router has 4 entries, if you insert a handler
    # before the exception handler any exception that might occur in
    # your newly inserted handler will not be handled and might crash the application.
    #
    # [
    #   exception_handler,
    #   pipeline_handler,
    #   websocket_handler,
    #   http_handler,
    # ]
    router.insert(1, DemoHandler.new)
  end
end
```
