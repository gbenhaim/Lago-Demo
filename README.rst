Lago Demo
====================================

About
^^^^^^

In this demo we will learn how to set up a basic environment with Lago.
The environment will consist of three virtual machines that will host Jenkins infrastructure.

The VMs
^^^^^^^

-  "vm0-server" - Jenkins server
-  "vm1-slave" - Jenkins slave
-  "vm2-slave" - Jenkins slave

The network
^^^^^^^^^^^^

The vms will be connected to the same network, There will be also connectivity between the vms host and the internet.

Prerequisite
^^^^^^^^^^^^^

- `Install Lago <http://lago.readthedocs.io/en/latest/README.html#installation>`__ 
- Clone this repository to your machine.

::

    git clone https://github.com/gbenhaim/lago-tutorial.git

Let's start !
^^^^^^^^^^^^^^

From within the cloned repository, run the following commands:

::

    lago init
    
-  Create the environment.
    
::

    lago start
    
-  Start the vms.

::

    lago deploy

-   Installing the vms:
   -  Jenkins will be installed on the server.
   -  OpenJDK will be installed on the slaves.
   
Optional commands:
   
::
    lago shell vm0-server
    
    Opens a shell to vm0-server (for any other vm, just replace 'vm0-server' with the name of the machine)

::

    lago status
    
- Prints some usefull information about the environment.

::

    lago stop
    
- Turns off the vms.

::

    lago destroy
    
- Will delete the vms.

Advanced stuff
^^^^^^^^^^^^^^^

For more advanced stuff please check out `this <http://lago.readthedocs.io/en/latest/index.html>`__ tutorial

