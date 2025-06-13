# Dockerfile INSTRUCTIONS:
Docker needs `Dockerfile` to create an image. A `Dockerfile` is a text file that contains various instructions _(or commands that can be called on the command-line as well)_ to build a Docker image. Each instruction in the Dockerfile creates a new layer 
in the image. Docker reads instructions one by one in the Dockerfile and executes them to create an image so that the resulting image can be pushed to a Docker registry and shared with others.

`Dockerfile` ensures that applications and dependencies are packaged and deployed consistently across different environments, without worrying about the underlying infrastructure.

Below are some Dockerfile instructions that are commonly used:
* `FROM` - This is used for to set the base image that provides OS and runtime environment. This instruction gets executed first before any other instructions and hence, it is very important to mention this 
  in the first line of docker file. A Dockerfile can have multiple `FROM` instructions to produce more than one image.
  ```
  FROM <image_name>
  ```
  **For example:**
  ```
  FROM ubuntu:19.04
  ```
* `MAINTAINER` - This is a non-executable instruction and is generally used to specify the author of the Dockerfile.
  ```
  MAINTAINER <author_name> <email> <other_details>
  ```
  **For example:**
  ```
  MAINTAINER Firstname Lastname example@google.com
  ```
* `LABEL` - This is used to provide metadata in the form of key-value pairs for the Docker image, such as name, version, description, etc. It is always good to use few `LABEL` instructions wherever needed as
  labels are helpful for organizing and managing images, automating processes, and providing governance and ownership information. All labels can be inspected using the `docker inspect` command.
  ```
  LABEL <key1>="<value1>" <key2>="<value2>"
  ```
  **For example:**
  ```
  LABEL name="example"
  LABEL email="user1@example.com"
  LABEL tutorial1="Docker" tutorial2="LABEL INSTRUCTION"
  ```
  
* `ENV` - This is used to set the environment variables in the Docker file while building the image which are persisted when a container is run. This instruction can be used within `Dockerfile` and also can be
  used by any scripts that `Dockerfile` calls.
  ```
  ENV <variable>=<value>
  ```
  or
  ```
  ENV <variable> <value>
  ```
  **For example:**
  ```
  ENV URL_POST=production.example-gfg.com
  ```
* `COPY` - This is used to copy the files and directories from the host machine into the container image while building the image.
  ```
  COPY <source> <destination>
  ```
  **For example:**
  ```
  COPY /target/java-web-app.war /usr/local/webapps/java-web-app.war
  ```
* `ADD` - This is similar to `COPY` instruction which is used to copy files, directories from host to Docker image but `ADD` instruction has additional features of auto-decompressing archives and fetching files
  from remote URLs to the file system of the image.
  ```
  ADD <url>
  ```
  **For example:**
  ```
  ADD run.sh /run.sh
  ADD project.tar.gz /install/
  ADD https://project.example.com/testingproject.rpm/test
  ```
* `RUN` - This allows to execute scripts and commands on top of the existing image or layer and create a new layer with the result of command execution. It basically tells which process will be running inside the
  container at the run time.
  ```
  RUN <command> <arguments>
  ```
  or
  ```
  RUN ["<command>", "<arguments>"]
  ```
  **For example:**
  ```
  RUN unzip install.zip /opt/install
  RUN echo hello
  ```
* `CMD` – This is used to start the process inside the container but does not perform anything during the building of docker image. This instruction can be overridden and is mostly used to launch the software
  required in the container. There can only be one `CMD` instruction in a Dockerfile and if there are more than one `CMD`, then only the last `CMD` will take effect.
  ```
  CMD ["<command>", "<arguments>"]
  ```
  or
  ```
  CMD command arguments
  ```
  **For example:**
  ```
  CMD ["program-foreground"]
  CMD ["executable", "program1", "program2"]
  ```
  
* `ENTRYPOINT` – This is used to execute a command or script as soon as the docker container is started but does not perform anything during the building of docker image. This instruction cannot be overridden.
  ```
  ENTRYPOINT [<command>, <arguments>]
  ```
  **For example:**
  ```
  ENTRYPOINT ["/start.sh"]
  ```

  **Note:** Both `CMD` and `ENTRYPOINT` instructions define which command is executed but there are some ground rules to be followed while using these two instructions:
  * At least one of the `CMD` or `ENTRYPOINT` commands should be specified in the Dockerfile.
  * When using the container as an executable, `ENTRYPOINT` must be defined.
  * `CMD` should be used to specify default arguments for an `ENTRYPOINT` command or to run an adhoc command in a container.
  * When running the container with alternative arguments, `CMD` will be overridden.

  
* `EXPOSE` - This is used by container to listen on a specific network port over TCP (default) or UDP protocol as required by application servers at runtime. It actually exposes the port of the application
  running inside the container to the outside of container.
  ```
  EXPOSE <port_number>
  EXPOSE <port_number>/<protocol>
  ```
  **For example:**
  ```
  EXPOSE 3030
  EXPOSE 80/udp
  ```
* `VOLUME` - This is used to either create a mount point and assign a given name volume to hold externally mounted volumes or define a shared volume by mapping host path to the container path depending on number
  of arguments provided.
  ```
  VOLUME [<new_volume_path>]
  ```
  or
  ```
  VOLUME [<host_path>] [<container_path>]
  ```
  **For example:**
  ```
  VOLUME ["/app/data"]
  VOLUME ["/tmp/data" "/app/data/"]
  ```
* `WORKDIR` - This is used to set the working directory for other `Dockerfile` instructions. If the working directory does not exist already, this instruction will create it automatically.
  ```
  WORKDIR <directory_path>
  ```
  **For example:**
  ```
  WORKDIR /app
  ```
* `USER` - This is used to set the user name (or UID) and optionally the group (or GID) to be used by the container when running the image. This is helpful when shared network directories are involved which
  need a fixed username or user ID.
  ```
  USER <user_name or user_id>
  ```
  **For example:**
  ```
  USER example
  USER 4000
  ```
* `ARG` - This is used to define a variable that can be used at build time using the `--build-arg` flag on the `docker build` command. This can be specified before `FROM` instruction. Multiple `ARG` instructions
  are supported. 
  ```
  ARG <variable>=<value>
  ```
  **For example:**
  ```
  ARG image_name=latest
  FROM centos:$image_name
  docker build -t <image-name>:<tag> --build-arg image_name=centos8 .
  ```
* `SHELL` – This instruction overrides the default shell used for the shell form of commands. On Linux, the default shell is `["/bin/sh", "-c"]`, and on Windows, it is `["cmd", "/S", "/C"]`. There can be multiple
  `SHELL` instructions but each `SHELL` instruction overrides all preceding `SHELL` instructions and has an impact on all subsequent instructions.
  ```
  SHELL ["<executable>", "<parameters>"]
  ```
  **For example:**
  ```
  SHELL ["powershell", "-command"]
  ```
* `HEALTHCHECK` – This instructs Docker on how to test a container to ensure that it is still operational. This can detect cases such as a web server that is stuck in an infinite loop and unable to handle new
  connections, despite the server process still being active, etc. When a container is marked for health check, it becomes either healthy when a health check passes or unhealthy after a certain number of consecutive
  failures. Only one `HEALTHCHECK` instruction is allowed in a Dockerfile but subsequent `HEALTHCHECK` instructions will override the previous ones.
  ```
  HEALTHCHECK <options> CMD <command>
  ```
  Various `HEALTHCHECK` options include:
  * `--interval` (default: 30s)
  * `--timeout` (default: 30s)
  * `--start-period` (default: 0s)
  * `--start-interval` (default: 5s)
  * `--retries` (default: 3)
  
  **For example:**
  ```
  HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost/ || exit 1
  ```
  When a container with a `HEALTHCHECK` instruction starts:
  * It has an initial status `starting`.
  * Then Docker runs the specified `command` at the given `interval`.
  * If the `command` exits with `0`, the container's status becomes healthy or remains healthy.
  * If the `command` runs for more than specified `timeout` seconds and exits with a non-zero status, the failure counter increments.
  * When the failure counter reaches the specified `retries` value, the container's status becomes unhealthy.
  * If a health check passes after a failure, the failure counter is reset to `0`.

