# 3. Docker Images
In this lab, we will write a Dockerfile and build a new Docker image.

In the previous section (2. Docker Concepts), we learned how to use Docker images to run Docker containers. Docker images that we used have been downloaded from the Docker Hub, a registry of Docker images. 

In this section we will create a simple web application from scratch. We will use Flask (http://flask.pocoo.org/), a microframework for Python. Our application for each request will display a random picture from the defined set.

In the next session we will create all necessary files for our application. The code for this application is also available in this directory.

## 3.1.1. Create project files

* Step 1 Create a new directory flask-app for our project:

```{r, engine='bash', count_lines}
$ mkdir flask-app
$ cd flask-app
```

* Step 2 In this directory, we will create the following project files:

flask-app/
    Dockerfile
    app.py
    requirements.txt
    templates/
        index.html

* Step 3 Create a new file app.py with the following content:

``` python
from flask import Flask, render_template
import random

app = Flask(__name__)

# list of cat images
images = [
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr05/15/9/anigif_enhanced-buzz-26388-1381844103-11.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr01/15/9/anigif_enhanced-buzz-31540-1381844535-8.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr05/15/9/anigif_enhanced-buzz-26390-1381844163-18.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr06/15/10/anigif_enhanced-buzz-1376-1381846217-0.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr03/15/9/anigif_enhanced-buzz-3391-1381844336-26.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr06/15/10/anigif_enhanced-buzz-29111-1381845968-0.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr03/15/9/anigif_enhanced-buzz-3409-1381844582-13.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr02/15/9/anigif_enhanced-buzz-19667-1381844937-10.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr05/15/9/anigif_enhanced-buzz-26358-1381845043-13.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr06/15/9/anigif_enhanced-buzz-18774-1381844645-6.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr06/15/9/anigif_enhanced-buzz-25158-1381844793-0.gif",
    "http://ak-hdl.buzzfed.com/static/2013-10/enhanced/webdr03/15/10/anigif_enhanced-buzz-11980-1381846269-1.gif"
]

@app.route('/')
def index():
    url = random.choice(images)
    return render_template('index.html', url=url)

if __name__ == "__main__":
    app.run(host="0.0.0.0")
```

> This code will simply create a rest API returning a web page with a random cat GIF.
    
* Step 4 Create a new file requirements.txt with the following line:

```{r, engine='bash', count_lines}
Flask==0.10.1
```
> In python dependencies can be all listed in a requirement file. Here we just need Flask.

* Step 5 Create a new directory templates and create a new file index.html in this directory with the following content:

``` html
<html>
  <head>
    <style type="text/css">
      body {
        background: black;
        color: white;
      }
      div.container {
        max-width: 500px;
        margin: 100px auto;
        border: 20px solid white;
        padding: 10px;
        text-align: center;
      }
      h4 {
        text-transform: uppercase;
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h4>Cat Gif of the day</h4>
      <img src="{{url}}" />
    </div>
  </body>
</html>
```
> This is the html template that will host our amazing GIF.

* Step 6 Create a new file Dockerfile:

```{r, engine='bash', count_lines}
# our base image
FROM alpine:3.5

# Install python and pip
RUN apk add --update py2-pip

# upgrade pip
RUN pip install --upgrade pip

# install Python modules needed by the Python app
COPY requirements.txt /usr/src/app/
RUN pip install --no-cache-dir -r /usr/src/app/requirements.txt

# copy files required for the app to run
COPY app.py /usr/src/app/
COPY templates/index.html /usr/src/app/templates/

# tell the port number the container should expose
EXPOSE 5000

# run the application
CMD ["python", "/usr/src/app/app.py"]
```
> Here we start from an existing image ( alpine:3.5) then add some extra steps because we need it for our application.

## 3.1.2. Build a Docker image
* Step 1 Now we can build our Docker image. In the command below, replace <user-name> with your user name. For a real image, the user name should be the same as you created when you registered on Docker Hub. Because we will not publish our image, you can use any valid name:

```{r, engine='bash', count_lines}
$ docker build -t <user-name>/myfirstapp .
Sending build context to Docker daemon 8.192 kB
Step 1 : FROM alpine:latest
...
Successfully built f1277efd5a79
```

Step 2 Check that your image exists:

```{r, engine='bash', count_lines}
$ docker images
REPOSITORY     TAG    IMAGE ID     CREATED       SIZE
.../myfirstapp latest f1277efd5a79 6 minutes ago 56.34 MB
```

* Step 3 Run a container in a background and expose a standard HTTP port (80), which is redirected to the container’s port 5000:

```{r, engine='bash', count_lines}
$ docker run -dp 80:5000 --name myfirstapp <user-name>/myfirstapp
```

* Step 4 Use your browser to open the address http://<lab IP> and check that the application works.

* Step 5 Stop the container and remove it:

```{r, engine='bash', count_lines}
$ docker stop myfirstapp
myfirstapp
$ docker rm myfirstapp
myfirstapp
```

Having a container is good, but having an army of container working together is much better, to see how to run multi-containers application check this page [Part 4 : Run a multi-containers application](../4-multi-container-application/).

