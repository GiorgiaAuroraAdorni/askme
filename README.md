# AskMe

AskMe is a simple Q&A application built on the model of StackOverflow. It
supports asking questions, posting answers and up- or down-voting existing answers.

The source code is available on GitLab at
[https://gitlab.com/GiorgiaAuroraAdorni/askme/](https://gitlab.com/GiorgiaAuroraAdorni/askme/).

#### Contributors
This project has been developed by Giorgia Adorni (806787) and Elia Cereda (807539).


## Architecture
The application has a distributed architecture composed of three main components:
* **web interface:** a client-side web application built with React, through which 
the users interact with the platform.
* **API server:** a web service built using Python and the Flask microframework 
that exposes the REST API used by the frontend.
* **database:** a PostgreSQL instance responsible for persistently storing 
the users' data.

## Containerization
We use Docker to containerize each of the three components. This allows us to 
simplify the setup process of the development environment and to create deployable 
images that contain all dependencies needed by a component. 

Thanks to Docker Compose, developers are able to spin up and debug the various 
containers of the application with a single `docker-compose up` command.
In addition, our `docker-compose.yml` file also starts an instance of pgAdmin to
enable direct access to the database.

We wrote the `Dockerfile`s to take advantage of
[multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build)
and build arguments to be able to produce two types of images from a single 
configuration: full-featured development images with compilers, debugger support
and live code reloading and slimmed down production images that include just 
the bare minimum required to execute the application.

For example, the web interface requires Node.js and a large number of other
dependencies during development and compilation, while only requiring a static
web server (such as Nginx) in production. Copying only the compiled files to 
production allowed us to create extremely small and efficient images.


## Continuous Integration / Continuous Deployment
We wrote tests for the API server with pytest and for the web interface with the
tools provided by React.


GitLab interface provides a visualization of the pipeline status.   

![alt text](docs/images/Env-production.jpg)


Using GitLab CI, we have set up a pipeline of Continuous Integration that runs after every commit: 
 * building Docker images
![alt text](docs/images/build running pipelines.jpg)
 * running unit tests
![alt text](docs/images/job build.jpg)
 * publishing images to GitLab Container Registry

With Continuous Deployment, commits that successfully pass all stages of the CI pipeline 
are published to the production environment automatically.
This ensures that only functioning builds are released to the customers.

![alt text](docs/images/deploy running pipelines.jpg)

![alt text](docs/images/job deploy.jpg)

In `.gitlab-ci.yml` file we configure the three stages of our pipeline: build, test, and deploy.
On any push GitLab will look for the `.gitlab-ci.yml` file and start jobs on Runners 
according to the contents of the file, for that commit.

![alt text](docs/images/finish pipelines.jpg)

![alt text](docs/images/finish job.jpg)


## Provisioning
We plan to complement the CI pipeline that we've already built with automation 
for deploying the containers to a Kubernetes cluster hosted on 
[GARR Cloud](https://cloud.garr.it/containers/).

GitLab's integration with Kubernetes should allow us to build an end-to-end 
DevOps process for our application, that starts from a commit pushed to the 
repository and ends with a set of containers running in production.


#Future works