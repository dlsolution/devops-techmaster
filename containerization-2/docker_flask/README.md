# Differences between user-defined bridges and the default bridge 🔗

- **Bridge network** The default network driver. If you don’t specify a driver, this is the type of network you are creating. Bridge networks are usually used when your applications run in standalone containers that need to communicate. 

- **User-defined bridge network** are best when you need multiple containers to communicate on the same Docker host.

- **User-defined bridges provide automatic DNS resolution between containers.** 

    Containers on the default bridge network can only access each other by IP addresses. On a user-defined bridge network, containers can resolve each other by name or alias.

- **User-defined bridges provide better isolation.**

    All containers without a --network specified, are attached to the default bridge network. This can be a risk, as unrelated stacks/services/containers are then able to communicate.

    Using a user-defined network provides a scoped network in which only containers attached to that network are able to communicate.

- **Containers can be attached and detached from user-defined networks on the fly.**

    During a container’s lifetime, you can connect or disconnect it from user-defined networks on the fly. To remove a container from the default bridge network, you need to stop the container and recreate it with different network options.

- **Each user-defined network creates a configurable bridge.**

    If your containers use the default bridge network, you can configure it, but all the containers use the same settings, such as MTU and iptables rules. In addition, configuring the default bridge network happens outside of Docker itself, and requires a restart of Docker.

    User-defined bridge networks are created and configured using docker network create. If different groups of applications have different network requirements, you can configure each user-defined bridge separately, as you create it.

- **Linked containers on the default bridge network share environment variables.**

    Containers connected to the same user-defined bridge network effectively expose all ports to each other. For a port to be accessible to containers or non-Docker hosts on different networks, that port must be published using the -p or --publish flag.

[https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge](https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge)