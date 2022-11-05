1.Add spectator to your shard.yml file as a dependencie.

```yaml
dependencies:
  grip:
    github: grip-framework/grip

  spectator:
    github: grip-framework/spectator
```

2.Then run shards to get the dependencies:

```bash
$ shards install
```

3.Now you should require it before your files in your spec/spec_helper.cr

```
require "../src/your-grip-app"
require "spectator"
```

For example, your Grip application:

```crystal
# src/your-grip-app.cr

require "grip"

class Index < Grip::Controllers::Http
  def get(context : Context) : Context
    context
      .text("Hello, World!")
  end
end

class Application < Grip::Application
  def initialize
    super(environment: "development", serve_static: false)

    get "/", Index
  end
end
```

4.Now you can easily test your Grip application in your specs.

```bash
crystal spec -Dtest
```

```crystal
# spec/your-grip-app-spec.cr

describe Index do
  # You can use GET, POST, PUT, PATCH, DELETE to call the corresponding route.
  it "renders /" do
    response = get Application.new, "/"
    response.status_code.should eq 200
  end
end
```
