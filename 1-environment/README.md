## 1.1. Install Docker On Linux

You can find the installation steps for your distribution at https://docs.docker.com/engine/installation/.


## 1.2. Install Docker on OS X

You can find the installation steps at : https://docs.docker.com/docker-for-mac/

You do not need to run the thousands of example proposed by the docker documentation at the moment.

## 2. Verify Docker is properly installed


```{r, engine='bash', count_lines}

$ docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker Hub account:
 https://hub.docker.com

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/

```

Now let's get some hands on what Docker can actually do, [Part 2 : Docker concept](../2-docker-concept/).

