## 3.2. Build a multi-container application
In this section, will guide you through the process of building a multi-container application. The application code is available at GitHub:
https://github.com/docker/example-voting-app
* Step 1 Clone the existing application from GitHub:

```{r, engine='bash', count_lines}
$ git clone https://github.com/docker/example-voting-app.git
Cloning into 'example-voting-app'...
...


$ cd example-voting-app
```

* Step 2 Edit the vote/app.py file, change the following lines near the top of the file:

```{r, engine='bash', count_lines}
option_a = os.getenv('OPTION_A', "Cats")
option_b = os.getenv('OPTION_B', "Dogs")
Step 3 Replace “Cats” and “Dogs” with two options of your choice. For example:

option_a = os.getenv('OPTION_A', "Java")
option_b = os.getenv('OPTION_B', "Python")
```

* Step 4 The existing file docker-compose.yml defines several images:

A voting-app container based on a Python image
A result-app container based on a Node.js image
A Redis container based on a redis image, to temporarily store the data.
A worker app based on a dotnet image
A Postgres container based on a postgres image
Note that three of the containers are built from Dockerfiles, while the other two are images on Docker Hub.

* Step 5 Let’s change the default port to expose. Edit the docker-compose.yml file and find the following lines:

```{r, engine='bash', count_lines}
...
ports:
  - "5000:80"
...
Change 5000 to 80:

...
ports:
  - "80:80"
...
```

* Step 6 Install the docker-compose tool:

```{r, engine='bash', count_lines}
$ curl -Lo docker-compose \
https://github.com/docker/compose/releases/download/1.8.0/docker-compose-Linux-x86_64 \
&& chmod +x docker-compose \
&& sudo mv docker-compose /usr/local/bin/
```

* Step 7 Use the docker-compose tool to launch your application:

```{r, engine='bash', count_lines}
$ docker-compose up -d
```

* Step 8 Check that all containers are running:

```{r, engine='bash', count_lines}
$ docker ps
59e355151f56 examplevotingapp_worker "/bin/sh -c 'dotnet W" 2 minutes ago Up 2 minutes                                                     examplevotingapp_worker_1
fd0740ee1525 redis:alpine            "docker-entrypoint.sh" 2 minutes ago Up 2 minutes      0.0.0.0:32772->6379/tcp                        redis
1fd3b378dd0a examplevotingapp_result "nodemon --debug serv" 2 minutes ago Up 2 minutes      0.0.0.0:5858->5858/tcp, 0.0.0.0:5001->80/tcp   examplevotingapp_result_1
0948fd9ba26c postgres:9.4            "/docker-entrypoint.s" 2 minutes ago Up 2 minutes      5432/tcp                                       db
52ef44aa1b97 examplevotingapp_vote   "python app.py"        2 minutes ago Up About a minute 0.0.0.0:80->80/tcp                             examplevotingapp_vote_1
```

Step 9 Use your browser to open the address http://<lab IP> and check that the application works. Then stop the application:

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