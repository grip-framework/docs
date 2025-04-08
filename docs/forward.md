Forward forwards a request to a specific route, mainly a HTTP::Handler class.

```ruby
class Authorization
  include HTTP::Handler

  def initialize(@username : String, @password : String); end

  def call(context : HTTP::Server::Context) : HTTP::Server::Context
    context
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

    # Forward macro simply routes the matched requests to a certain Base controller
    # which contains a single call/1 function.
    forward "/", Authorization, username: "admin", password: "admin"
  end
end
```



