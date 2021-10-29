Now you are using the Docker Swarm. Everybody knows that you need a few things to deploy your application to the Docker Swarm: stack and environment files. Let's deploy the simple application.

> We choose the [echoserver](https://github.com/cilium/echoserver). It is a simple web server which provide some information about it.

Create a stack file:

<pre class="file" data-filename="echoserver.yml" data-target="replace">
---
version: '3.3'
services:
  app:
    image: quay.io/qwinkler/echoserver:1.10.3
    env_file:
      - echoserver.env
    ports:
      - 8888:8080
    healthcheck:
      test: ["CMD", "curl", "http://localhost:8080"]
      interval: 30s
      timeout: 10s
      retries: 3
    labels:
      foo: bar
    deploy:
      mode: replicated
      replicas: 1
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
      resources:
        limits:
          cpus: '0.1'
          memory: 32M
        reservations:
          cpus: '0.1'
          memory: 32M
</pre>

And an environment file:

<pre class="file" data-filename="echoserver.env" data-target="replace">
---
PORT=8080
</pre>