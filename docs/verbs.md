There are several HTTP verbs supported by the router:

```ruby
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
    get "/", DemoController # Routes all the `GET /` requests to DemoController.get/1
    post "/", DemoController # Routes all the `POST /` requests to DemoController.post/1
    put "/", DemoController # Routes all the `PUT /` requests to DemoController.put/1
    delete "/", DemoController # Routes all the `DELETE /` requests to DemoController.delete/1
    patch "/", DemoController # Routes all the `PATCH /` requests to DemoController.patch/1
    head "/", DemoController # Routes all the `HEAD /` requests to DemoController.head/1
    options "/", DemoController # Routes all the `OPTIONS /` requests to DemoController.options/1
  end
end
```
