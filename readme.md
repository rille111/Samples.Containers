## Building ASP.NET Core on Containers (Docker and Kubernetes)

Let's experiment with a multicontainer application for Docker, using Docker Compose. 
Or - use Kubernetes with Minikube. Guides provided for both.

Nginx is the chosen loadbalancer/proxy and it will proxy incoming user web requests to a web (or directly to the api).
The web in turn, needs to talk to the api. It goes through Nginx to reach that Api. It looks for an environment variable to know wich one, but can respond anyway.

* Frontend LB: Nginx. (image nginx which is based on busybox)
* Frontend Web: aspnetcore mvc web app (talks to the backend thru the frontend lb)
* Backend Api: aspnetcore api web app

* The web and api docker builds:
    * Using a linux image (alpine) with .net core sdk to build the app
    * Using another linux image (alpine) with .net core runtime to run the app 
* The front load balancer
    * Using nginx (from image Busybox I think)

See the installation steps below, or jump into the startup sections below if you got that covered already.

# Requirements

* Windows 10 Professional
* Virtualization=Enabled in your BIOS
* Hyper-V installed
* Choco (go to chocolatey.org and download it)

# Starting the Containers / Pods

## Startup - Docker build & run

* Just go to the folder of the appl you want and call build.bat and run.bat
* You can run the NginX container but it wont do anything since it has nothing to route the requests to

## Startup - Docker compose

* Since it's a multicontainer setup docker-compose is best.
    * `docker-compose up --build`
* Then go to: http://localhost:8080 for the frontend lb routing to the web
* Then go to: http://localhost:5000 for the backend api without going through frontend lb
* Then go to: http://localhost:3000 for the front web without going through frontend lb, however it will fetch values thru front lb
* You can also run the containers individually with their respective build.bat and run.bat files. Except nginx because it doesn't reach to outside it's container(s). Dunno how to fix.

## Startup - Kubernetes

* go to /kubernetes folder and run apply-all.bat
* run `kubectl get pods` and verify there is one there
* run `kubectl get services` and verify there is one port there
* run `minikube ip` to find out the ip
* go to `http://YOURIP:32001` the port is configured in client-node-port.yaml

# Installation

## Install Docker Desktop for Windows

* I prefer not using choco for this
* Go to https://hub.docker.com/editions/community/docker-ce-desktop-windows
* Create an account and install docker desktop CE edition, for windows
* Login both from the tray icon and in your shell environment
* Try `docker run hello-world` and make sure you get some output
* It should work, otherwise fix all problems

## Install Kubernetes locally on Windows 10

* First, Create a network switch in hyper-v. (Ref: https://medium.com/@JockDaRock/minikube-on-windows-10-with-hyper-v-6ef0f4dc158c)
    * name it `minikube_network_switch`
* Then follow below steps  (Ref: https://kubernetes.io/docs/tasks/tools/install-minikube/#windows)
    * CD to the C:\ drive
    * run `choco install minikube kubernetes-cli`
    * IMPORTANT!! restart shell/commandprompt - not only refreshenv!!!
    * CD to the C:\ drive
    * run `minikube start --vm-driver hyperv --hyperv-virtual-switch "minikube_network_switch"`
    * it should work, otherwise fix all problems. 
* Then, fix a bug (mentioned in  https://medium.com/@JockDaRock/minikube-on-windows-10-with-hyper-v-6ef0f4dc158c)
    * Stop minikube image in hyper-v
    * Right click - Settings - Memory
    * Turn off dynamic and give it a solid 2gb or something
    * Restart it

Problems?
* Uninstall with `choco uninstall minikube`
* Open Hyper-V and delete the minikube image
* Delete everything from:
    * %User%\.kube
    * %User%\.minikube
* Go through the above steps again
* Or read and do this: https://github.com/kubernetes/minikube/issues/822

More info:
* You can connect to the VM and login with user: **docker**   password: **tcuser**
* Shutdown the image that is running in Hyper-V: `sudo shutdown -r now -f`
* Shut down the cluster from a shell (Not from within the Hyper-V image!!) with `minikube stop`


## Resources

* Udemy course docker: https://www.udemy.com/docker-and-kubernetes-the-complete-guide/
* Microsoft tutorial: https://docs.microsoft.com/en-us/dotnet/core/docker/building-net-docker-images
* To override ports in aspnetcore: https://stackoverflow.com/questions/48669548/why-does-aspnet-core-start-on-port-80-from-within-docker
* Github for config aspnetcore and docker https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/aspnetcore-docker-windows.md
* Github sample aspnetapp for docker https://github.com/dotnet/dotnet-docker/tree/master/samples/aspnetapp
* Combine nginx and aspnetcore https://stackoverflow.com/questions/51264577/install-nginx-on-an-existing-asp-net-core-docker-container
* Some nginx upstream examples http://nginx.org/en/docs/http/ngx_http_upstream_module.html#example
* Nginx config beginners guide: http://nginx.org/en/docs/beginners_guide.html#conf_structure
* Nginx docker images: https://hub.docker.com/_/nginx