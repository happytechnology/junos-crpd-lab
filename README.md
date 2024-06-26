# junos-crpd-lab
Juniper cRPD Lab Setup Guide


## Inital requirments:
* Linux (debian 12) host with docker
* Copy of cRPD image from Juniper Networks
* 2 vCPU & 4GB RAM & 10GB storage per cRPD instance running

This guide was built from Juniper documentation found [here](https://www.juniper.net/documentation/us/en/quick-start/software/crpd/crpd-quick-start/topics/concept/step-1-crpd.html).

The full documentation for cRPD can be found [here](https://www.juniper.net/documentation/product/us/en/crpd/)

## Install vSwitch
To accomplish this lab configuration you need to install or have [ovs](https://www.openvswitch.org/) working with your docker configuration.  OVS allows us to build the network bridge that connects the containers together.  
* Install the OVS .deb to the host `sudo apt-get install openvswitch-switch`
* Change to /usr/bin directory `cd /usr/bin`
* Download ovs-docker code `sudo wget "https://raw.githubusercontent.com/openvswitch/ovs/master/utilities/ovs-docker"`
* Give permissions `sudo chmod a+rwx ovs-docker`

## Install cRPD
Navigate to the Juniper Networks Support page for Junos cRPD: https://support.juniper.net/support/downloads/?p=crpd and click the latest version.  Copy the `.tgz` file to the docker host.  

Next, install the cRPD image to docker `docker load -i junos-routing-crpd-docker-$version.tgz` Now run `docker images` to verify the cRPD image was installed - be sure to note the **TAG id** as you will need this when running the container.

![image](https://github.com/happytechnology/junos-crpd-lab/blob/main/dockerimage.PNG)

Each cRPD container requres two volumes: the config and varlog.  Build these volumes before you can run the container, the volume names will referenced with the `run` command.

`docker volume create crpd01-config` 

`docker volume create crpd01-varlog`

Now you can launch the cRPD instance.  The `-v` volumes need to match the ones you just created and the `-it` needs to match the TAG id from `docker images`

`docker run --rm --detach --name crpd-01 -h crpd-01 --privileged -v crpd01-config:/config -v crpd01-varlog:/var/log -m 2048MB --memory-swap=2048MB -it crpd:23.4R1-S1.5`

`docker ps` will now output the crpd-01 contaier you just built

## Configure OVS Bridge
![image](https://github.com/happytechnology/junos-crpd-lab/blob/main/cRPD%20Lab.png)

The limitation of the number of containers and links is entirely limited by the amount of resources you have available on the docker host.  In this example we will build out one of the four cRPD instances with three links connected to the ovs-bridge. 

First you need to define the bridge, in our example this bridge is called `junos-lab-bridge`

`sudo ovs-vsctl add-br junos-lab-bridge`

Next you need to build the interfaces on the bridge and assign them to the container.

`sudo ovs-docker add-port junos-lab-bridge eth0 crpd-r1`

`sudo ovs-docker add-port junos-lab-bridge eth1 crpd-r1`

`sudo ovs-docker add-port junos-lab-bridge eth2 crpd-r1`

The format is for additional interfaces is  `add-port $bridgename $interfacename $containername`

![image](https://github.com/happytechnology/junos-crpd-lab/blob/main/cRPD%20Lab%20topo.png)

Lastly, we need to assign an IP to each address of the cRPD subsystem.  From the shared topology we'll be assiging addresss to the crpd-r1 continer.

`docker exec -d crpd-r1 ifconfig eth0 172.0.0.1/30`

`docker exec -d crpd-r1 ifconfig eth1 172.0.0.5/30`

`docker exec -d crpd-r1 ifconfig eth2 172.0.0.22/30`

`docker exec -d crpd-r1 ifconfig lo0.0 10.255.255.1 netmask 255.255.255.255`
