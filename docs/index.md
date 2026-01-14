Grip is a lightweight microframework for building RESTful web applications in the Crystal programming language. Designed for modularity, simplicity, and high performance, Grip leverages Crystal’s capabilities to create scalable APIs. It provides extensible middleware, a flexible router, and exceptional throughput, achieving up to [1,663,946 requests/second](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=json&l=zdk8an-1r) for plain text responses.

## Framework Features

- **Microframework Design**: Grip focuses on simplicity for building RESTful APIs, enabling rapid development while supporting complex applications through modular extensions.

- **Middleware ("Pipes")**: Integrated middleware, implemented as `HTTP::Handler` classes, processes and modifies request/response contexts (e.g., adding headers, authentication) before forwarding to endpoints.

- **Router**: Inspired by the [Phoenix framework](https://github.com/phoenixframework/phoenix), Grip’s router offers expressive routing with macros like `get`, `scope`, `pipe_through`, and `forward` for defining endpoints and pipelines.

- **Extensibility**: Developers can create custom middleware, controllers, and error handlers to tailor functionality to specific needs.

- **High Performance**: Optimized for speed, Grip delivers exceptional throughput, making it ideal for high-performance API development.

## Recommended Project Structure

A typical Grip application follows an organized structure to separate business logic, web interactions, and dependencies. Below is the recommended structure for a project named `Echo`:

```
Echo/                       # Project root directory
├── lib/                    # External dependencies installed via shards.yml (e.g., Grip framework)
├── src/                    # Application source code
│   ├── application.cr      # Main entry file defining the Application class and routes
│   ├── echo/               # Business logic layer (e.g., database models, services)
│   │   └── ...             # Additional files for models, repositories, or domain logic
│   ├── echo_web/           # Web layer for HTTP request/response handling
│   │   ├── controllers/    # Controllers for processing HTTP requests
│   │   │   └── ...         # Controller files (e.g., DemoController)
│   │   ├── views/          # Templates for rendering responses (e.g., JSON, HTML)
│   │   │   └── ...         # View files
├── shards.yml              # Dependency configuration file
```

### Structure Details

- **lib/**: Contains external libraries (e.g., Grip) installed via `shards install`, managed by Crystal’s dependency manager.

- **src/application.cr**: The application entry point, defining the `Application` class (inheriting from `Grip::Application`). It configures middleware via a `property handlers` array and sets up routes using macros like `get` or `forward`.

- **src/echo/**: Houses business logic, including database models, services, or other domain-specific functionality, ensuring separation from web concerns.

- **src/echo_web/**: Manages HTTP interactions, with controllers (e.g., `DemoController`) handling requests and views defining response formats (e.g., JSON, HTML).

- **shards.yml**: Specifies project metadata and dependencies, such as `grip: { github: grip-framework/grip }`.

## Example Setup

To create a Grip application with the above structure:

1. Initialize a Crystal project: `crystal init app echo && cd echo`.

2. Add Grip to `shards.yml`:
   ```yaml
   dependencies:
     grip:
       github: grip-framework/grip
   ```

3. Install dependencies: `shards install`.

4. Define the application in `src/application.cr`, configuring handlers and routes (see previous examples for controller and route definitions).

5. Run the server: `crystal src/application.cr`.

This structure and Grip’s features provide a robust foundation for building scalable, high-performance RESTful applications in Crystal.