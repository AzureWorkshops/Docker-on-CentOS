## Overview
We have just created our CentOS server.  We now need to apply any available system updates along with installing and configuring Docker to begin working with containers.

## Install Updates
Just like any other operating system, updates are periodically released to support new features and patch any potential security threats.  We will apply the updates first.

  1. If you have not already, connect to your remote CentOS server and login.

  2. From the command prompt, type the following to automatically install all available updates:
  ```bash
  sudo yum update -y
  ```
  3. You'll be required to re-enter your password.

  4. Depending on the number and size of available updates, this process may take a few minutes.  Now would be a good time to take a break.

## Install Docker
We now have an updated CentOS operating system.  We are ready to install Docker.

  1. First, let's remove any remnants of older versions of Docker to ensure that we run the latest version. From the command prompt, type the following:
  ```bash
  sudo yum remove docker docker-common container-selinux docker-selinux docker-engine
  ```  
  > NOTE: You can paste this into PuTTY by _right-clicking_ the terminal screen.

  2. We need to install dependencies for Docker:
  ```bash
  sudo yum install -y yum-utils device-mapper-persistent-data lvm2
  ```
  
  3. Now, we need to tell CentOS _where_ the Docker repository is located. From the command prompt, type (or paste) the following:
  ```bash
  sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
  ```

  4. Now that the new repository has been added, we need to update the `yum` package index:
  ```bash
  sudo yum makecache fast
  ```

  5. We need to query for the latest version of Docker available in the repository:
  ```bash
  yum list docker-ce.x86_64  --showduplicates |sort -r
  ```

  6. You should see outputed list _similar_ to the following:
  ```bash
  docker-ce.x86_64            17.03.1.ce-1.el7.centos             docker-ce-stable
  docker-ce.x86_64            17.03.0.ce-1.el7.centos             docker-ce-stable
  ```

  7. The latest version of Docker will be the top line.  The version we want is listed in the middle column. In this case it's `17.03.1.ce-1.el7.centos`. To install, run the following command replacing `<VERSION>` with the version listed in the center column.
  ```bash
  sudo yum install -y docker-ce-<VERSION>
  ```

  8. Installing the Docker engine may take an additional minute or two.

  9. Map the storage device for Docker to use. We'll need to temporarily promote ourselves to the highest user permission level.
  ```bash
  sudo bash
  mkdir /etc/docker
  echo '{ "storage-driver": "devicemapper" }' > /etc/docker/daemon.json
  exit
  ```

  10. Finally, start Docker:
  ```bash
  sudo systemctl start docker
  ```

## Additional Configuration
To simplify running and managing Docker, there's some additional configuration that we need to implement.  While this section is optional, it is recommended to make managing Docker much easier.

#### Ensure Docker Engine is Running

  1. From the command prompt, type:
  ```bash
  sudo systemctl status docker
  ```

  2. You should see something similar to the following:
  ```bash
  ● docker.service - Docker Application Container Engine
     Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
     Active: active (running) since Sun 2017-06-04 22:38:16 UTC; 4min 10s ago
       Docs: https://docs.docker.com
   Main PID: 32844 (dockerd)
  ```

  3. Because the service is running, we can now use the `docker` command later in this workshop.

#### Enable Docker Engine at Startup
Let's make sure the Docker engine is configured to run on system startup (and reboot).

  1. From the command prompt, type:
  ```bash
  sudo systemctl enable docker
  ```

  2. You should see something similar to the following:
  ```bash
  Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
  ```

#### Elevate Your Privileges
Be default, running the `docker` command requires root privileges - that is, you have to prefix the command with `sudo`. It can also be run by a user in the **docker** group, which is automatically created during the install of Docker.  If you attempt to run the `docker` command without prefixing it with `sudo` or without being in the docker group, you'll get an output like the following:

```bash
docker: Cannot connect to the Docker daemon. Is the docker daemon running on this host?.
See 'docker run --help'.
```

To avoid typing `sudo` whenever you run the `docker` command, add your username to the docker group:

```bash
sudo usermod -aG docker $(whoami)
```

You will then need to log out and back in for the changes to take effect.

If you need to add another user to the `docker` group (one in which you have not logged in as currently), simply provide that username explicitly in the command:

```bash
sudo usermod -aG docker <username>
```

You've successfully installed the Docker engine.  You have also configured it to run at startup and have added yourself to the **Docker** group so that you have sufficient privileges for running Docker.