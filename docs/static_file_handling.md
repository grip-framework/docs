This handler lets you serve static files using Grip

```ruby
class Application < Grip::Application
  def initialize
    super(environment: "development")

    # This DSL macro will add a static route on / to a "public" directory,
    # it will allow it to fall through to other handlers and it disables the directory listing.
    static "/", "./public", fallthrough: true, directory_listing: false

    # By default fallthrough and directory_listing are false.
    static "/content", "./content"
  end
end
```
