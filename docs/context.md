The `Context` class in the `Grip` module provides a set of helper methods to simplify HTTP request and response handling in a Crystal-based HTTP server. These methods enable fluent manipulation of headers, cookies, status codes, and response content, with most returning `self` for method chaining.

## Available Methods

- [`exec/0`](#exec)
- [`delete_req_header/1`](#delete_req_header)
- [`delete_resp_header/1`](#delete_resp_header)
- [`get_req_header/1`](#get_req_header)
- [`get_req_header?/1`](#get_req_header-1)
- [`get_req_cookie/1`](#get_req_cookie)
- [`get_resp_header/1`](#get_resp_header)
- [`halt/0`](#halt)
- [`merge_resp_headers/1`](#merge_resp_headers)
- [`put_req_header/2`](#put_req_header)
- [`put_resp_header/2`](#put_resp_header)
- [`put_resp_cookie/2`](#put_resp_cookie)
- [`put_status/1`](#put_status)
- [`send_file/1`](#send_file)
- [`send_resp/1`](#send_resp)
- [`json/2`](#json)
- [`html/2`](#html)
- [`text/2`](#text)
- [`binary/2`](#binary)
- [`fetch_json_params/0`](#fetch_json_params)
- [`fetch_query_params/0`](#fetch_query_params)
- [`fetch_body_params/0`](#fetch_body_params)
- [`fetch_file_params/0`](#fetch_file_params)
- [`fetch_path_params/0`](#fetch_path_params)
- [`redirect/2`](#redirect)

### exec

Run a block in the context scope, allowing chained operations.

```crystal
def get(context : Context)
  context.exec do
    put_status(201) # Put a response status code.
    put_resp_header("Server", "TornadoServer/6.0.4") # Put a response header.
    json( # Respond with JSON encoded data.
      {
        "id" => 1
      }
    )
  end
end
```

### delete_req_header

Deletes a request header by key if present. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .delete_req_header("Referer")
    .text(nil)
end
```

### delete_resp_header

Deletes a response header by key if present. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .json("Hello, World!")
    .delete_resp_header("Content-Type")
end
```

### get_req_header

Returns the value of the request header specified by `key`. Raises `KeyError` if not found.

```crystal
def get(context : Context) : Context
  referer =
    context
      .get_req_header("Referer")

  context
    .json(
      {
        "referer" => referer
      }
    )
end
```

### get_req_header?

Returns the value of the request header specified by `key`, or `nil` if not found.

```crystal
def get(context : Context) : Context
  referer =
    context
      .get_req_header?("Referer")

  context
    .json(
      {
        "referer" => referer || "No Referer"
      }
    )
end
```

### get_req_cookie

Returns the cookie from the request specified by `key`, or `nil` if not found.

```crystal
def get(context : Context) : Context
  cookie =
    context
      .get_req_cookie("MyCookie")

  context
    .json(
      {
        "cookie" => cookie.try(&.value) || "No Cookie"
      }
    )
end
```

### get_resp_header

Returns the value of the response header specified by `key`. Raises `KeyError` if not found.

```crystal
def get(context : Context) : Context
  content_type =
    context
      .get_resp_header("Content-Type")

  context
    .json(
      {
        "contentType" => content_type
      }
    )
end
```

### halt

Halts the function chain by closing the response stream. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .json("Hello, World!")
    .halt
end
```

### merge_resp_headers

Merges a series of response headers into the context. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .merge_resp_headers({"Content-Type" => "application/json"})
    .send_resp("Hello, World!")
end
```

### put_req_header

Adds or replaces a request header with the given `key` and `value`. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .put_req_header("Referer", "www.google.com")
    .json("Hello, World")
end
```

### put_resp_header

Adds or replaces a response header with the given `key` and `value`. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .put_resp_header("Content-Type", "application/json")
    .send_resp("Hello, World!")
end
```

### put_resp_cookie

Adds or overwrites a response cookie. Accepts either a key-value pair or an `HTTP::Cookie` object. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .put_resp_cookie("MyCookie", "Cookie Value") # Or .put_resp_cookie(HTTP::Cookie.new("MyCookie", "Cookie Value"))
    .send_resp("Hello, World!")
end
```

### put_status

Assigns the given status code to the context response. Accepts either an `HTTP::Status` or an `Int32`. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .put_status(400)
    .json("Bad request")
end
```

### send_file

Sends a file to the client. The `path` must point to an existing file. Optionally specify `mime_type` (inferred from extension if `nil`) and enable `gzip_enabled` for compression. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .send_file("./example.txt")
end
```

### send_resp

Sends a response to the client with the given content. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .send_resp("Hello, World!")
end
```

### json

Sends a JSON response. Optionally specify the `Content-Type` header. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .json("Hello, World!", "application/json; charset=UTF-8")
end
```

### html

Sends an HTML response. Optionally specify the `Content-Type` header. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .html("Hello, World!")
end
```

### text

Sends a text response. Optionally specify the `Content-Type` header. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .text("Hello, World!")
end
```

### binary

Sends a binary response. Accepts a string or `Bytes`. Optionally specify the `Content-Type` header. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .binary("Hello, World!")
end
```

### fetch_json_params

Fetches JSON parameters from the request body. Returns an empty hash if no parameters are available.

```crystal
def get(context : Context) : Context
  params =
    context
      .fetch_json_params

  context
    .json(params)
end
```

### fetch_query_params

Fetches query parameters from the URL query string. Returns an empty `URI::Params` if no parameters are available.

```crystal
def get(context : Context) : Context
  params =
    context
      .fetch_query_params

  context
    .json(params)
end
```

### fetch_body_params

Fetches URL-encoded body parameters from the request. Returns an empty `URI::Params` if no parameters are available.

```crystal
def get(context : Context) : Context
  params =
    context
      .fetch_body_params

  context
    .json(params)
end
```

### fetch_file_params

Fetches multipart file parameters from the request. Returns an empty hash if no parameters are available.

```crystal
def get(context : Context) : Context
  params =
    context
      .fetch_file_params

  context
    .json(params)
end
```

### fetch_path_params

Fetches path parameters from the URL. Returns an empty hash if no parameters are available.

```crystal
def get(context : Context) : Context
  params =
    context
      .fetch_path_params

  context
    .json(params)
end
```

### redirect

Redirects the response to the specified `url` with a given status code (default `302 Found`). Accepts either an `HTTP::Status` or an `Int32`. Returns `self` for method chaining.

```crystal
def get(context : Context) : Context
  context
    .redirect("/new-page", 302)
end
```