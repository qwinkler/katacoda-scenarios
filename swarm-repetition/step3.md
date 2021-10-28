To summarize, you know the following things about Docker Swarm:

- To deploy your application, you need a stack and environment files;
- To deploy your application, you need a SSH access to the one of the master nodes;
- Full container definition in the stack file/manifest;
- Internal communication between services by their name;
- To expose your application, you need to setup `ports` section;
- We need a `healthcheck` to understand the service health.