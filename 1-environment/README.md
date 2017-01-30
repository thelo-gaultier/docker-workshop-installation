# 1. Environment

See below what fit your need the best based on your OS:

- 1.1 Install Docker on Linux
- 1.2 Install Docker on OS X
- 1.3 Do not install Docker and use one of our VM.


## 1.1. Install Docker On Linux
* Step 1 We will install Docker from Docker’s APT repository. For the first step, we will install HTTPS transport for Apt:

```{r, engine='bash', count_lines}
$ sudo apt-get update
$ sudo apt-get install -y apt-transport-https ca-certificates
```

* Step 2 Add a new GPG key:

```{r, engine='bash', count_lines}
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 \
--recv-keys 58118E89F3A912897C070ADBF76221572C52609D
```

* Step 3 Add a new Apt source entry:

```{r, engine='bash', count_lines}
$ echo "deb https://apt.dockerproject.org/repo ubuntu-xenial main" | \
sudo tee /etc/apt/sources.list.d/docker.list
```
* Step 4 Update the apt package index:
```{r, engine='bash', count_lines}
$ sudo apt-get update
```
* Step 5 Install Docker:
```{r, engine='bash', count_lines}
$ sudo apt-get install -y docker-engine
```
* Step 6 Let’s verify that Docker is installed correctly. The following command downloads a test image and runs it in a container. When the container runs, it prints an informational message and exits:

```{r, engine='bash', count_lines}
$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
...
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
```
To try something more ambitious, you can run an Ubuntu container with:
```{r, engine='bash', count_lines}
 $ docker run -it ubuntu bash
 ```

Share images, automate workflows, and more with a free Docker Hub account:
 https://hub.docker.com

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
* Step 7 By default, the docker daemon binds to a Unix socket, which is owned by the user root and other users can access it with sudo. To make it possible for regular users to use the docker client without sudo there is a group called docker. When the docker daemon starts, it makes the ownership of the socket read/writable by the docker group.

Let’s add the current user stack to the docker group:
```{r, engine='bash', count_lines}
$ sudo usermod -aG docker stack
```
* Step 8 Usually, to make the changes to take affect, you need to logout and login again, but in our case, we will create a new login session:
```{r, engine='bash', count_lines}
$ sudo su - stack
```
* Step 9 Check that the user stack is in the docker group:

```{r, engine='bash', count_lines}
$ id
uid=1000(stack) gid=1000(stack) groups=1000(stack),999(docker)
```
Step 10 Now you can use docker without sudo:
```{r, engine='bash', count_lines}
$ docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.
...
```

## 1.2. Install Docker on OS X

You can find the installation steps at : https://docs.docker.com/docker-for-mac/

You do not need to run the thousands of example proposed by the docker documentation at the moment.

## 1.3. 1.3 Do not install Docker and use one of our VM.


TODO



Now let's get some hands on what Docker can actually do, [Part 2 : Docker concept](../2-docker-concept/).

