# Prepare stack and env files

Now you are using the Docker Swarm. Everybody knows that you need the few things in order to deploy your application to the Docker Swarm: stack and environment files. Let's deploy the simple application.

> We choose the [echoserver](https://github.com/cilium/echoserver). It is a simple web server which provide some information about it.

Create a stack file (`echoserver.yml`):

```
---
version: '3.3'
services:
  app:
    image: docker.io/cilium/echoserver:1.10.3
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
```

And an environment file (`echoserver.env`):

```
---
PORT=8080
```