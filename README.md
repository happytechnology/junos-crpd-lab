# junos-crpd-lab
Juniper cRPD Lab Setup Guide


## Inital requirments:
* Linux (debian 12) host with docker
* Copy of cRPD image from Juniper Networks
* 2 vCPU & 4GB RAM & 10GB storage per cRPD instance running

This guide was built from Juniper documentation found [here](https://www.juniper.net/documentation/us/en/quick-start/software/crpd/crpd-quick-start/topics/concept/step-1-crpd.html).

The full documentation for cRPD can be found [here](https://www.juniper.net/documentation/product/us/en/crpd/)

## Open vSwitch
To accomplish this lab configuration you need to install or have [ovs](https://www.openvswitch.org/) working with your docker configuration.  OVS allows us to build the network bridge that connects the containers together.  
* Install the OVS .deb to the host `sudo apt-get install openvswitch-switch`
* Change to /usr/bin directory `cd /usr/bin`
* Download ovs-docker code `sudo wget "https://raw.githubusercontent.com/openvswitch/ovs/master/utilities/ovs-docker"`
* Give permissions `sudo chmod a+rwx ovs-docker`

## Install cRPD
Navigate to the Juniper Networks Support page for Junos cRPD: https://support.juniper.net/support/downloads/?p=crpd and click the latest version.  Copy the `.tgz` file to the docker host.  Next, install the cRPD image to docker `docker load -i junos-routing-crpd-docker-21.2R1.10.tgz` Now run `docker images` to verify the cRPD image was installed - be sure to note the **TAG id** as you will need this when running the container.

Each cRPD container requres two volumes: the config and varlog.  Build these volumes before you can run the container, the volume names will referenced with the `run` command.

`docker volume create crpd01-config` 

`docker volume create crpd01-varlog`

Now you can launch the cRPD instance.  The `-v` volumes need to match the ones you just created and the `-it` needs to match the TAG id from `docker images`

`docker run --rm --detach --name crpd-01 -h crpd-01 --privileged -v crpd01-config:/config -v crpd01-varlog:/var/log -m 2048MB --memory-swap=2048MB -it crpd:23.4R1-S1.5`

`docker ps` will now output crpd-01 you just built
