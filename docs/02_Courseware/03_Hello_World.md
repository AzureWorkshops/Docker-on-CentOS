## Overview
Now that we have Docker installed, we are able to deploy images as containers.  In this short step of the workshop, we will deploy a couple of small containers as practice.

## Hello World
  1. Ensure you have logged in to your remote CentOS server and are at the prompt.

  2. From the command prompt, type the following:
  ```bash
  docker run hello-world
  ```

  3. You should then see something similar to the following:
  ```bash
  Unable to find image 'hello-world:latest' locally
  latest: Pulling from library/hello-world
  78445dd45222: Pull complete
  Digest: sha256:c5515758d4c5e1e838e9cd307f6c6a0d620b5e07e6f927b07d05f6d12a1ac8d7
  Status: Downloaded newer image for hello-world:latest

  Hello from Docker!
  This message shows that your installation appears to be working correctly.

  To generate this message, Docker took the following steps:
   1. The Docker client contacted the Docker daemon.
   2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
   3. The Docker daemon created a new container from that image which runs the
      executable that produces the output you are currently reading.
   4. The Docker daemon streamed that output to the Docker client, which sent it
      to your terminal.

  To try something more ambitious, you can run an Ubuntu container with:
   $ docker run -it ubuntu bash

  Share images, automate workflows, and more with a free Docker ID:
   https://cloud.docker.com/

  For more examples and ideas, visit:
   https://docs.docker.com/engine/userguide/
  ```

I'll explain what the first couple of lines mean later.  The important thing here is to see that around the 7th line, you'll see the message 'Hello from Docker!' followed by a line informing you that the Docker installation 'appears to be working correctly.'

## Whalesay
Now let's run another fun container.

  1. From the command prompt, type:
  ```bash
  docker run docker/whalesay cowsay 'Azure Rocks!'
  ```

  2. After downloading the dependent images, you should see an ASCII whale with a speech bubble containing the message 'Azure Rocks!'.

This image is based on the old Unix _cowsay_ game.  We're basically running _cowsay_ in a container and telling the whale to say whatever we provide in single quotes.  You can run the command as many times as you'd like and put whatever you'd like the whale to say in single quotes.  Go ahead and give it a try. Notice, that after the first time running the container, the image is no longer downloaded.  More about this in the next section.

