In Grip raw middlewares are mentioned as handlers, when creating a handler you include the `HTTP::Handler` module and define a call/1 function.

```ruby
class DemoHandler
  include HTTP::Handler

  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    # Mutate the context and pass it on to the next handler.
    call_next(context)
  end
end

class Application < Grip::Application
  def initialize
    super(
      environment: ENV["ENVIRONMENT"]? || "production"
      handlers: [
        DemoHandler.new,
        Grip::Handlers::HTTP.new,
      ] of HTTP::Handler
    )
  end
end
```
