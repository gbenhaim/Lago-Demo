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

All the VMs will connect to the same virtual network .
# add some explanation of the network that will be used, that it's a range on
# 192.168.20x.x and such

#### Creating the working directory:

In order to create a working directory, clone the following repo:

https://github.com/gbenhaim/lago-tutorial

#### What is in the repository?

* init.json – This is the file which describes the structure of our environment: the specification of the vms, networks, the path and name of the deployment scripts for each vm.

* presets -  This directory contains default configuration files for common enviornments.
# ^^ this looks like the same as init.json in the root of the repo

* templates-repo – This directory contains .json files which specify the template repository that should be used. (The templates repository is the place from which the base “qcow2” virtual disks will be copied from).
# add also, that it's a special case, and by default it will use the file from
# http://templates.ovirt.org/repo/repo.metadata, and that you can specify urls
# also as --template-repo-path

* deployment-scripts – This directory contains the deployment scripts for each vm (in our case a script that will
install the jenkins server)

#### Editing init.json.in

The init.json.in is where we need to describe our environment e.g vms and network, so Lago can create them for us.

//TODO: elaborate about the different features and values that can be specified within the file.

#### Creating The Environment

From within the repo run:

```
lago init --template-repo-path=templates-repo/template-repo.json lago-work-dir init.json.in
```
This directory /lago-work-dir will contain the files of our new Lago environment.
This directory shouldn't exist before invoking lago init.
# if using the LagoInitFile, the default lago workdir (.lago), and the default
# repo, you can run instead:
#     lago init


#### Deploy the VMs

cd into /lago-work-dir.
# If you used the default workdir path, you can skip the above cd (careful with
# the relative script paths though
From now on, each command that relates to the environment,
should be run from within it.
# with the default workdir you can run from it's parent, or any 'sibiling', for
# example:
#    lago init
#    mkdir lolo
#    cd lolo
#    lago status
#

Now, lets start the vms:

```
lago start
```
Or for a specific vm named "server":

```
lago start server
```

The command below will run the deployment scripts (from within the vms) that were specified
in the init.json.in file.
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
You can write down to yourself the ip adresses of the server and slaves,
as we will need them when configuring the server.

# add also that you can extract the info formatted with json or yaml (check the
# --out-format option)

### Interacting with the VMs

Lago allows you to connect to the vms via ssh.
for exmaple, if we have a vm named "server" we will use the following:

```
lago shell server
```
If the deployment scripts run successfuly we don't have 
to connect to the machines.

# Add also the lago console command, is useful when the vm failed to boot, to
# see the boot process or troubleshoot network issues

### Adding the Jenkins slaves

Open your browser and enter to the Jenkins web UI.
The address should be like: "put-your-server-ip-here:8080"
In the UI do the following:

* Go to Manage jenkins >> Manage nodes
* Click on: New node
* Enter a name for the new slave (you can pick whatever name you like) and mark "Dumb Slave", now hit OK
* Enter "/jenkins" in "Remoote Root Directory" (This is were Jenkins will place his files in the slave)
* Enter the slave's ip in "Host"
* Near the "Credentials" label, click on "add"
* Enter: Username = "root", Password = "123456" - this is the root password of the vms. for more information
  about configuring the root password with Lago, check out [Lago's website](http://lago.readthedocs.org/en/latest/README.html)
* hit the "Save" button
* Repeat the process for the other slave.

# a future improvement might be to autoadd the slave from jenkins cli or
# similar from the deploy scripts (though not sure how complicated that might
# be, as the deploy command does run all of it in parallel based on the vm)

Your server is now configured with the new slaves.

### Shutdown the environment

In order to send a shutdown signal to the machines we will use:
# I don't think it does a real shutdown, iirc it hard-destroys the vms, might
# be a good idea though to add a lago command to soft-shutdown the vms, or even
# reboot.

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





