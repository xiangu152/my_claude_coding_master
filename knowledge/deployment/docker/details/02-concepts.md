---
title: "Docker 核心概念"
source: "https://docs.docker.com/get-started/docker-concepts/"
version: "latest"
---

# Docker 核心概念

> 原始文档来源：https://docs.docker.com/get-started/docker-concepts/

---

Get started

Docker concepts

The basics

What is a container?
What is a container?

Explanation

Imagine you're developing a killer web app that has three main components - a React frontend, a Python API, and a PostgreSQL database. If you wanted to work on this project, you'd have to install Node, Python, and PostgreSQL.

How do you make sure you have the same versions as the other developers on your team? Or your CI/CD system? Or what's used in production?

How do you ensure the version of Python (or Node or the database) your app needs isn't affected by what's already on your machine? How do you manage potential conflicts?

Enter containers!

What is a container? Simply put, containers are isolated processes for each of your app's components. Each component - the frontend React app, the Python API engine, and the database - runs in its own isolated environment, completely isolated from everything else on your machine.

Here's what makes them awesome. Containers are:

Self-contained. Each container has everything it needs to function with no reliance on any pre-installed dependencies on the host machine.
Isolated. Since containers run in isolation, they have minimal influence on the host and other containers, increasing the security of your applications.
Independent. Each container is independently managed. Deleting one container won't affect any others.
Portable. Containers can run anywhere! The container that runs on your development machine will work the same way in a data center or anywhere in the cloud!
Containers versus virtual machines (VMs)

Without getting too deep, a VM is an entire operating system with its own kernel, hardware drivers, programs, and applications. Spinning up a VM only to isolate a single application is a lot of overhead.

A container is simply an isolated process with all of the files it needs to run. If you run multiple containers, they all share the same kernel, allowing you to run more applications on less infrastructure.

Using VMs and containers together

Quite often, you will see containers and VMs used together. As an example, in a cloud environment, the provisioned machines are typically VMs. However, instead of provisioning one machine to run one application, a VM with a container runtime can run multiple containerized applications, increasing resource utilization and reducing costs.

Try it out

In this hands-on, you will see how to run a Docker container using the Docker Desktop GUI.

Using the GUI Using the CLI

Use the following instructions to run a container.

Open Docker Desktop and select the Search field on the top navigation bar.

Specify welcome-to-docker in the search input and then select the Pull button.

Once the image is successfully pulled, select the Run button.

Expand the Optional settings.

In the Container name, specify welcome-to-docker.

In the Host port, specify 8080.

Select Run to start your container.

Congratulations! You just ran your first container! 🎉

View your container

You can view all of your containers by going to the Containers view of the Docker Desktop Dashboard.

This container runs a web server that displays a simple website. When working with more complex projects, you'll run different parts in different containers. For example, you might run a different container for the frontend, backend, and database.

Access the frontend

When you launched the container, you exposed one of the container's ports onto your machine. Think of this as creating configuration to let you connect through the isolated environment of the container.

For this container, the frontend is accessible on port 8080. To open the website, select the link in the Port(s) column of your container or visit http://localhost:8080 in your browser.

Explore your container

Docker Desktop lets you explore and interact with different aspects of your container. Try it out yourself.

Go to the Containers view in the Docker Desktop Dashboard.

Select your container.

Select the Files tab to explore your container's isolated file system.

Stop your container

The docker/welcome-to-docker container continues to run until you stop it.

Go to the Containers view in the Docker Desktop Dashboard.

Locate the container you'd like to stop.

Select the Stop action in the Actions column.

Additional resources

The following links provide additional guidance into containers:

Running a container
Overview of container
Why Docker?
Next steps

Now that you have learned the basics of a Docker container, it's time to learn about Docker images.

What is an image?

---

404

There might be a mistake in the URL or you might've clicked a link to content that no longer exists. If you think it's the latter, please file an issue in our issue tracker on GitHub.

Create a new issue
Go to the homepage

---

404

There might be a mistake in the URL or you might've clicked a link to content that no longer exists. If you think it's the latter, please file an issue in our issue tracker on GitHub.

Create a new issue
Go to the homepage

---

Get started

Docker concepts

Running containers

Publishing and exposing ports
Publishing and exposing ports

Explanation

If you've been following the guides so far, you understand that containers provide isolated processes for each component of your application. Each component - a React frontend, a Python API, and a Postgres database - runs in its own sandbox environment, completely isolated from everything else on your host machine. This isolation is great for security and managing dependencies, but it also means you can’t access them directly. For example, you can’t access the web app in your browser.

That’s where port publishing comes in.

Publishing ports

Publishing a port provides the ability to break through a little bit of networking isolation by setting up a forwarding rule. As an example, you can indicate that requests on your host’s port 8080 should be forwarded to the container’s port 80. Publishing ports happens during container creation using the -p (or --publish) flag with docker run. The syntax is:

$ docker run -d -p HOST_PORT:CONTAINER_PORT nginx

HOST_PORT: The port number on your host machine where you want to receive traffic
CONTAINER_PORT: The port number within the container that's listening for connections

For example, to publish the container's port 80 to host port 8080:

$ docker run -d -p 8080:80 nginx

Now, any traffic sent to port 8080 on your host machine will be forwarded to port 80 within the container.

Important

When a port is published, it's published to all network interfaces by default. This means any traffic that reaches your machine can access the published application. Be mindful of publishing databases or any sensitive information. Learn more about published ports here.

Publishing to ephemeral ports

At times, you may want to simply publish the port but don’t care which host port is used. In these cases, you can let Docker pick the port for you. To do so, simply omit the HOST_PORT configuration.

For example, the following command will publish the container’s port 80 onto an ephemeral port on the host:

$ docker run -p 80 nginx

Once the container is running, using docker ps will show you the port that was chosen:

docker ps

CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS          PORTS                    NAMES

a527355c9c53   nginx         "/docker-entrypoint.…"   4 seconds ago    Up 3 seconds    0.0.0.0:54772->80/tcp    romantic_williamson

In this example, the app is exposed on the host at port 54772.

Publishing all ports

When creating a container image, the EXPOSE instruction is used to indicate the packaged application will use the specified port. These ports aren't published by default.

With the -P or --publish-all flag, you can automatically publish all exposed ports to ephemeral ports. This is quite useful when you’re trying to avoid port conflicts in development or testing environments.

For example, the following command will publish all of the exposed ports configured by the image:

$ docker run -P nginx

Try it out

In this hands-on guide, you'll learn how to publish container ports using both the CLI and Docker Compose for deploying a web application.

Use the Docker CLI

In this step, you will run a container and publish its port using the Docker CLI.

Download and install Docker Desktop.

In a terminal, run the following command to start a new container:

$ docker run -d -p 8080:80 docker/welcome-to-docker

The first 8080 refers to the host port. This is the port on your local machine that will be used to access the application running inside the container. The second 80 refers to the container port. This is the port that the application inside the container listens on for incoming connections. Hence, the command binds to port 8080 of the host to port 80 on the container system.

Verify the published port by going to the Containers view of the Docker Desktop Dashboard.

Open the website by either selecting the link in the Port(s) column of your container or visiting http://localhost:8080 in your browser.

Use Docker Compose

This example will launch the same application using Docker Compose:

Create a new directory and inside that directory, create a compose.yaml file with the following contents:

services:

  app:

    image: docker/welcome-to-docker

    ports:

      - 8080:80

The ports configuration accepts a few different forms of syntax for the port definition. In this case, you’re using the same HOST_PORT:CONTAINER_PORT used in the docker run command.

Open a terminal and navigate to the directory you created in the previous step.

Use the docker compose up command to start the application.

Open your browser to http://localhost:8080.

Additional resources

If you’d like to dive in deeper on this topic, be sure to check out the following resources:

docker container port CLI reference
Published ports
Next steps

Now that you understand how to publish and expose ports, you're ready to learn how to override the container defaults using the docker run command.

Overriding container defaults

---

404

There might be a mistake in the URL or you might've clicked a link to content that no longer exists. If you think it's the latter, please file an issue in our issue tracker on GitHub.

Create a new issue
Go to the homepage

---

404

There might be a mistake in the URL or you might've clicked a link to content that no longer exists. If you think it's the latter, please file an issue in our issue tracker on GitHub.

Create a new issue
Go to the homepage

---
