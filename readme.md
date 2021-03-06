﻿# About

Let's experiment with a multicontainer application for Docker, using Docker Compose. 
Or - use Kubernetes with Minikube. Guides provided for both.

First, follow this guide to set up docker & kubernetes locally with minikube.

Then go the next parts of the tutorial:
* https://github.com/rille111/Samples.Containers/tree/master/kubernetes
* https://github.com/rille111/Samples.Containers/tree/master/kubernetes-helm-and-tiller (package/application installer)
* https://github.com/rille111/Samples.Containers/tree/master/kubernetes-ingress-nginx (loadbalacing and http routing)
* https://github.com/rille111/Samples.Containers/tree/master/kubernetes-setup-dns-cert-https
* https://github.com/rille111/Samples.Containers/tree/master/kubernetes-prometheus (monitoring, alerting)

# Building ASP.NET Core on Containers (Docker and Kubernetes)

Nginx is the chosen loadbalancer/proxy and it will proxy incoming user web requests to a web (or directly to the api).
The web in turn, needs to talk to the api. It goes through Nginx to reach that Api. It looks for an environment variable to know wich one, but can respond anyway.

* Frontend LB: Nginx. (image nginx which is based on busybox) for local dev, but for the real production cluster we're gonna use nginx ingress.
* Frontend Web: aspnetcore mvc web app (talks to the backend thru the frontend lb)
* Backend Api: aspnetcore api web app (we're not going to focus much on this)

* The web and api docker builds:
    * Using a linux image (alpine) with .net core sdk to build the app
    * Using another linux image (alpine) with .net core runtime to run the app 
* The front load balancer
    * Using nginx (from image Busybox I think)

See the installation steps below, or jump into the startup sections below if you got that covered already.

## Requirements

* Windows 10 Professional
* Virtualization=Enabled in your BIOS (UPDATE 2021: Use WSL2 for Docker Desktop instead!)
* Hyper-V installed
* Choco (go to chocolatey.org and download it)

# Installation

## Install Docker Desktop for Windows

* I prefer not using choco for this
* Go to https://hub.docker.com/editions/community/docker-ce-desktop-windows
* Create an account and install docker desktop CE edition, for windows
* Login both from the tray icon and in your shell environment
* Try `docker run hello-world` and make sure you get some output
* It should work, otherwise fix all problems

# Startup

## Startup - The .NET project locally

First, clone this repo. Then run the web and see that it starts up. 
Then, startup the api, see the api url and change the url in the web project to this. Refresh web and see that it works.

## Startup - Docker build & run

* Just go to the folder of the appl you want and call build.bat and run.bat
* You can run the NginX container but it wont do anything since it has nothing to route the requests to

## Startup - Docker compose

* Since this is really a multicontainer setup, docker-compose is best.
    * `docker-compose up --build`
* Then go to: http://localhost:8080 for the frontend lb routing to the web
* Then go to: http://localhost:5000 for the backend api without going through frontend lb
* Then go to: http://localhost:3000 for the front web without going through frontend lb, however it will fetch values thru front lb
* You can also run the containers individually with their respective build.bat and run.bat files. Except nginx because it doesn't reach to outside it's container(s). Dunno how to fix.

# Resources

* Udemy course docker: https://www.udemy.com/docker-and-kubernetes-the-complete-guide/
* Microsoft tutorial: https://docs.microsoft.com/en-us/dotnet/core/docker/building-net-docker-images
* To override ports in aspnetcore: https://stackoverflow.com/questions/48669548/why-does-aspnet-core-start-on-port-80-from-within-docker
* Github for config aspnetcore and docker https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-windows.md
* Github sample aspnetapp for docker https://github.com/dotnet/dotnet-docker/tree/master/samples/aspnetapp
* Combine nginx and aspnetcore https://stackoverflow.com/questions/51264577/install-nginx-on-an-existing-asp-net-core-docker-container
* Some nginx upstream examples http://nginx.org/en/docs/http/ngx_http_upstream_module.html#example
* Nginx config beginners guide: http://nginx.org/en/docs/beginners_guide.html#conf_structure
* Nginx docker images: https://hub.docker.com/_/nginx

# Reading
* Understanding Deployments vs StatefulSets
    * https://stackoverflow.com/questions/41583672/kubernetes-deployments-vs-statefulsets/48006210#48006210
    * https://www.mirantis.com/blog/kubernetes-replication-controller-replica-set-and-deployments-understanding-replication-options/
* Understanding Replication Controller, ReplicaSets and Deployments
    * https://www.mirantis.com/blog/kubernetes-replication-controller-replica-set-and-deployments-understanding-replication-options/

# TODO
* Try NLog to get more meaningful logs to EFK-stack
* With Docker: Make the API work with Redis and MySql
* Then the same with k8s
* Expose Grafana and Kibana somehow
