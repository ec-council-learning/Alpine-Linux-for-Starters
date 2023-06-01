# Alpine Linux for Starters

## Installing Alpine Linux in a Virtual Machine

For production use, you can certainly install Alpine Linux on a normal physical machine.  However, for testing and learning purposes, you might find it handier to install it in a virtual machine.  I normally use VirtualBox, which is a Free Open Source Software (FOSS) virtualization platform that can run on top of Windows, Mac, Solaris, or Linux host machines.  Here's where you can find VirtualBox as well as instructions on how to install it:

[VirtualBox.org](https://www.virtualbox.org/)

You can download an Alpine installation image from [here](https://alpinelinux.org/).

Installing Alpine Linux is straightforward, and is much easier than installing other minimalistic Linux distros such as Slackware or Arch.  Once you've booted from the installation image into the Alpine command prompt, just run the `setup-alpine` command.  Then, it's mainly a matter of answering questions, most of which are fairly self-explanatory.  The one question that isn't concerns the type of installation that you want to perform.  For our purposes, select the `sys` type, which will install the whole operating system onto your disk.

You can find an overview of the installation process [here](https://wiki.alpinelinux.org/wiki/Installation).

# Installing Alpine Linux on a Raspberry Pi

Unlike other Linux distros, Alpine Linux doesn't require you to use any type of disk burning utility to install an operating system image to a MicroSD card.  Instead, just use a normal disk partitioning utility to create the needed partitions on the card, and then unarchive the Alpine Linux tarball onto the card.  After that, boot the Pi device from the MicroSD card, and then run the `alpine-setup` utility.  

You can find more details [here](https://wiki.alpinelinux.org/wiki/Raspberry_Pi) .

## Creating and Modifying User Accounts

It's never good practice to log into a machine as the root user.  Fortunately, the Alpine installer has you create a normal user account for yourself, and automatically adds it to the `wheel` group so that you can have administrative privileges.

If you've ever worked with any other Linux distro, you're likely familiar with using `sudo` to allow yourself or other users to perform administrative tasks.  Alpine Linux uses `doas` instead of `sudo`, because `doas` is a more lightweight alternative.  

Once you've installed the operating system, you can create other user accounts as the need arises.  You can find directions on how to do that [here](https://wiki.alpinelinux.org/wiki/Setting_up_a_new_user).

You can find more details about configuring doas [here](https://www.maketecheasier.com/what-is-install-doas/).

## Updating the Operating System

As with any other operating system, it's important to keep Alpine updated so that you can avoid bug-related security issues.  On Alpine, you can do this with two simple commands:

```
doas apk update

doas apk upgrade
```

If you're used to running other Linux distros, you'll be amazed at how quickly this goes on Alpine.

There are also some optional repositories that you can enable if needed.  Just open the `/etc/apk/repositories` file in your favorite text editor, and uncomment the lines for the optional repositories.  Then, update the operating in order to bring down the list of new packages.

## Installing Packages

To install a software package from the Alpine repositories, just do:

```
doas apk add package_name
```

To remove a package that's been installed, just do:

```
doas apk del package_name
```

## Managing Services with OpenRC

Unlike most other modern Linux distros, Alpine uses the OpenRC init system instead of `systemd`.  This is one of the reasons that Alpine is so light on system resource usage.  There's nothing hard about using it, but it is a bit different from what you're used to using.

To enable a service, do:

```
doas rc-update add service_name
```

To disable a service, do:

```
doas rc-update del service_name
```

To start a service, do:

```
doas rc-service service_name start
```

To stop a service, do:

```
doas rc-service service_name stop
```

Finally, do this to see the status of a service:

```
doas rc-service service_name status
```

## Configuring the Network

You can configure the network as you desire as you install the operating system.  The installer will ask you how you want to set up the network, and you'll just answer the questions.

You can also reconfigure the network after the operating system is installed.  Alpine has several utilities that makes the job easy.

To set a new hostname, do:

```
doas setup-hostname
```

To reconfigure a network interface, do:

```
doas setup-interfaces
```

To create a list of DNS nameservers that you want to use, do:

```
doas setup-dns
```

Once you invoke any of these utilities, you should find that they're mostly self-explanatory.

## Adding a Firewall

Alpine doesn't come with a pre-configured firewall, but you can easily install either `iptables` or `nftables`.  Unless you have a specific need to run the old-fashioned `iptables`, your best bet is to install the newer `nftables`.  That's because `nftables` offers better firewall performance and is easier to configure.

To install and enable `nftables`, do:

```
doas apk add nftables

doas rc-update add nftables
```

Before you start the nftables service, open the `/etc/nftables.nft` file in your text editor and add a directive to open any network ports that you need to open.  Under the `tcp dport 113` line, add this line to open Port 22 for SSH access:

```
tcp dport 22 ct state new accept
```

Save the file, and then start the nftables service, like this:

```
doas rc-service nftables start
```

If desired, you can open multiple network ports with only one directive, like this:

```
tcp dport { 22, 80, 443 } ct state new accept
```

Be sure to reload any new configuration changes that you make, like this:

```
doas rc-service nftables reload
```

## Installing a Graphical Desktop

The Alpine Linux installer doesn't allow you to install a desktop environment as you install the operating system.  But, you can install one after the installation has completed.

If you go to the Alpine wiki, you'll find the manual procedure for installing various different desktop environments.  However, that's a somewhat convoluted process that you don't need to do.  What the Alpine wiki doesn't tell you is that there's a handy utility that does all the work for you.  All you need to do to install a desktop environment is:

```
doas setup-desktop
```

Choose your preferred environment from the list, and hit the `Enter` key.  Once the installation has completed, just reboot the machine.  It's quick and easy, and only takes a few minutes.

Having said that, there is still one good reason why you might want to use the manual procedure.  That is, the `setup-desktop` script only works with the Gnome, KDE Plasma, and XFCE desktops.  The manual procedure also allows you to choose either the LXQt or MATE desktops.

## Upgrading to the Next Version of Alpine

When a new version of Alpine is released, upgrading to it is quick and easy.  For example, let's say that you need to upgrade from Alpine 3.17 to Alpine 3.18.  Just open the `/etc/apk/repositories` file in your text editor, and look for all instances of `v3.17`.  Change them all to `v3.18` and save the file.  Then do:

```
doas apk update

doas apk upgrade --available
```

Then, reboot the machine, and you'll be on the new version.

## Using Security Packages from the Alpine Repository

Let's say that you need to use a security-focused Linux distro to perform your job as an IT security person.  But, you don't need the complexity of something like Kali Linux.  You can easily install security packages from the Alpine repositories.

You can see a list of security-related packages that you might find in the Alpine repositories [here](https://wiki.alpinelinux.org/wiki/Alpine_security).  Bear in mind though that you can only use this page as a guide, because it is now classified as "obsolete" and hasn't been updated in a while.

## Shell Scripting in Alpine

You can create shell scripts for Alpine Linux that can help you automate repetitive tasks.  The main difference with doing it on Alpine is that Alpine uses the `ash` shell, instead of the `bash` or `zsh` shells that other Linux distros use.

The first line in a shell script designates the shell that will be used to interpret the script.  For example, to write a script that will only run with the `ash` shell, the line would look like this:

```
#!/bin/ash
```

This script would run on Alpine, but it won't run on most other Linux distros unless you install the `ash` shell first.  To make the script portable, so that it would run on other Linux distros that don't use ash, make the line look like this:

```
#!/bin/sh
```

The `sh` is normally a symbolic link that points to executable file for whatever the default shell is for your system.

## Installing Wordpress on Alpine

You can find a procedure for installing Wordpress on an Alpine Linux server [here](https://wiki.alpinelinux.org/wiki/WordPress).  Unfortunately, it's another Alpine procedure that you can only use as a guide, because it's for a very old version of Alpine, and has you try to install old versions of the PHP packages that no longer exist in the Alpine repositories.

In the world of PHP and PHP-based content management systems, things change very quickly, and there's no such thing as backwards compatibility between one version and the next.  Whenever a new version of a content management system, such as Wordpress, gets released, it will often require newer versions of the PHP packages.  Also, when new versions of the PHP packages get released, it will often break an existing installation of a content management system.

In the video, I showed you how to modify the procedure from the Alpine wiki to install the newer versions of PHP that are in Alpine 3.17.  Alpine 3.18 is now out, so the procedure might have to be modified again.  (I don't know that for sure, because I haven't yet tried it with version 3.18.)  The bottom line here is that if you ever need to install any kind of PHP-based content management system on an Alpine Linux server, you'll need to use the Alpine wiki page as your guide, and modify it as necessary so that it will work with the current version of Alpine.

## Contrasting Docker and Podman

Docker and Podman are two competing utilities for creating and managing containers and container images.  Each has its own advantages and disadvantages.

The big disadvantage of using Docker is that Docker offers no security for the host machine.  With a default Docker setup, it's trivially easy for any unprivileged users who are members of the `docker` group to make themselves the `root` user on the host machine.  With Podman, that's not possible.

The big disadvantage of Podman is that it doesn't work well with Kubernetes networking.  I mean, there's supposed to be ways to make it work, but I was never able to succeed in making it work.  So, in spite of my normal preference for Podman, we'll use Docker for our demos.  But, to mitigate the security risk to your host machine, install Docker into a virtual machine.

We'll use an Ubuntu Server virtual machine as our Docker workstation.  You can easily use either Docker or Podman on Alpine, but using Kubernetes on Alpine is problematic.  (Again, there's supposedly a way to do it, but I wasn't able to make it work.)  

To build our container images, we can use either Docker or Buildah.  Buildah is a component of the Podman suite, but as you'll see later, you can also use it with Docker.

To install both Docker and Buildah on your Ubuntu Server virtual machine, do:

```
sudo apt install docker.io buildah
```

To make the Kubernetes demos work, you'll need to upload your Docker images to the Dockerhub repository.  So, you'll need to create a free-of-charge Dockerhub account, which you can do [here](https://hub.docker.com/).

## Building an Alpine Container

Although we can't provide a comprehensive course on Docker just now, we can show you some simple examples of how to build a Docker container image.

First, on the Ubuntu Server virtual machine that you set up in the previous exercise, log into your Dockerhub account, like this:

```
docker login
```

To create a new Docker image, create a directory for it within your own home directory.  Create a Dockerfile, which will contain the instructions on how Docker is to build your container image.  A simple example would look something like this:

```
FROM alpine:3.17
RUN apk update && apk upgrade
RUN apk add vim curl nmap nmap-scripts nmap-ncat sslscan arp-scan
```

This tells Docker to create a new image from an existing `alpine:3.17` image, which will get downloaded from Dockerhub.  (You could also replace `alpine:3.17` with `alpine:latest`, in order to use whatever the current version of Alpine is.)  It then installs any available system updates, and then installs some packages that we'll use to perform a few different types of security scans.  From within the directory where this Dockerfile resides, build the new image like this:

```
docker build -t donniet/alpine-security .
```

(Where it says, `donniet`, substitute the login name of your own Dockerhub account.)

In this command, the -t option specifies the tag that you want to assign to the new image. Optionally, you could also have assigned a version number to the image, which would look something like this:

```
docker build -t donniet/alpine-security:1.00 .
```

The `. `at the end of the command tells Docker to look for any additional files within this current working directory.  (Of course, it's not really needed here because there are no additional files for Docker to access.)

Next, upload the new image to your Dockerhub account.  As before, substitute your own Dockerhub username for my own.  If you didn't assign a version number to your image, the command would look like this:

```
docker push donniet/alpine-security
```

If you did assign a version number, you'll need to include it in the command, or else Docker won't be able to find the image.  So, the command would look like this:

```
docker push donniet/alpine-security:1.00
```

Now, create an interactive container, like this:

```
docker run -it donniet/alpine-security ash
```

Once the container comes up, you'll be able to invoke the scanner programs that you installed when you created the image.  When you've finished, type exit to exit the container.  To stop and remove the container, you'll first need to obtain the container ID, which would look something like this:

```
donnie@ubuntu2204:~$ docker ps -a
CONTAINER ID   IMAGE                                 COMMAND                  CREATED         STATUS                          PORTS     NAMES
6eb0271a87a6   donniet/alpine-security               "ash"                    5 minutes ago   Exited (0) About a minute ago             eager_mclaren

donnie@ubuntu2204:~$
```

What's cool about this, is that you don't need to type the entire CONTAINER ID number in to invoke the stop and remove commands.  Just type in enough of the ID number to let Docker know that it's a unique ID number, and you'll be good.  So, our stop command would look like this:

```
donnie@ubuntu2204:~$ docker stop 6e

6e

donnie@ubuntu2204:~$
```

To remove the container, do this:

```
donnie@ubuntu2204:~$ docker rm 6e

6e

donnie@ubuntu2204:~$
```

You can also use the `buildah` utility to create your Docker containers.  This allows you to run `buildah` commands from within a shell script, instead of building a `Dockerfile`.  To demo, create another directory within your own home directory and `cd` into it.  Then, create the alpine_demo.sh shell script, which will look something like this:

```
#!/bin/bash

buildah from alpine
buildah run alpine-working-container apk update
buildah run alpine-working-container apk upgrade
buildah run alpine-working-container apk add nodejs npm curl
buildah config --workingdir /sr/scr/app/ alpine-working-container
buildah run alpine-working-container -- npm init -y
buildah run alpine-working-container -- npm install express --save
buildah copy alpine-working-container HelloWorld.js 
buildah config --entrypoint "node HelloWorld.js" alpine-working-container
buildah commit alpine-working-container donniet/alpine-demo:v1
buildah push donniet/alpine-demo:v1
```

As before, replace `donniet` with the login ID of your own Dockerhub account.

This script tells buildah to create an image that contains everything you need to run a simple Javascript app.  To see this work, create the `Helloworld.js` file, and make it look like this:

```
const express = require('express') 
const app = express() 
const port = 3000 
app.get('/', (req, res) => res.send('Hello World!')) 
app.listen(port, () => console.log(`Example app listening on port ${port}!`))
```

Add, the executable permission to the shell script:

```
chmod u+x alpine_demo.sh
```

Invoke the shell script to build and automatically upload the image:

```
./alpine_demo.sh
```

Now, spin up a new container from this image, exposing Port 8081 to the outside world:

```
docker run -d -p 8081:3000 donniet/alpine-demo:v1
```

Use curl to verify that the app works:

```
curl localhost:8081
```

You should see the "Hello World!" message pop up.

When you've finished, stop and remove the container as I've already shown you.

## Scanning an Alpine Container

It's important to scan your containers for security vulnerabilites.  There are several tools that you can use, such as Grype.

There's already a lot of good existing documentation on how to install and use Grype.  So, rather than trying to re-invent the wheel, I'll just refer you to [this article](https://www.howtogeek.com/devops/how-to-find-vulnerabilities-in-containers-and-files-with-grype/).

## Understanding Kubernetes

Kubernetes is an orchestration tool that allows you to divide your container workloads into pods.  Instead of having one big container that runs all of the services that your app needs, you can install each service into a different pod.  This allows you to more easily scale out your application across a network, and to more easily keep the everything updated.  Instead of having to rebuild everything in order to update just one service, you can just update that one service and leave everything else alone.  Kubernetes also allows you to create pod clusters with replica pods, which provides fault tolerance and an easy way to update your app without having to take the app offline.

Before you can install Kubernetes on your Ubuntu Server virtual machine, you'll need to edit the `user@.service` unit file.  Open the file in your editor like this:

```
sudo systemctl edit --full user@.service
```

Find this line:

```
Delegate=pids memory
```

Change it to:

```
Delegate=pids memory cpu
```

Next, perform these steps to install the `kubectl `component of Kubernetes:

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"

echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

kubectl version --short

kubectl version --short --output=yaml

kubectl version --short --output=json
```

To create the cluster control node, we'll use Minikube.  Install it like this:

```
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube

minikube start --container-runtime=containerd
```

It takes a long time for Minikube to come up, so be patient.  Once it's up, you'll be ready to create your first cluster.

## Orchestrating Alpine Containers with Kubernetes

We'll divide this section into several subsections.

### Create a Deployment

Create a deployment for the `alpine_demo:v1` image that we created in the previous section:

```
kubectl create deployment alpine-demo --image=donniet/alpine-demo:v1
kubectl get deployments
```

Note that by default, this proxy listens on Port 8001.

To verify it works, first use another terminal window to log into the virtual machine.  In that window, run the `kubectl proxy`, like this:

```
kubectl proxy
```

Go back the first terminal window, and run these commands:

```
curl http://localhost:8001/version
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/
```

Leave the proxy running in the second window to perform the next set of steps.

### Explore the app

Observe the pods that were created by the deployment:

```
kubectl get pods
kubectl describe pods
```

Verify that the pod works:

```
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/
kubectl logs $POD_NAME
kubectl exec $POD_NAME -- env
kubectl exec -ti $POD_NAME -- sh
cat HelloWorld.js
curl localhost:3000
exit
```

### Create a Service to Expose the App to the Outside Network

In Kubernetes, a "service" is a mechanism for allowing the host machine's network to access a pod's IP address.  Create and verify the service like this:

```
kubectl get pods
kubectl get services
kubectl expose deployment/alpine-demo --type="NodePort" --port 3000
kubectl get services
kubectl describe services/alpine-demo
export NODE_PORT=$(kubectl get services/alpine-demo -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT
curl $(minikube ip):$NODE_PORT                
kubectl describe deployment
kubectl get pods -l app=alpine-demo
kubectl get services -l app=alpine-demo
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')
echo Name of the Pod: $POD_NAME
kubectl label pods $POD_NAME version=v1
kubectl describe pods $POD_NAME
kubectl get pods -l version=v1
```

When you've finished, delete the service, like this:

```
kubectl delete service -l app=alpine-demo
kubectl get services
curl $(minikube ip):$NODE_PORT
kubectl exec -ti $POD_NAME -- curl localhost:3000
```

(We'll create a new service in the next section.)

### Scale Up the App

Now, let's scale up by creating some pod replicas.  That way, if one pod crashes, we'll still have others that can handle the load.  Let's start by creating four replicas:

```
kubectl get deployments
kubectl get rs
kubectl scale deployments/alpine-demo --replicas=4
kubectl get deployments
kubectl get pods -o wide
kubectl describe deployments/alpine-demo
```

Create and test the service:

```
kubectl expose deployment/alpine-demo --type="NodePort" --port 3000
kubectl describe services/alpine-demo
export NODE_PORT=$(kubectl get services/alpine-demo -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT
curl $(minikube ip):$NODE_PORT
```

If you invoke the curl command multiple times, you'll hit a different replica each time.  (You can't really tell, so you'll have to take my word for it.)

Okay, we've decided that we don't really need four replicas.  So, let's scale down to only two replicas:

```
kubectl scale deployments/alpine-demo --replicas=2
kubectl get deployments
kubectl get pods -o wide
```

### Updating the App

First, open the `alpine_demo.sh` script in your text editor.  Change both instances of `alpine_demo:v1` to `alpine_demo:v2`.  Save the file and run the script.

Now, perform the upgrade:

```
kubectl get deployments
kubectl get pods
kubectl describe pods
kubectl set image deployments/alpine-demo alpine-demo=donniet/alpine-demo:v2
kubectl get pods
```

Note that as each pod replica gets updated, there will always be other replicas that are running the app.  So, service is never interrupted.

Now, let's test:

```
kubectl describe services/alpine-demo
export NODE_PORT=$(kubectl get services/alpine-demo -o go-template='{{(index .spec.ports 0).nodePort}}')
echo NODE_PORT=$NODE_PORT
curl $(minikube ip):$NODE_PORT
kubectl rollout status deployments/alpine-demo
kubectl describe pods
```

it should show that everything is all good.

Now, what would happen if someone were to accidentally deploy an update that doesn't exist?  Let's see:

```
kubectl set image deployments/alpine-demo alpine-demo=donniet/alpine-demo:v10
kubectl get deployments
kubectl get pods
kubectl describe pods
```

You should see an error message about the non-existent update.  That's okay, because you still have other running replicas that can carry the load.  Finally, let's correct our mistake by rolling back the non-existent update:

```
kubectl rollout undo deployments/alpine-demo
kubectl get pods
kubectl describe pods
```

Everything should now be back to normal.

There's still a lot more to Kubernetes than what we've been able to show you, but that's okay.  We've at least been able to give you a taste of how you can use Alpine Linux as a base for your containers, and how to orchestrate them with Kubernetes.
