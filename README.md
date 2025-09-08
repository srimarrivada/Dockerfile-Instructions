# Dockerfile INSTRUCTIONS:
Docker needs `Dockerfile` to create an image. A `Dockerfile` is a text file that contains various instructions _(or commands that can be called on the command-line as well)_ to build a Docker image. Docker reads instructions one by one in the Dockerfile and executes them to create an image. Each instruction in the Dockerfile creates a new layer on top of previous one, stacking together to form the final image which can be pushed to a Docker registry and shared with others.

`Dockerfile` ensures that applications and dependencies are packaged and deployed consistently across different environments, without worrying about the underlying infrastructure.

Below are some Dockerfile instructions that are commonly used:
* `FROM` - It defines the base image that provides OS and runtime environment. This instruction gets executed first before any other instructions and so, should be placed at the first line in the Dockerfile. A Dockerfile can have multiple `FROM` instructions to create a builder stage that contains all build-time dependencies, and then copy only the necessary final artifacts into a much smaller, final image.
  ```
  FROM <image_name>
  ```

* `LABEL` - It defines the metadata in the form of key-value pairs for the Docker image, such as name, version, description, etc. It is always good to use few `LABEL` instructions wherever needed since labels are helpful for organizing and managing images, automating processes, and providing governance and ownership information. All labels can be inspected using the `docker inspect` command.
  ```
  LABEL <key1>="<value1>" <key2>="<value2>"
  ```
  or
  ```
  LABEL <key1>="<value1>" \
        <key2>="<value2>"
  ```

* `MAINTAINER` - This is a non-executable instruction and is generally used to specify the author of the Dockerfile. This instruction has been **deprecated** in favor of the `LABEL` instruction because labels offer a more flexible and standardized way to add metadata to images but the `MAINTAINER` instruction only supported a single, unstructured string for the author's details and could not be used to add other types of metadata, like version numbers, documentation URLs, or licensing information.
  ```
  MAINTAINER <author_name> <email> <other_details>
  ```

* `WORKDIR` - It sets the working directory inside the container for any subsequent `Dockerfile` instructions. If the working directory does not exist already, this instruction creates the directory automatically.
  ```
  WORKDIR <directory_path>
  ```
  
* `ENV` - It sets environment variables in the Docker file while building the image and such variables are persisted when a container is running. This instruction can be used within `Dockerfile` and also can be used by any scripts that `Dockerfile` calls.
  ```
  ENV <variable>=<value>
  ```
  or
  ```
  ENV <variable> <value>
  ```

* `COPY` - It copies files and/or directories from the host machine into the container image at specified path while building the image.
  ```
  COPY <source> <destination>
  ```

* `ADD` - It works similar to `COPY` instruction which copies files and directories from host to Docker image but it also has additional features of automatically extracting compressed archives (like `.tar.gz`) and fetching files from remote URLs to the file system of the image. Docker recommends to use `COPY` for local files and only use `ADD` in case of specific functionality, like automatic archive extraction.
  ```
  ADD <url>
  ```

* `RUN` - It executes scripts and commands on top of the existing image layer and create a new layer with the result of command execution. It basically tells which process will be running inside the container at the run time.
  ```
  RUN <command> <arguments>
  ```
  or
  ```
  RUN ["<command>", "<arguments>"]
  ```

* `CMD` – It sets the default command that runs when a container starts from the image. This instruction can be overridden  by specifying new arguments in the `docker run` command.and is mostly used to launch the software required in the container. There can only be one `CMD` instruction in a Dockerfile and when there are more than one `CMD`, the last `CMD` only will take effect.
  ```
  CMD ["<command>", "<arguments>"]
  ```
  or
  ```
  CMD command arguments
  ```
  
* `ENTRYPOINT` – It defines a fixed command or script that is always executed as soon as the container starts. This instruction cannot be overridden and is typically combined with `CMD` instruction to provide a fixed executable with flexible, user-overridable arguments.
  ```
  ENTRYPOINT [<command>, <arguments>]
  ```

  **Note:** Both `CMD` and `ENTRYPOINT` instructions define which command is executed but there are some ground rules to be followed while using these two instructions:
  * At least one of the `CMD` or `ENTRYPOINT` commands should be specified in the Dockerfile.
  * When using the container as an executable, `ENTRYPOINT` must be defined.
  * `CMD` should be used to specify default arguments for an `ENTRYPOINT` command or to run an adhoc command in a container.
  * When running the container with alternative arguments, `CMD` will be overridden.

* `EXPOSE` - It informs Docker that container is configured to listen on the specified network ports over TCP (default) or UDP protocol as required by application servers at runtime. This instruction is for informational purposes only but to acutally publish a port to the outside of container when running, use the `-p` or `--publish` flag with `docker run` command.
  ```
  EXPOSE <port_number>
  ```
  or
  ```
  EXPOSE <port_number>/<protocol>
  ```

* `VOLUME` - It creates a mount point with a given name volume to hold externally mounted volumes from the host or other container. It is used to persist data or sharing volume with other containers. It also defines a bind volume by mapping host path to the container path.
  ```
  VOLUME [<new_volume_path>]
  ```
  or
  ```
  VOLUME [<host_path>] [<container_path>]
  ```

* `ARG` - It define variables that can be used at build time using the `--build-arg` flag on the `docker build` command. This instruction can be specified before or after the `FROM` instruction, but the placement affects the scope and availability of the variable. Multiple `ARG` instructions are supported. Unlike `ENV` instruction, `ARG` variables are not available in the final container image unless explicitly set with an `ENV` instruction.
  ```
  ARG <variable>=<value>
  ```
  
* `USER` - It sets the user name (or UID) and optionally the group (or GID) to be used by the container when running the image. This is helpful when shared network directories are involved which need a fixed username or user ID but in general, this is recommended to use to prevent running container processes as the privileged `root` user.
  ```
  USER <user_name or user_id>
  ```

* `SHELL` – It overrides the default shell used to execute subsequent shell form commands like `RUN`, `CMD`, and `ENTRYPOINT`. On Linux, the default shell is `["/bin/sh", "-c"]`, and on Windows, the default shell is `["cmd", "/S", "/C"]`. This instruction is particularly useful when a specific shell or shell features are needed that are not available in the default shell. There can be multiple `SHELL` instructions in a `Dockerfile` but each `SHELL` instruction overrides all previous `SHELL` instructions affecting on all subsequent instructions.
  ```
  SHELL ["<executable>", "<parameters>"]
  ```

* `HEALTHCHECK` – It instructs Docker how to test a container to check periodically if that container is still working correctly. It can detect cases such as a web server that is stuck in an infinite loop and unable to handle new connections, despite the server process still being active, etc. When a container is marked for health check, it becomes either healthy when a health check passes or unhealthy after a certain number of consecutive failures. Only one `HEALTHCHECK` instruction is allowed in a Dockerfile but subsequent `HEALTHCHECK` instructions will override the previous ones if multiple are used.
  ```
  HEALTHCHECK <options> CMD <command>
  ```
  Various `HEALTHCHECK` options include:
  * `--interval` (default: 30s)
  * `--timeout` (default: 30s)
  * `--start-period` (default: 0s)
  * `--start-interval` (default: 5s)
  * `--retries` (default: 3)
  
  When a container with a `HEALTHCHECK` instruction starts:
  * It has an initial status `starting`.
  * Then Docker runs the specified `command` at the given `interval`.
  * If the `command` exits with `0`, the container's status becomes healthy or remains healthy.
  * If the `command` runs for more than specified `timeout` seconds and exits with a non-zero status, the failure counter increments.
  * When the failure counter reaches the specified `retries` value, the container's status becomes unhealthy.
  * If a health check passes after a failure, the failure counter is reset to `0`.

<br/>

Below is an example `Dockerfile` that builds a **Node.js** web application using a wide range of instructions to create a secure, lightweight, and well-documented image.
```
# Define a build-time argument for the base image version
ARG NODE_VERSION=20-alpine

# Use an official Node.js runtime as the base image for the entire process
FROM node:${NODE_VERSION} AS builder

# Add descriptive labels for metadata and organization
LABEL org.opencontainers.image.title="Single-Stage Node.js App" \
      org.opencontainers.image.description="A comprehensive example of a Dockerfile using all instructions." \
      org.opencontainers.image.authors="ACME Inc." \
      org.opencontainers.image.licenses="MIT" \
	  version="1.0.0"

# Set a build-time argument that can be passed during the build command
ARG APP_PORT=3000

# Set environment variables available during build and runtime
ENV NODE_ENV=development \
    PORT=${APP_PORT}

# Set the working directory inside the container for all subsequent instructions
WORKDIR /app

# Use the ADD instruction to get a remote file and extract it (for demonstration) and place it in the container
ADD https://example.com/latest_version.tar.gz ./dependencies/

# Copy package files first to leverage build caching
COPY package*.json ./

# Install application dependencies
RUN npm install

# Copy the rest of the application source code into the image
COPY . .

# Explicitly use the bash shell for the command
SHELL ["/bin/bash", "-c"]

# Define a mount point for a volume where the application could write logs
VOLUME /var/log/app

# Set the user to a non-root user for security
RUN adduser -D myuser
USER myuser

# Expose a port (for documentation, requires runtime flag to be published)
EXPOSE ${PORT}

# Define a health check to monitor if the application is running correctly
HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD curl -f http://localhost:${PORT} || exit 1

# Define the entrypoint for the container's primary executable, making the
# image behave like a command-line tool.
ENTRYPOINT ["npm"]

# Provide default arguments for the entrypoint
CMD ["start"]

```
