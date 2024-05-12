1.Init a Crystal project.

```shell
crystal init app demo && cd demo
```

2.Add the dependency to your shard.yml and run `shards install`.

```yaml
dependencies:
  grip:
    github: grip-framework/grip
```

3.Write down the following code in `src/demo.cr`.

```ruby
require "grip"

class DemoController < Grip::Controllers::Http
  def get(context : Context)
    context
      .put_status(201) # Put a response status code.
      .put_resp_header("Server", "TornadoServer/6.0.4") # Put a response header.
      .json( # Respond with JSON encoded data.
        {
          "id" => 1
        }
      )
  end
end

class Application < Grip::Application
  def initialize
    super(environment: "development")

    get "/", DemoController
  end
end

app = Application.new
app.run
```

4.Run server.

```shell
crystal src/demo.cr
```

5.Send request.

```shell
curl "http://0.0.0.0:5000/"
```

6.Auto restart server.

```shell
# Use nodemon to watch file changed and auto restart server.
sudo npm install -g nodemon
nodemon -e "cr,yml" --exec "crystal run" src/demo.cr
```
