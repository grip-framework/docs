To use the Grip framework in a Crystal application, you must add it as a dependency in the project’s `shard.yml` file and install it using the `shards` command. This ensures the framework is available for building RESTful web applications.

## 1. Update `shard.yml`

Add the Grip dependency to your project’s `shard.yml` file, located in the project root directory. This file specifies the project’s metadata and dependencies.

```yaml
# shard.yml: Configuration file for project dependencies and metadata.
dependencies:
  grip:
    github: grip-framework/grip # References the Grip repository on GitHub.
```

## 2. Install Dependencies

Run the following command in your terminal:

```bash
# Installs dependencies specified in shard.yml, downloading Grip to the lib/ directory.
shards install
```
