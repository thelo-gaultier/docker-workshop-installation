
## 5.1. Connect a container to a network

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
* Connect a container to a network
