# gitlab
How to install GitLab using Docker Compose?
Hi. Today I will show you how you can install the GitLab server within an hour and run your first CI/CD process in it.
This article is part of a series on how to get started with the popular CI/CD tools. In this article, I will show you how to install the CI/CD tool and how to prepare the process of building and testing a simple project based on Maven.

What is GitLab?
Gitlab is a tool supporting software development using the Continuous Integration and Continuous Delivery processes. Its main component is the Git version control system. In addition, it provides many functions that support the work of programmers in the continuous building, testing and automatic installation of projects for various environments.

What is Docker Compose?
To find out what Docker Compose is, go to the article: How to install Jenkins using Docker Compose?

Tools required
Before starting work, prepare the required tools. You can find a description of these tools on the website: How to install Jenkins using Docker Compose?

Configuration for Docker Compose
We will start work on the installation by creating a dedicated directory in which we will store data and Gitlab configuration
> mkdir gitlab

For convenience, we will also set an environment variable that will contain the path to our Gitlab directory:

 > export GITLAB_HOME=$(pwd)/gitlab
>

In the next step, we create the docker-compose.yml file with the following content:
> 

# docker-compose.yml
version: '3.7'
services:
  web:
    image: 'gitlab/gitlab-ce:latest'
    restart: always
    hostname: 'localhost'
    container_name: gitlab-ce
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://localhost'
    ports:
      - '8080:80'
      - '8443:443'
    volumes:
      - '$GITLAB_HOME/config:/etc/gitlab'
      - '$GITLAB_HOME/logs:/var/log/gitlab'
      - '$GITLAB_HOME/data:/var/opt/gitlab'
    networks:
      - gitlab
  gitlab-runner:
    image: gitlab/gitlab-runner:alpine
    container_name: gitlab-runner    
    restart: always
    depends_on:
      - web
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - '$GITLAB_HOME/gitlab-runner:/etc/gitlab-runner'
    networks:
      - gitlab

networks:
  gitlab:
    name: gitlab-network

