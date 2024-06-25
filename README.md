# junos-crpd-lab
Juniper cRPD Lab Setup Guide


## Inital requirments:
* Linux (debian 12) host with docker
* Copy of cRPD image from Juniper Networks
* 2 vCPU & 4GB RAM & 10GB storage per cRPD instance running

This guide was build from Juniper documentation found [here](https://www.juniper.net/documentation/us/en/quick-start/software/crpd/crpd-quick-start/topics/concept/step-1-crpd.html).

The full documentation for cRPD can be found [here](https://www.juniper.net/documentation/product/us/en/crpd/)

## Open vSwitch
To accomplish this lab configuration you need to install or have [ovs](https://www.openvswitch.org/) working with your docker configuration.  OVS allows us to build the network bridge that connects the containers together.  
* Install the OVS .deb to the host `sudo apt-get install openvswitch-switch`
* Change to /usr/bin directory `cd /usr/bin`
* Download ovs-docker code `sudo wget "https://raw.githubusercontent.com/openvswitch/ovs/master/utilities/ovs-docker"`
* Give permissions `sudo chmod a+rwx ovs-docker`

## Install cRPD
Navigate to the Juniper Networks Support page for Junos cRPD: https://support.juniper.net/support/downloads/?p=crpd and click the latest version.
