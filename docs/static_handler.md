This handler lets you serve static files using Grip

It has several arguments.
- `public_dir`: Directory that should be served
- `fallthrough`: Forwards the execution to the `call_next()` function of the HTTP::Handler
- `directory_listing`: Lists contents of the static directory
```cr
class Application < Grip::Application
  def custom : Array(HTTP::Handler)
    [
      HTTP::StaticFileHandler.new(public_dir: "./static/", fallthrough: true, directory_listing: false),
    ] of HTTP::Handler
  end
end
```
