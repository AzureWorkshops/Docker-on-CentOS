## Overview
The final part of this workshop is to practice exposing a container service outside of Azure. We're going to create a simple web server and access it from our local machine.

## NGINX
We are going to deploying a container hosting NGINX (pronounced "engine X"), a simple, but powerful web server. NGINX is typically used in containerized deployments because it supports autoscaling, service discovery and other capabilities often leveraged in microservice architecture.

Run NGINX by typing the following command:
```bash
docker run -d -p 8080:80 nginx
```

This will download and run NGINX in the background.  As stated earlier in this workshop, we often run services in _detached_ mode (`-d`).  As new parameter that you see here is mapping, or publishing (`-p`), ports  - very similar to a NAT, if you are familiar with the concept. There are two ports specified here separated by a colon.  The first number is the host's port while the second number is the container's port.  So, in essence, we are mapping the host's port 8080 to the container's port 80.  If our container runs multiple services or a service requiring multiple ports, we can also specify a port range.  We could have used the default HTTP port 80 for the host, but for the workshop I chose port 8080 for differentiation between the two environments in pursuit of clarity.

Let's make sure that NGINX is running successfully.
```bash
curl http://localhost:8080
```
Running the previous command, should display some html source code.
```bash
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

## Network Security Group (NSG)
Now that our web server is running, let's make it available outside of Azure.

When we created our CentOS virtual machine, we accepted the defaults, including the default settings for our NSG.  The default settings only allowed SSH (port 22) access. We need to add a rule to our NSG to allow HTTP traffic over port 8080.

  1. If you are not still there, go back to the Azure portal and navigate to the settings of your CentOS virtual machine.

  2. In the left menu, click on **Network interfaces** <img src="https://raw.githubusercontent.com/AzureWorkshops/images/master/icons_network_interfaces.jpg" class="inline"/>.

  3. This will open the _Network Interfaces_ blade for your CentOS virtual machine. Click on the singular, listed interface.

  4. In the left menu, click on **Network security group** <img src="https://raw.githubusercontent.com/AzureWorkshops/images/master/icons_network_security_group.jpg" class="inline"/>.

  5. This will list the currently active NSG.  In our case, it should be the NSG that was created with our virtual machine - **docker-centos-nsg**.  Click on the NSG (**NOTE:** Click on the actual NSG link, **NOT** on **Edit**).

  6. In the left menu, click on **Inbound security roles** <img src="https://raw.githubusercontent.com/AzureWorkshops/images/master/icons_inbound_security_rules.jpg" class="inline"/>.

  7. At the top of the blade, click **Add** <img src="https://raw.githubusercontent.com/AzureWorkshops/images/master/icons_add.jpg" class="inline"/>.

  8. Enter the following configuration:

      * Name: **allow-http**
      * Priority: **1010**
      * Source: **Any**
      * Service: **Custom**
      * Protocol: **Any**
      * Port range: **8080**
      * Action: **Allow**

  9. Click **OK**.

This should only take a couple of seconds.  Once you see the rule added, open a new browser and navigate to the IP address of your CentOS virtual machine, including the port number.  The IP address used in this workshop's screen shots is **52.170.85.112** (your IP address will be different).  Using the aforementioned IP address, I would direct my browser to **http://52.170.85.112:8080**.  Doing so, you should see the NGINX landing page.