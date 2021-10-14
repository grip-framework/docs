Swagger is in essence an Interface Description Language for describing RESTful APIs expressed using JSON. Swagger is used together with a set of open-source software tools to design, build, document, and use RESTful web services.

The class with documentation:

```ruby
alias Document = Grip::Annotations

@[Document::Controller(description: "An example HTTP controller")]
class HttpController < Grip::Controllers::Http
  # See these documents for more information:
  #   https://github.com/grip-framework/grip/blob/master/src/grip/dsl/macros.cr
  #   https://github.com/icyleaf/swagger/
  @[Document::Route(
    description: "This is a description.",
    summary: "This route returns a response.",
    parameters: [] of Swagger::Parameter, # https://github.com/icyleaf/swagger/blob/master/src/swagger/parameter.cr
    responses: [] of Swagger::Response, # https://github.com/icyleaf/swagger/blob/master/src/swagger/response.cr
    request: nil, # https://github.com/icyleaf/swagger/blob/master/src/swagger/request.cr
    authorization: false,
    deprecated: false
  )]
  def get(context : Context) : Context
    context
      .halt
  end
end
```

The application which instructs the `swagger` macro to document the class:

```ruby
class Application < Grip::Application
  def routes

    scope "/api" do
      forward "/swagger/*",
        Grip::Controllers::Swagger,
        document: document(),
        base_path: "/api/swagger"

      get "/", HttpController
    end
  end
end
```
