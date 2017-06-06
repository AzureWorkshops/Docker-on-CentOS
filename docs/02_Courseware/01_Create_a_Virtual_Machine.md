## Objective
Now that we've explored the Azure portal a bit, let's get started with creating some resources.  Our primary resource will be a virtual machine on which we install Docker.  Once we create the virtual machine, we'll see that some additional resources are created for us.

## Create a Resource Group
As stated on the previous page, in order to create resources, we need a _Resource Group_ to place them in.

1. If you are not there already, go ahead and click on the **Resource Groups** <img src="https://raw.githubusercontent.com/AzureWorkshops/images/master/icons_resource_groups.jpg" style="display: inline; margin:0px 5px;box-shadow: 2px 2px 2px #999;border:1px solid #ccc;"/> in the Azure Portal to open the Resource Groups blade.

  2. At the top of the Resource Groups blade, click on **Add** <img src="https://raw.githubusercontent.com/AzureWorkshops/images/master/icons_add.jpg" style="display: inline; margin:0px 5px;box-shadow: 2px 2px 2px #999;border:1px solid #ccc;"/>. This will open a panel that asks for some basic configuration settings.

  3. Complete the configuration settings with the following:

      * Resource group name: **azworkshops_docker_centos_demo**
      * Subscription: **_&lt;choose your subscription&gt;_**
      * Resource group location: **_&lt;choose your location&gt;_**

  4. _&lt;Optional&gt;_ Check _Pin to dashboard_ at the bottom of the panel.

  5. Click **Create**.

  6. It should only take a second for the resource group to be created.  Once you click create, the configuration panel closes and returns you to the list of available resource groups.  Your recently created group may not be visible in the list.  Clicking on **Refresh** <img src="https://raw.githubusercontent.com/AzureWorkshops/images/master/icons_refresh.jpg" style="display: inline; margin:0px 5px;box-shadow: 2px 2px 2px #999;border:1px solid #ccc;"/> at the top of the Resource Groups blade should display your new resource group.

**NOTE:** When you create a resource group, you are prompted to choose a location. Additionally, as you create individual resources, you will also be prompted to choose locations. The location of resource groups and their resources can be different.  This is because resource groups store _metadata_ describing their contained resources; and, due to some types of compliance that your company may adhere to, you may need to store that metadata in a different location than the resources themselves.  For example, if you are a US-based company, you may choose to keep the metadata state-side while creating resources in foreign regions to reduce latency for the end-user.

## Create a Virtual Machine
Now that we have an available resource group, let's create the actual CentOS server.

  1. If you are not there already, go ahead and navigate to the **azworkshops_docker_centos_demo** resource group.

  2. At the top of the blade for our group, click on **Add** <img src="https://raw.githubusercontent.com/AzureWorkshops/images/master/icons_add.jpg" style="display: inline; margin:0px 5px;box-shadow: 2px 2px 2px #999;border:1px solid #ccc;"/>. This will display the blade for the _Azure Marketplace_ allowing you to deploy a number of different solutions.

  3. We are interested in deploying a CentOS server. Therefore, in the _Search Everything_ box, type in **CentOS-based**.  This will display a couple of different versions.  Since we want to deploy the latest _stable_ version of CentOS, from the displayed options, choose **CentOS-based 7.3**.
  <img src="../images/centos_server.jpg" style="margin:10px 0px;box-shadow: 2px 2px 2px #999;border:1px solid #ccc;"/>

  4. Choose the image published by "Rogue Wave Software".
  <img src="../images/centos_rogue.jpg" style="margin:10px 0px;box-shadow: 2px 2px 2px #999;border:1px solid #ccc;"/>

  5. This will display a blade providing more information about the server we have chosen. To continue creating the server, choose **Create**.

  6. We are now prompted with some configuration options.  There are 3 sections we need to complete and the last section is a summary of our chosen options.

     1. Basics

        * Name: **docker-centos**
        * VM disk type: **SSD**
        * Username: **localadmin**
        * Authentication type: **Password**   
          (NOTE: You can choose SSH if you are familiar with how to set this up.  If you are not, we will do this in a later workshop.  However, for this workshop, _Password_ authentication is sufficient.)
        * Password: **Pass@word1234**
        * Confirm password: _&lt;same as above&gt;_
        * Subscription: _&lt;choose your subscription&gt;_
        * Resource group: **Use existing** - **azworkshops_docker_centos_demo**
        * Location: _&lt;choose a location&gt;_

     2. Size

        * **DS1_V2**

     3. Settings

        * Use managed disks: **No**
        * Storage account: (click on it & **Create New**)
            
            * Name: **dockercentosdata**_&lt;random number&gt;_  (ex.  _dockercentosdata123456_)  
              (NOTE: This name must be _globally_ unique, so it cannot already be used.)
            * Performance: **Premium**
            * Replication: **Locally-redundant storage (LRS)**
        * Virtual network: _&lt;accept default&gt;_ (e.g. _(new) azworkshops_docker_centos_demo-vnet_)
        * Subnet: _&lt;accept default&gt;_ (e.g. _default (172.16.1.0/24)_)
        * Public IP address: _&lt;accept default&gt;_ (e.g. _(new) docker-centos-ip_)
        * Network security group (firewall): _&lt;accept default&gt;_ (e.g. _(new) docker-centos-nsg_)
        * Extensions: **No extensions**
        * Availability set: **None**
        * Boot diagnostics: **Enabled**
        * Guest OS diagnostics: **Disabled**
        * Diagnostics storage account: (click on it & **Create New**)

            * Name: **dockercentosdiags**_&lt;random number&gt;_  (ex.  _dockercentosdiags123456_)  
            * Performance: **Standard**
            * Replication: **Locally-redundant storage (LRS)**

     4. Summary (just click **OK** to continue)

This machine is relatively small, but with containers, it can still deliver some pretty impressive performance.  Once scheduled, it may take a minute or two for the machine to be created by Azure.  Once it has been created, Azure _should_ open the machine's status blade automatically.

## Connect to the Virtual Machine
Once your machine has been created, we can remotely connect to it using secure shell (SSH).  These instructions assume that you do not have strong familiarity with SSH and/or that you have no built-in SSH client in your local OS.  For this reason, we will be using the PuTTY client we downloaded earlier for the workshop.  However, if you are more comfortable using another Telnet/SSH client (e.g. MacOS, Linux, Windows Sub-Layer (WSL)), please feel free to use it.

#### Get Public IP
  1. If it is not already open, navigate to the **Overview** blade of your newly created virtual machine.

  2. In the top section of the blade, in the right column, you should see a **Public IP address** listed. <img src="../images/ip_address.jpg" style="margin:10px 0px;box-shadow: 2px 2px 2px #999;border:1px solid #ccc;"/>

  3. Copy the IP address.

#### Connect with SSH
  1. Open **PuTTY**.

  2. In the configuration window:  
  
      * Hostname: **_&lt;IP address from previous step&gt;_**
      * Port: **22**
      * Connection type: **SSH**   
<img src="../images/putty_configuration.jpg" style="margin:10px 0px;box-shadow: 2px 2px 2px #999;border:1px solid #ccc;"/>

  3. Click **Open**

  4. In the security prompt, click **Yes**.  
  <img src="../images/putty_security.jpg" style="margin:10px 0px;box-shadow: 2px 2px 2px #999;border:1px solid #ccc;"/>

  5. You will then connect to the remote CentOS server.

  6. Enter the username and password from above (e.g. **localadmin** and **Pass@word1234**, respectively).

  7. You should then see the `bash` prompt:
  ```bash
  [localadmin@docker-centos ~]$
  ```

Congratulations.  You have successfully created and connected to your remote CentOS server in Azure.  You are now ready to install the Docker runtime.