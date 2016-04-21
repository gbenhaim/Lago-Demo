# lago-tutorial
This repository contains the default files for creating Lago environment. 

In this tutorial you will learn how to set up a custom virtual environment with Lago.
The virtual environment will be used as an infrastructure for your application, so you can run tests
in order to check the functionality of your application, without the overhead of setting up an infrastructure.

This tutorial will be “hands on”, and will take you through the process of creating, running and deploying the environment.

Lago environments are flexible and can be used for all different kinds of tasks. For keeping things simple I will focus on one scenario, although you will get the ability to create your own custom environment.
We will use only the basic mandatory commands of Lago, for further reading please advise: 
[Lago Hompage](http://lago.readthedocs.org/en/latest/index.html)

#### Prerequisites:

Install Lago - follow [this](http://lago.readthedocs.org/en/latest/README.html) tutorial for more information

#### The scenario:

Create a Lago environment which will consist of three virtual machines that will host Jenkins infrastructure.

#### The VMs:

*  "vm0-server" - Jenkins server
*  "vm1-slave" - Jenkins slave
*  "vm2-slave" - Jenkins slave

#### The network:

All the VMs will connect to the same virtual network.
Lago allows for a creation of 10 different lans ranging from: 192.168.200.x to 192.168.209.x
The subnet will be automatically assigend to the virtual network bridge.

#### Creating the working directory:

In order to create a working directory, clone the following repo:

https://github.com/gbenhaim/lago-tutorial

#### What is in the repository?

* init.json – This is the file which describes the structure of our environment: the specification of the vms, networks, the path and name of the deployment scripts for each vm.

* presets -  This directory contains default configuration files for common enviornments.
# ^^ this looks like the same as init.json in the root of the repo

* templates-repo – This directory contains .json files which specify the template repository that should be used. (The templates repository is the place from which the base “qcow2” virtual disks will be copied from).
By default Lago will use the file from: [http://templates.ovirt.org/repo/repo.metadata]

* deployment-scripts – This directory contains the deployment scripts for each vm (in our case a script that will
install the Jenkins server)

#### Editing init.json

The init.json is where we need to describe our environment e.g vms and networks, so Lago can create them for us.

//TODO: elaborate about the different features and values that can be specified within the file.

#### Creating The Environment


We can use two different approches:

Manual Configurations:

```
lago init --template-repo-path=templates-repo/template-repo.json lago-work-dir init.json
```
(This should be invoked from within the repo)

* The directory /lago-work-dir will contain the files of our new Lago environment.
This directory shouldn't exist before invoking lago init.
* --template-repo-path, for specifing the path to the template-repo file.
* init.json, the name of the file which describes our environment.

Auto Configurations

```
lago init
```
This command will use the following default configurations:
* The command will serch for a file named "LagoInitFile" that will be used as the init file.
* The workdir will be named ".lago"
* The [http://templates.ovirt.org/repo/repo.metadata](default template repo file) will be used.

Note: by default the init.json file is configured with a relative path from /lago-work-dir to the deployment scripts.
If you are using the Auto configuration, or give any other name to the workdir, you should edit the relative path.

#### Deploy the VMs

When using the manual configurations all the commands should be invoked from /lago-work-dir or one of
it's sub-directories.

When using the default workdir you can invoke commands from it's parent, or any 'sibiling', for example:
```
lago init
mkdir lolo
cd lolo
lago status
```

Now, lets start the vms:

```
lago start
```
Or for a specific vm named "server":

```
lago start server
```

The command below will run the deployment scripts (from within the vms) that were specified in the init.json file.
This command should be invoked from the directory that will satisfied the relative path to the deployment scripts
as mentiod in the init.json file.

so, if you used the "manual configurations", the command should be invoked from /lago-work-dir.

```
lago ovirt deploy
```
Jenkins will be installed on the server.
OpenJDK will be installed on the slaves.

### Getting the state of the environment

You cae get information about the state of the enviorment with:

```
lago status
```
Or for a formated output as json or yaml:

```
lago --out-format [json | yaml] status
```

You can write down to yourself the ip adresses of the server and slaves,
as we will need them when configuring the server.

### Interacting with the VMs

Lago allows you to connect to the vms via ssh.
for exmaple, if we have a vm named "server" we will use the following:

```
lago shell server
```
If the deployment scripts run successfuly we don't have 
to connect to the machines.

In case of a failure, you can access the vms via console.
This is useful when the vm failed to boot or when trubleshooting network issues.

```
lago console server
```

### Adding the Jenkins slaves

Open your browser and enter to the Jenkins web UI.
The address should be like: "put-your-server-ip-here:8080"
In the UI do the following:

* Go to Manage Jenkins >> Manage nodes
* Click on: New node
* Enter a name for the new slave (you can pick whatever name you like) and mark "Dumb Slave", now hit OK
* Enter "/jenkins" in "Remoote Root Directory" (This is were Jenkins will place his files in the slave)
* Enter the slave's ip in "Host"
* Near the "Credentials" label, click on "add"
* Enter: Username = "root", Password = "123456" - this is the root password of the vms. for more information
  about configuring the root password with Lago, check out [Lago's website](http://lago.readthedocs.org/en/latest/README.html)
* hit the "Save" button
* Repeat the process for the other slave.

Your server is now configured with the new slaves.

### Stop the environment

In order to stop the machines (brute shutdown) we will use:

```
lago stop
```
Or for a specific vm named "server":

```
lago stop server
```

### Removing the enviornment

The following command will remove all the files
that relates to the environment.

```
lago destroy
```

### Summary

This was a basic introduction on how to use Lago.
For further reading, or contributing to the project, please check the following links:

[Lago on github](https://github.com/lago-project/lago/)

[Lago's website](http://lago.readthedocs.org/en/latest/index.html)
