## Overview
We've successfully deployed a couple of containers into our Docker engine.  In this section, we'll dig a little deeper into working with and interacting with containers.

## Listing Images
As we experienced with running the _Whalesay_ container multiple times, the actual image was only downloaded, uncompressed and built once.  On all subsequent executions, a new container was simply instantiated based on the image.  Docker keeps a local repository of images currently in use; and, those images cannot be deleted until all dependent containers have been deleted.

To view a list of currently downloaded images, type the following in the command prompt:
```bash
docker images
```

The output should look similar to the following:
```bash
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              48b5124b2768        4 months ago        1.84kB
docker/whalesay     latest              6b362a9f73eb        2 years ago         247MB
```

There's a few things that are reported to us here.

First, we see the repository, including the namespace, of the image.  The `hello-world` is what we would consider a _library_ image.  In other words, it's an image that, for lack of a better way to describe it, is 'built-in' to Docker.  For `whalesay`, we see that the repository is `docker`.  We'll talk more about repositories below.

The second column shows us the current tag of the image.  We look at tagging in the next workshop section.

The third column displays a unique id of the image.  Just so that you know, we can refer to the image in our commands throughout the exercise by the full name as it's listed under `REPOSITORY`, the image id, or simply use the first 3 characters in the image id (e.g. `hello-world` could also be referenced by `48b5124b2768` or `48b`).  The thing is, the minimum is 3 characters, but if you have multiple images that have the same first 3 characters, you may need to use a couple of more until you reach a differentiator.

The created column does not report when you downloaded the image locally.  Instead, it reports the date of when the image was created by its owner/designer.  This is a great column for DevOps to use when quickly trying to determine when a particular image was created.

The size column reports the total size of the image - a sum of all layers comprised to make the image.

## Docker Repositories/Registries
A registry is a service - public or private - from which images can be hosted and pulled by other users.  Images are stored in these repositories under namespaces which are, typically, usernames or organizational names.

In the above example, the `whalesay` image is located under the `docker` namespace.  This means that the image belongs to and is managed by the Docker organization.

Microsoft's public registry is hosted by Docker.  You can visit Microsoft's registry at [https://hub.docker.com/u/microsoft/](https://hub.docker.com/u/microsoft/).  As you view the available images (which are only available for Windows-based machines), you'll see that each begin with `microsoft/`.  If we were to pull an image created and maintained by Microsoft into our local Docker instance, we would see the image prefaced by that namespace.

## Inspecting Images
There are a couple of ways we can get some greater details about our images.  We can view the underlying metadata of our image; and, we can see the build history of the image.

#### Image Inspection (Metadata)
To view the metadata of an image, we'll need to _inspect_ it.  From the command prompt, type the following:

```bash
docker image inspect docker/whalesay
```

You should see something that begins with the following:
```bash
[
    {
        "Id": "sha256:6b362a9f73eb8c33b48c95f4fcce1b6637fc25646728cf7fb0679b2da273c3f4",
        "RepoTags": [
            "docker/whalesay:latest"
        ],
        "RepoDigests": [
            "docker/whalesay@sha256:178598e51a26abbc958b8a2e48825c90bc22e641de3d31e18aaf55f3258ba93b"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2015-05-25T22:04:23.303454458Z",
        "Container": "5460b2353ce4e2b3e3e81b4a523a61c5adc238ae21d3ec3a5774674652e6317f",
        "ContainerConfig": {
            "Hostname": "9ec8c01a6a48",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ENV PATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Image": "5d5bd9951e26ca0301423625b19764bda914ae39c3f2bfd6f1824bf5354d10ee",
            "Volumes": null,
            "WorkingDir": "/cowsay",
            "Entrypoint": null,
            "OnBuild": [],
            "Labels": {}
        },
        "DockerVersion": "1.6.0",
        ...
```

Take a moment to examine the metadata. As you look through this information, you will find various attributes that describe the image.  

#### Image History
The last bit of information that the `inspect` command reported was a set of layers:
```bash
    "Layers": [
        "sha256:1154ba695078d29ea6c4e1adb55c463959cd77509adf09710e2315827d66271a",
        "sha256:528c8710fd95f61d40b8bb8a549fa8dfa737d9b9c7c7b2ae55f745c972dddacd",
        "sha256:37ee47034d9b78f10f0c5ce3a25e6b6e58997fcadaf5f896c603a10c5f35fb31",
        "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
        "sha256:b26122d57afa5c4a2dc8db3f986410805bc8792af3a4fa73cfde5eed0a8e5b6d",
        "sha256:091abc5148e4d32cecb5522067509d7ffc1e8ac272ff75d2775138639a6c50ca",
        "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef",
        "sha256:d511ed9e12e17ab4bfc3e80ed7ce86d4aac82769b42f42b753a338ed9b8a566d",
        "sha256:d061ee1340ecc8d03ca25e6ca7f7502275f558764c1ab46bd1f37854c74c5b3f",
        "sha256:5f70bf18a086007016e948b04aed3b82103a36bea41755b6cddfaf10ace3c6ef"
    ]

```
Other than the layer id's, this doesn't really tell you much.  To see the actual build history of the image, type the following command:
```bash
docker image history docker/whalesay
```
You'll will then see something similar to the following:
```bash
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
6b362a9f73eb        2 years ago         /bin/sh -c #(nop) ENV PATH=/usr/local/bin:...   0B
<missing>           2 years ago         /bin/sh -c sh install.sh                        30.4kB
<missing>           2 years ago         /bin/sh -c git reset --hard origin/master       43.3kB
<missing>           2 years ago         /bin/sh -c #(nop) WORKDIR /cowsay               0B
<missing>           2 years ago         /bin/sh -c git clone https://github.com/mo...   89.9kB
<missing>           2 years ago         /bin/sh -c apt-get -y update && apt-get in...   58.6MB
<missing>           2 years ago         /bin/sh -c #(nop) CMD ["/bin/bash"]             0B
<missing>           2 years ago         /bin/sh -c sed -i 's/^#\s*\(deb.*universe\...   1.9kB
<missing>           2 years ago         /bin/sh -c echo '#!/bin/sh' > /usr/sbin/po...   195kB
<missing>           2 years ago         /bin/sh -c #(nop) ADD file:f4d7b4b3402b5c5...   188MB
```
Keep in mind that these are _layers_.  So the way to read this is from bottom-up, meaning that the last row is actually the _first_ layer.  Then, as you go up the list, additional layers are applied.  The bottom layer is usually a base image, such as an OS and, because it's the OS, is usually larger in size.  Then, actions or commands are applied to that image that make up the additional layers.  Some of the last actions to be applied to this image were running an install script and setting some environment variables.

Let's take a look at another example.  Download another image to your local Docker engine by typing the following command:
```bash
docker image pull a11smiles/softcover
```

This is an image stored under my personal namespace in the public Docker registry.  Softcover is a ruby-based application used to build digital books for various platforms.  Running this command will take a few minutes, but, as you will observe, there's quite a few layers to this image.

Once this image download and reconstruction has completed, check out the history of the image:
```bash
docker image history a11smiles/softcover
```
You will see all of the commands, which include installing multiple dependency libraries for Softcover (e.g. `apt-get install ...`), issued to build the image. Some of these dependencies are quite large, ranging from 815kB to almost 4GB.  Image the time it would require to download all of these requirements manually.  This doesn't include the time it would take to properly and consistently configure them for each developer and/or environment.  With the Softcover container, in this case, simply download the image and run the container with supplying your book's source to create a digital publication.  This can be easily wired up in an automated build process.

## Listing Containers
Not only can we list our local images, but we can also list our local, instantiated containers.  

#### Running Containers
Type in the command line:
```bash
docker ps
```
You should see some like the following:
```bash
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
This tells us that no containers are currently running.  
#### Non-running Containers
We can see previously ran containers by typing in the following:
```bash
docker ps -a
```
This will render a report similar to the following:
```bash
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                    PORTS               NAMES
a883ff18a967        docker/whalesay     "cowsay Hola!"      26 hours ago        Exited (0) 26 hours ago                       sad_goldstine
baa0591c4392        hello-world         "/hello"            26 hours ago        Exited (0) 26 hours ago                       jovial_raman
```
You may see more depending on how many times you instantiated the `whalesay` image.  The important thing to see from this report is the _Status_ of containers.  All containers, in this report, have exited and are no longer running.

One thing to note is that even though the containers have finished executing, they have **not** been deleted.  This allows you to enter into a stopped container.  One reason this may be useful is if an application has thrown an exception and quit, you may want to enter into the container to check out some log files or something of that nature to debug the application.  Once you're done with the container, you must delete it.  We will see how to do this in the next page of the workshop.

## Running a Container
We've already instantiated a couple of images.  We do this by the following command:
```bash
docker run <namespace/image>
```
There are two primary types of processes - short-running and long-running.

#### Short-running Processes
Short-running process are just like the images we've already ran - `hello-world` and `whalesay`.  Another example is the `softcover` image.  A short-running process typically runs a single command, performing a single function, then exits.  This function could be displaying a message, sending an email, or building an application or other resource (e.g. a book, help documentation, etc).

Every time we we run a short-running process, using the above command another container is instantiated.  This is why a new container was created ever time you ran `whalesay`.

#### Long-running Processes
Long-running processes are typically service-based applications (e.g. web servers, service buses, queues, etc.).  We can also run an OS as a container.  Try typing in the following:
```bash
docker run -it centos /bin/bash
```
This command will will download an `centos` image and run the `bash` shell in _interactive_ mode (`-i`) by opening up a _terminal_ (`-t`).  When this runs, you will be automatically placed inside of the running CentOS container (notice the change in the command prompt (e.g. something similar to `root@<container id>:/# ` )). If you are familiar with CentOS, feel free to take a look around. To exit the container, simply type in `exit`.  This will return you to your host machine.

Let's now run CentOS in _detached_ mode. Instantiating a container in interactive mode assumes we are going to interact with the running container.  However, typical long-running processes (like web servers) don't require command line interaction.  We'll run a web server later in this workshop.  However, for the moment, let's mimic a long-running process by running CentOS in an infinite sleep mode.  Execute the following command:
```bash
docker run -d centos sleep inf
```
Again, this will run a CentOS container in _detached_ mode (`-d`) and set it to sleep infinitely.

Let's see the container running in the background by typing in:
```bash
docker ps
```
You should see something like:
```bash
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
3fbc6b362e55        centos              "sleep inf"         2 seconds ago       Up 2 seconds                            loving_liskov
```
You'll see under the _Status_ column, that it's reporting an up-time for the container. We don't typically run commands against long-running processes, but just for the practice, issue the following command (substitute the container id with your id):
```
docker exec 3fb ls
```
This will run a `list` command against the current directory _inside_ of the container and return a list of subfolders.

While the container is running, let's look at one more interesting thing. At the command prompt, type the following:
```bash
ps aux
```
This will list all of the currently running processes.  As you scroll and look at the background processes, you should see a line similar to the following. The process id, memory consumption, etc. may be different but try to find the last column.
```bash
root     55515  0.0  0.0   4380   664 ?        Ss   01:39   0:00 sleep inf
```
What you see here is that container processes are exposed to the host kernel and have access to host system resources.

Now let's stop the container.  Technically, we could kill the process using traditional Linux (UNIX) commands, but this would leave our Docker engine in an unclean state and would require us to do some additional clean up.  Let's stop the container using Docker commands.
```bash
docker stop 3fb
```
(NOTE: Again, `3fb` is the first 3 characters of my container's id.  You'll need to use the id assigned to your container.)

Running `docker ps` again should show you that no more containers are currently running.

#### Re-running a Stopped Container
There will be times when you may need to re-run a stopped container. Find one of the stopped 'whalesay' containers by typing the following:
```bash
docker ps -a
```
My output looks like the following:
```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
3fbc6b362e55        centos              "sleep inf"              2 minutes ago       Up 2 minutes                                   loving_liskov
997f2f96a9dc        centos              "/bin/bash"              3 minutes ago       Exited (0) 3 minutes ago                       stupefied_curran
521376937c77        docker/whalesay     "cowsay 'Azure Roc..."   4 minutes ago       Exited (0) 4 minutes ago                       frosty_kilby
571f6e48e14c        hello-world         "/hello"                 5 minutes ago       Exited (0) 5 minutes ago                       pensive_rosalind
```

By typing the following command, I can re-run my `whalesay` container _without_ instantiating a new container and without having to supply any parameters.  Basically, I can re-run the container as it was originally ran.
```bash
docker start -a 521
```
(NOTE: Again, `521` is the first 3 characters of the stopped `whalesay` container's id.)

With this command, we are restarting a stopped container and attaching (`-a`) to the container's output (STDOUT/STDERR) to view any messages.  In our case, the message is a simple whale with a speech bubble.

Wow, congratulations! You just successfully completed what may seem like a crash course in managing containers.  However, there's really not much more to it than this.  As with anything, the more you play with Docker, the more familiar and confident you become with it.