This handler lets you serve static files using Grip

It has several arguments.
- `public_dir`: Directory that should be served
- `fallthrough`: Forwards the execution to the `call_next()` function of the HTTP::Handler
- `directory_listing`: Lists contents of the static directory
```ruby
class Application < Grip::Application
  def initialize
    super(environment: "development", serve_static: true)

    # Static file directory.
    def pubilc_dir : String
      "./public"
    end

    # Ignore non-existing files and move to the next handler.
    def fallthrough : Bool
      false
    end

    # Enable/Disable directory listing.
    def directory_listing : Bool
      false
    end
  end
end
```
