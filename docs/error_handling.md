Grip comes with a pre-defined error handlers for the JSON response type. You can customize the built-in error pages or even add your own using the `error` macro.

```ruby
class IndexController < Grip::Controllers::Http
  def get(context : Context) : Context
    raise Grip::Exceptions::Forbidden.new
  end
end

class ForbiddenController < Grip::Controllers::Exception
  def call(context : Context) : Context
    context
      .put_status(403) # Raised exception automatically carries over the status code if inherited from the base class.
      .json(
        {
          "error" => ["You lack privileges to access the current resource!"]
        }
      )
  end
end

class Application < Grip::Application
  def initialize
    super(environment: "development")

    exception Grip::Exceptions::Forbidden, ForbiddenController
  end
end
```

### Difference between native and inherited exceptions

Keep in mind that if you won't use one of the classes from the `Exceptions` module this will just return as a normal response:

```ruby
class IndexController < Grip::Controllers::Http
  def get(context : Context) : Context
    context
      .put_status(404)
      .html("<p>404 Not found</p>")
      .halt
  end
end
```

and this will trigger an error handler defined in the routes method of the application:

```ruby
class IndexController < Grip::Controllers::Http
  def get(context : Context) : Context
    raise Grip::Exceptions::NotFound.new
  end
end
```

You can also raise any exception you want and handle it in the error handler like this:

```ruby
class FallbackController < Grip::Controllers::Exception
  def call(context : Context) : Context
    context.json({"error" => "An error occured, please try again later."})
  end
end

class Application < Grip::Application
  def initialize
    super(environment: "development")

    exception NotImplementedError, FallbackController
  end
end
```
