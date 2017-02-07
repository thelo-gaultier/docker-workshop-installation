## 3.2. Build a multi-container application
In this section, will guide you through the process of building a multi-container application. The application code is available at GitHub:
https://github.com/docker/example-voting-app
* Step 1 : Clone the existing application from GitHub:

```{r, engine='bash', count_lines}
$ git clone https://github.com/docker/example-voting-app.git
Cloning into 'example-voting-app'...
...


$ cd example-voting-app
```

* Step 2 : Edit the vote/app.py file, change the following lines near the top of the file with something far better:

```{r, engine='bash', count_lines}
option_a = os.getenv('OPTION_A', "Cats")
option_b = os.getenv('OPTION_B', "Dogs")
```
* Step 3 : Replace “Cats” and “Dogs”  with two options of your choice. For example:

```{r, engine='bash', count_lines}
option_a = os.getenv('OPTION_A', "Kitten")
option_b = os.getenv('OPTION_B', "Puppy")
```

* Step 4 : The existing file docker-compose.yml defines several images:

1. A voting-app container based on a Python image. It also provides a web page for the user to post and save his vote to redis ( see below )
2. A result-app container based on a Node.js image. 
3. A Redis container based on a redis image, to temporarily store the data. ( Key - Value cache software)
4. A worker app based on a dotnet image
5. A Postgres container based on a postgres image ( Database)

In general the multi-container application architecture looks like this:

![architecture](https://raw.githubusercontent.com/docker/example-voting-app/master/architecture.png "Voting app architecture")



> Note that three of the containers are built from Dockerfiles, while the other two are images on Docker Hub ( Redis and Postgres).

* Step 5 : Let’s change the default port to expose. Edit the docker-compose.yml file and find the following lines:

```{r, engine='bash', count_lines}
...
ports:
  - "5000:80"
...

# Change 5000 to 80:

...
ports:
  - "80:80"
...
```

* Step 6 : Install the docker-compose tool ( if not already installed):

```{r, engine='bash', count_lines}
$ curl -Lo docker-compose \
https://github.com/docker/compose/releases/download/1.8.0/docker-compose-Linux-x86_64 \
&& chmod +x docker-compose \
&& sudo mv docker-compose /usr/local/bin/
```

* Step 7 : Use the docker-compose tool to launch your application:

> Docker-compose is a tool allowing you to define and run multi/container application.
> The set of container is defined inside the docker-compose.yml file

```{r, engine='bash', count_lines}
$ docker-compose up -d
```

This command will run all the containers defined in the docker-compose.yml file.

* Step 8 : Check that all containers are running:

```{r, engine='bash', count_lines}
$ docker ps
59e355151f56 examplevotingapp_worker "/bin/sh -c 'dotnet W" 2 minutes ago Up 2 minutes                                                     examplevotingapp_worker_1
fd0740ee1525 redis:alpine            "docker-entrypoint.sh" 2 minutes ago Up 2 minutes      0.0.0.0:32772->6379/tcp                        redis
1fd3b378dd0a examplevotingapp_result "nodemon --debug serv" 2 minutes ago Up 2 minutes      0.0.0.0:5858->5858/tcp, 0.0.0.0:5001->80/tcp   examplevotingapp_result_1
0948fd9ba26c postgres:9.4            "/docker-entrypoint.s" 2 minutes ago Up 2 minutes      5432/tcp                                       db
52ef44aa1b97 examplevotingapp_vote   "python app.py"        2 minutes ago Up About a minute 0.0.0.0:80->80/tcp                             examplevotingapp_vote_1
```

* Step 9 : Use your browser to open the address http://<Host IP> and check that the application works. Then stop the application:

1. To vote check the voting-app web page : http://<Host IP>:80
2. To see the result, check the result-app webb page : http://<Host IP:5001

* Step 10 : Explore the docker-compose file

#### Take a look at the docker-compose file and try to answer the following questions:

##### What are the different parts of the docker-compose file?

<details>
<summary>Click here for the answer</summary>

In this file, we can see three different information
1. Version : ( here 3) Indicates to docker-compose the version of the docker-file ( along the different docker-compose the syntax evolved a little bit).
2. Services : List and specification of the different container to start.
3. Volumes : List and specification of the docker volume that may be used by the containers.
</details>


##### Where do the images of the container without the "images" come from?

<details>
<summary>Click here for the answer</summary>

These "image-less" containers have the image field replace with the field "build", which indicates the location of the 
container Dockerfile.
</details>

##### How does the voting-app knows the cache information ( redis ) to save the vote result?

> Hint: Feel free to check the file ./vote/app.py

<details>
<summary>Click here for the answer</summary>


Here the redis information are semi hard-coded in the code. The voting-app point to the "redis" domain name. Since the redis container
is linked to the voting-app container, the "redis" domain name will be automatically translated to the redis ip.

```python
def get_redis():
    if not hasattr(g, 'redis'):
        g.redis = Redis(host="redis", db=0, socket_timeout=5)
    return g.redis
```

</details>

#### Why does the "worker" container does not have a "command" field?


<details>
<summary>Click here for the answer</summary>

The command is declared in the worker dockerfile instead of in the docker-compose.yml file.


```{r, engine='bash', count_lines}

RUN dotnet restore -v minimal src/ \
    && dotnet publish -c Release -o ./ src/Worker/ \
    && rm -rf src/ $HOME/.nuget/

CMD dotnet Worker.dll
```


</details>










* Step 11 : shutdown the multi-container application.

```{r, engine='bash', count_lines}
$ docker-compose down
Stopping examplevotingapp_worker_1 ... done
Stopping examplevotingapp_redis_1 ... done
Stopping examplevotingapp_result_1 ... done
Stopping examplevotingapp_db_1 ... done
Stopping examplevotingapp_vote_1 ... done
Removing examplevotingapp_worker_1 ... done
Removing examplevotingapp_redis_1 ... done
Removing examplevotingapp_result_1 ... done
Removing examplevotingapp_db_1 ... done
Removing examplevotingapp_vote_1 ... done
Removing network examplevotingapp_default
```

#Checkpoint
Write Dockerfile
Build an image
Install Docker Compose
Write docker-compose.yml file
Use Docker Compose to build and run a multi-container application