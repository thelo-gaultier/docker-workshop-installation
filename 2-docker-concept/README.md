# 2. Docker concepts

## 2.1. Show running containers

* Step 1 : Run docker ps to show running containers:

```{r, engine='bash', count_lines}
$ docker ps
CONTAINER ID IMAGE       COMMAND  CREATED        STATUS                     PORTS NAMES
```
* Step 2 : The output shows that there are no running containers at the moment. Use the command docker ps -a to list all containers:

```{r, engine='bash', count_lines}
docker ps -a
CONTAINER ID IMAGE       COMMAND  CREATED        STATUS                     PORTS  NAMES
6e6db2a24a8e hello-world "/hello" 15 minutes ago Exited (0) 15 minutes ago         dreamy_nobel
77609b91727a hello-world "/hello" 10 minutes ago Exited (0) 10 minutes ago         grave_pike
```

In the previous section we started two containers and the command docker ps -a shows that both of them are exited. Note that Docker has generated random names for the containers (the last column). In your case, these names can be different.

* Step 3 : Let’s run the command docker images to show all the images on your local system:

```{r, engine='bash', count_lines}
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              c54a2cc56cbb        4 weeks ago         1.848 kB
```
As you see, there is only one image that was downloaded from the Docker Hub.

## 2.3. Specify a container main process

* Step 1 : Let’s run our own “hello world” container. We will use the existing Ubuntu image for that:

```{r, engine='bash', count_lines}
$ docker run ubuntu /bin/echo 'Hello world'
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
...
Status: Downloaded newer image for ubuntu:latest
Hello world
```

As you see, Docker downloaded the image ubuntu because it was not on the local machine.

### Questions:

* Was the ubuntu image on the local machine
 <details>
<summary><b>Click here to see the solutions</b></summary>


* Was the ubuntu image on the local machine ?
If you haven't use Docker before the ubuntu image should not be on the local machine, hence docker will pull out the image from the docker repository and save a copy locally. The next time you want to create a container from the Ubuntu image, Docker will use the local copy. 
</details>


* Step 2 : Let’s run the command docker images again:

```{r, engine='bash', count_lines}
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              42118e3df429        11 days ago         124.8 MB
hello-world         latest              c54a2cc56cbb        4 weeks ago         1.848 kB
```
You can see that the images is now on your local host.

* Step 3 : If you run the same “hello world” container again, Docker will use a local copy of the image:

```{r, engine='bash', count_lines}
$ docker run ubuntu /bin/echo 'Hello world'
Hello world
```

## 2.4. Specify an image version
* Step 1 As you see, Docker has downloaded the ubuntu:latest image. You can see Ubuntu version by running the following command:

```{r, engine='bash', count_lines}
$ docker run ubuntu /bin/cat /etc/issue.net
Ubuntu 16.04 LTS
```

Let’s say you need a previous Ubuntu LTS release. In this case, you can specify the version you need:

```{r, engine='bash', count_lines}
$ docker run ubuntu:14.04 /bin/cat /etc/issue.net
Unable to find image 'ubuntu:14.04' locally
14.04: Pulling from library/ubuntu
...
Status: Downloaded newer image for ubuntu:14.04
Ubuntu 14.04.4 LTS
```
Step 2 The docker images command should show that we have two Ubuntu images downloaded locally:

```{r, engine='bash', count_lines}
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              42118e3df429        11 days ago         124.8 MB
ubuntu              14.04               0ccb13bf1954        11 days ago         188 MB
hello-world         latest              c54a2cc56cbb        4 weeks ago         1.848 kB
```
## 2.5. Run an interactive container

* Step 1 Let’s use the ubuntu image to run an interactive bash session. We will use the -t command line argument to assigns a pseudo-tty or terminal inside the new container, and the -i to make an interactive connection by grabbing the standard input of the container. Use the exit command or press Ctrl-D to exit the interactive bash session:
`
```{r, engine='bash', count_lines}
$ docker run -t -i ubuntu /bin/bash
root@17d8bdeda98e:/# uname -a
Linux 17d8bdeda98e 3.19.0-31-generic ...
root@17d8bdeda98e:/# exit
exit
```

* Step 2 Let’s check that when the shell process has finished, the container stops:

```{r, engine='bash', count_lines}
$ docker ps
CONTAINER ID IMAGE       COMMAND  CREATED        STATUS                     PORTS NAMES
```

## 2.6. Run a container in a background

* Step 1 To run a container in a background use the -d command line argument:

```{r, engine='bash', count_lines}
$ docker run -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
ac231579e57faf1afcb76e4f7ff22415d39af73eeeb4476eab3ea6bb8991f556
```
The command returned the container ID (it can be different in your case).

* Step 2 Let’s use the docker ps command to see running containers:

```{r, engine='bash', count_lines}
$ docker ps
CONTAINER ID IMAGE  COMMAND                  CREATED        STATUS        PORTS NAMES
ac231579e57f ubuntu "/bin/sh -c 'while tr"   1 minute ago   Up 11 minute        evil_golick
ac231579e57f is the shortened container ID (it can be different in your case).
```

* Step 3 Let’s use this short container ID to show the container standard output:

```{r, engine='bash', count_lines}
$ docker logs <short-id>
hello world
hello world
hello world
...
``
As we see, in the docker ps command output, the auto generated container name is evil_golick (your container can have a different name).

Step 4 Use your container name to to show the container standard output:

```{r, engine='bash', count_lines}
$ docker logs <name>
hello world
hello world
hello world
...
```
* Step 5 Finally, let’s stop our container:

```{r, engine='bash', count_lines}
$ docker stop <name>
<name>
```

* Step 6 Check, that there are no running containers:

```{r, engine='bash', count_lines}
$ docker ps
CONTAINER ID IMAGE       COMMAND  CREATED        STATUS                     PORTS NAMES
```

## 2.7. Expose containers ports

* Step 1 Let’s run a simple web application. We will use the existing image training/webapp, which contains a Python Flask application:

```{r, engine='bash', count_lines}
$ docker run -d -P training/webapp python app.py
...
Status: Downloaded newer image for training/webapp:latest
6e88f42d3d853762edcbfe1fe73fdc5c48865275bc6df759b83b0939d5bd2456
In the command above we specified the main process (python app.py), the -d command line argument, which tells Docker to run the container in the background. The -P command line argument tells Docker to map any required network ports inside our container to our host. This allows us to access the web application in the container.
```

* Step 2 Use the docker ps command to list running containers:

```{r, engine='bash', count_lines}
$ docker ps
CONTAINER ID IMAGE           COMMAND         CREATED       STATUS       PORTS                   NAMES
6e88f42d3d85 training/webapp "python app.py" 3 minutes ago Up 3 minutes 0.0.0.0:32768->5000/tcp determined_torvalds
The PORTS column contains the mapped ports. In our case, Docker has exposed port 5000 (the default Python Flask port) on port 32768 (can be different in your case).
```

* Step 3 The docker port command shows the exposed port. We will use the container name (determined_torvalds in the example above, it can be different in your case):

```{r, engine='bash', count_lines}
$ docker port <name> 5000
0.0.0.0:32768
```

* Step 4 Let’s check that we can access the web application exposed port:

```{r, engine='bash', count_lines}
$ curl http://localhost:<port>/
Hello world!
```

* Step 5 Let’s stop our web application for now:

```{r, engine='bash', count_lines}
$ docker stop <name>
```

* Step 6 We want to manually specify the local port to expose (-p argument). Let’s use the standard HTTP port 80. We also want to specify the container name (--name argument):

```{r, engine='bash', count_lines}
$ docker run -d -p 80:5000 --name webapp training/webapp python app.py
249476631f7d445c6a61a6067c4c24461b0e51ea0667c86cb1bce301981ecd22
```

* Step 7 Let’s check that the port 80 is exposed:

```{r, engine='bash', count_lines}
$ docker ps
CONTAINER ID IMAGE           COMMAND         CREATED       STATUS       PORTS                NAMES
249476631f7d training/webapp "python app.py" 1 minute  ago Up 1 minute  0.0.0.0:80->5000/tcp webapp

$ curl http://localhost/
Hello world!
```

## 2.8. Inspect a container

* Step 1 You can use the docker inspect command to see the configuration and status information for the specified container:

```{r, engine='bash', count_lines}
$ docker inspect webapp
[
    {
        "Id": "249476631f7d...",
        "Created": "2016-08-02T23:42:56.932135327Z",
        "Path": "python",
        "Args": [
            "app.py"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 16055,
            "ExitCode": 0,
            "Error": "",
            ...
```

* Step 2 You can specify a filter (-f command line argument) to show only specific elements. For example:

```{r, engine='bash', count_lines}
$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' webapp
172.17.0.2
```
This command returned the IP address of the container.

## 2.9. Execute a process in a container

* Step 1 Use the docker exec command to execute a command in the running container. For example:

```{r, engine='bash', count_lines}
$ docker exec webapp ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.2  0.0  52320 17384 ?        Ss   00:11   0:00 python app.py
root        26  0.0  0.0  15572  2104 ?        Rs   00:12   0:00 ps aux
```
The same command with the -it command line argument can be used to run an interactive session in the container:

```{r, engine='bash', count_lines}
$ docker exec -it webapp bash
root@249476631f7d:/opt/webapp# ps auxw
ps auxw
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0  52320 17384 ?        Ss   00:11   0:00 python app.py
root        32  0.0  0.0  18144  3064 ?        Ss   00:14   0:00 bash
root        47  0.0  0.0  15572  2076 ?        R+   00:16   0:00 ps auxw
```

* Step 2 Use the exit command or press Ctrl-D to exit the interactive bash session:

```{r, engine='bash', count_lines}
root@249476631f7d:/opt/webapp# exit
```

## 2.10. Copy files to/from container
The docker cp command allows you to copy files from the container to the local machine or from the local file system to the container. This command works for a running or stopped container.

* Step 1 Let’s copy the container’s app.py file to the local machine:

```{r, engine='bash', count_lines}
$ docker cp webapp:/opt/webapp/app.py .
```

* Step 2 Edit the local app.py file. For example, change the line return 'Hello '+provider+'!' to return 'Hello '+provider+'!!!'. Copy the modified file back and restart the container:

```{r, engine='bash', count_lines}
$ docker cp app.py webapp:/opt/webapp/
$ docker restart webapp
```

* Step 3 Check that the modified web application works::

```{r, engine='bash', count_lines}
$ curl http://localhost/
Hello world!!!
```
##2.11. Restart a container

* Step 1 Let’s stop the container with web application:
```{r, engine='bash', count_lines}
$ docker stop webapp
```
The main process inside the container will receive SIGTERM, and after a grace period, SIGKILL.

* Step 2 You can start the container later using the docker start command:

```{r, engine='bash', count_lines}
$ docker start webapp
```

* Step 3 Check that the web application works:

```{r, engine='bash', count_lines}
$ curl http://localhost/
Hello world!
Step 4 You also can restart the running container using the docker restart command. The -t command line argument specifies a number of seconds to wait for stop before killing the container:

```{r, engine='bash', count_lines}
$ docker restart webapp
```

## 2.12. Remove a container

* Step 1 You can remove the existing container. You should stop the container before removing it. Alternatively you can use the -f command line argument:

```{r, engine='bash', count_lines}
$ docker rm -f webapp
```

## 2.13. Use a Data Volume

* Step 1 Add a data volume to a container:

```{r, engine='bash', count_lines}
$ docker run -d -P --name webapp -v /webapp training/webapp python app.py
```
This command started a new container and created a new volume inside the container at /webapp.

TODO: add explaination

* Step 2 Locate the volume on the host using the docker inspect command:

```{r, engine='bash', count_lines}
$ docker inspect webapp
...
    "Mounts": [
        {
            "Name": "7d72348e8f...",
            "Source": "/var/lib/docker/volumes/7d72348e8f.../_data",
            "Destination": "/webapp",
            "Driver": "local",
            "Mode": "",
            "RW": true,
            "Propagation": ""
        }
    ],
...
```

Alternatively, you can specify a host directory you want to use as a data volume:

```{r, engine='bash', count_lines}
$ mkdir db
$ docker run -d --name db -v $HOME/db:/db training/postgres
```
TODO: Add explaination src_dir:dst_dir

* Step 3 Start an interactive session in the db container and create a new file in the /db directory:

```{r, engine='bash', count_lines}
$ docker exec -it db bash
root@9a7a4fbcc929:/# cd /db
root@9a7a4fbcc929:/db# touch hello_from_db_container
root@9a7a4fbcc929:/db# exit
```
* Step 4 Check that the local db directory contains the new file:

```{r, engine='bash', count_lines}
$ ls db
hello_from_db_container
``
Step 5 Check that the data volume is persistent. Remove the db container:
```{r, engine='bash', count_lines}
$ docker rm -f db
```
* Step 6 Create the db container again:

```{r, engine='bash', count_lines}
$ docker run -d --name db -v /home/stack/db:/db training/postgres
```

* Step 7 Check that its /db directory contains the hello_from_db_container file:

```{r, engine='bash', count_lines}
$ docker exec -it db bash
root@47a60c01590e:/# ls /db
hello_from_db_container
root@47a60c01590e:/# exit
```
## 2.14. Use a Data Volume Container
* Step 1 Let’s create a new named container with a volume to share:

```{r, engine='bash', count_lines}
$ docker create -v /dbdata --name dbstore training/postgres /bin/true
```
This container does not run an application and reuses the training/postgres image.

* Step 2 Use the --volumes-from flag to mount the /dbdata volume in another containers:

```{r, engine='bash', count_lines}
$ docker run -d --volumes-from dbstore --name db1 training/postgres
$ docker run -d --volumes-from dbstore --name db2 training/postgres
```
TODO:

```
helos-MBP:~ Thelo$ docker run -d --volumes-from dbstore --name db1 training/postgres
8eccc3531c0af12b8c2948ad48711b71f4c8845417a83423976306b0ef921028
Thelos-MBP:~ Thelo$ docker run -d --volumes-from dbstore --name db2 training/postgres
6066746c4da989672fc560ee6f1c711e738f922cd658bd166ea76b0a6d25db45
Thelos-MBP:~ Thelo$ docker exec -it db1 bash
root@8eccc3531c0a:/# ls /
bin   dbdata  etc   lib    media  opt    root  sbin  sys  usr
boot  dev     home  lib64  mnt      proc    run   srv   tmp  var
root@8eccc3531c0a:/# mount | grep dbdata
/dev/sda2 on /dbdata type ext4 (rw,relatime,data=ordered)
root@8eccc3531c0a:/# cd dbdata/
root@8eccc3531c0a:/dbdata#
```

## 2.15. Link containers
* Step 1 You should have the db and webapp containers running. Remove the webapp container:

```{r, engine='bash', count_lines}
$ docker rm -f webapp
```

* Step 2 Create a new webapp container and link it with your db container:

```{r, engine='bash', count_lines}
$ docker run -d -P --name webapp --link db:db training/webapp python app.py
```
TODO: explain -P option plus where everything comes from...

* Step 3 Inspect your linked containers with docker inspect:

```{r, engine='bash', count_lines}
$ docker inspect -f "{{ .HostConfig.Links }}" webapp
[/db:/webapp/db]
```
You see that the webapp container is now linked to the db container. This allows it to access information about the db container.

* Step 4 Start an interactive session in the webapp container and check its /etc/hosts file and its environment variables:

```{r, engine='bash', count_lines}
$ docker exec -it webapp bash
root@90eb1de6fa8a:/opt/webapp# env | grep DB
DB_NAME=/webapp/db
DB_PORT_5432_TCP_ADDR=172.17.0.3
DB_PORT=tcp://172.17.0.3:5432
DB_PORT_5432_TCP=tcp://172.17.0.3:5432
DB_PORT_5432_TCP_PORT=5432
DB_PORT_5432_TCP_PROTO=tcp
DB_ENV_PG_VERSION=9.3
root@90eb1de6fa8a:/opt/webapp# cat /etc/hosts
...
172.17.0.3  db 47a60c01590e
172.17.0.2  90eb1de6fa8a
root@90eb1de6fa8a:/opt/webapp# exit
```

You see that Docker updated the /etc/hosts file and set the environment variables. This information can be used in the webapp container to access the database.


## 2.16. Connect a container to a network

This section is optional, you can complete the rest of the workshop first then come back to this part.

* Step 1 Start a database container ( here we will use postgresql):

```{r, engine='bash', count_lines}
$ docker run -d --name db training/postgres
```

* Step 2 Check that the webapp and db containers are running:

```{r, engine='bash', count_lines}
$ docker ps
CONTAINER ID IMAGE             COMMAND                CREATED        STATUS        PORTS                NAMES
...
c3afff20019a training/postgres "su postgres -c '/usr" 4 minutes ago  Up 4 minutes  5432/tcp             db
62ed4a627356 training/webapp   "python app.py"        30 minutes ago Up 30 minutes 0.0.0.0:80->5000/tcp webapp
```

* Step 3 Get IP addresses of the webapp and db containers:

```{r, engine='bash', count_lines}
$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' webapp
172.17.0.3

$ docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' db
172.17.0.4
```

* Step 4 In your case, the IP addresses can be different. Let’s start an interactive session in the db container and ping the IP address of the webapp:

```{r, engine='bash', count_lines}
$ docker exec -it db bash
root@c3afff20019a:/# ping -c 1 172.17.0.3
PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.
64 bytes from 172.17.0.3: icmp_seq=1 ttl=64 time=0.111 ms

--- 172.17.0.3 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.111/0.111/0.111/0.000 ms
root@c3afff20019a:/# exit
```

* Step 5 The docker network ls command shows Docker networks:

```{r, engine='bash', count_lines}
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
d428e49e4869        bridge              bridge              local
0d1f78528cc5        host                host                local
4a07cef84617        none                null                local
```

* Step 6 You can inspect your containers to see the networks they are connected to:

```{r, engine='bash', count_lines}
$ docker inspect -f '{{json .NetworkSettings.Networks}}' webapp
{"bridge": ...


$ docker inspect -f '{{json .NetworkSettings.Networks}}' db
{"bridge": ...
```

* Step 7 You can inspect the bridge network to get IP address of all containers in this network:

```{r, engine='bash', count_lines}
$ docker network inspect bridge
...
"Containers": {
    "62ed4a627356...": {
        "Name": "webapp",
        "EndpointID": "7af40b8967c0...",
        "MacAddress": "02:42:ac:11:00:03",
        "IPv4Address": "172.17.0.3/16",
        "IPv6Address": ""
    },
    "c3afff20019a...": {
        "Name": "db",
        "EndpointID": "1458900b4ebc...",
        "MacAddress": "02:42:ac:11:00:04",
        "IPv4Address": "172.17.0.4/16",
        "IPv6Address": ""
    }
},
...
```

* Step 8 By default, Docker runs containers in the bridge network. You may want to isolate one or more containers in a separate network. Let’s create a new network:

```{r, engine='bash', count_lines}
$ docker network create my-network \
-d bridge \
--subnet 172.19.0.0/16
```
The -d bridge command line argument specifies the bridge network driver and the --subnet command line argument specifies the network segment in CIDR format. If you do not specify a subnet when creating a network, then Docker assigns a subnet automatically, so it is a good idea to specify a subnet to avoid potential conflicts with the existing networks.

* Step 9 To check that the new network is created, execute docker network ls:

```{r, engine='bash', count_lines}
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
d428e49e4869        bridge              bridge              local
0d1f78528cc5        host                host                local
56ef0481820d        my-network          bridge              local
4a07cef84617        none                null                local
```
* Step 10 Let’s inspect the new network:

```{r, engine='bash', count_lines}
$ docker network inspect my-network
[
    {
        "Name": "my-network",
        "Id": "56ef0481820d...",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.19.0.0/16"
                }
            ]
        },
        "Internal": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```
* Step 11 As expected, there are no containers connected to the my-network. Let’s recreate the db container in the my-network:

```{r, engine='bash', count_lines}
$ docker rm -f db

$ docker run -d --network=my-network --name db training/postgres
```

* Step 12 Inspect the my-network again:

```{r, engine='bash', count_lines}
$ docker network inspect my-network
...
    "Containers": {
        "93af62cdab64...": {
            "Name": "db",
            "EndpointID": "b1e8e314cff0...",
            "MacAddress": "02:42:ac:12:00:02",
            "IPv4Address": "172.19.0.2/16",
            "IPv6Address": ""
        }
    },
...
```
As you see, the db container is connected to the my-network and has 172.19.0.2 address.

* Step 13 Let’s start an interactive session in the db container and ping the IP address of the webapp again:

```{r, engine='bash', count_lines}
$ docker exec -it db bash
root@c3afff20019a:/# ping -c 1 172.17.0.3
PING 172.17.0.3 (172.17.0.3) 56(84) bytes of data.

--- 172.17.0.3 ping statistics ---
1 packets transmitted, 0 received, 100% packet loss, time 0ms
```
As expected, the webapp container is no longer accessible from the db container, because they are connected to different networks.

* Step 14 Let’s connect the webapp container to the my-network:

```{r, engine='bash', count_lines}
$ docker network connect my-network webapp
```

* Step 15 Check that the webapp container now is connected to the my-network:

```{r, engine='bash', count_lines}
$ docker network inspect my-network
...
    "Containers": {
        "62ed4a627356...": {
            "Name": "webapp",
            "EndpointID": "ae95b0103bbc...",
            "MacAddress": "02:42:ac:12:00:03",
            "IPv4Address": "172.19.0.3/16",
            "IPv6Address": ""
        },
        "93af62cdab64...": {
            "Name": "db",
            "EndpointID": "b1e8e314cff0...",
            "MacAddress": "02:42:ac:12:00:02",
            "IPv4Address": "172.19.0.2/16",
            "IPv6Address": ""
        }
    },
...
```
The output shows that two containers are connected to the my-network and the webapp container has 172.19.0.3 address in that network.

* Step 16 Check that the webapp container is accessible from the db container using its new IP address:

```{r, engine='bash', count_lines}
$ docker exec -it db bash
root@c3afff20019a:/# ping -c 1 172.19.0.3
PING 172.19.0.3 (172.19.0.3) 56(84) bytes of data.
64 bytes from 172.19.0.3: icmp_seq=1 ttl=64 time=0.136 ms

--- 172.19.0.3 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.136/0.136/0.136/0.000 ms
```


# Checkpoint
* Install Docker
* List running containers
* Specify a container main process
* Run an interactive container
* Run a container in a background
* Use a container ID and container name to display container standard output
* Expose container ports
* Execute a process in a container
* Copy files to/from container
* Use a data volume
* Use a data volume container
* Link containers
* Stop a container
* Remove a container
* [Optional] Connect a container to a network
