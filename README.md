# cml-exabgp

This repo contains the configuration to run ExaBGP and the route generation script (Smash Route) in Cisco Modeling Labs 2.x.

```
https://github.com/Exa-Networks/exabgp
```
```
https://github.com/dfex/route-smash
```

## Build Instructions

1.  Choose an Ubuntu type node in the CML UI and add it to your lab topology.

2.  Adjust the interface count to meet your requirements before starting the node.  The first interface should be used for management and the subsequent interfaces for BGP peering.  This cannot be changed later without wiping the node configuration.

3.  Adjust the RAM before starting the node.  This cannot be changed later without wiping the node configuration.  Sizing guidelines are TBD.  32GB is estimated to support a full internet routing table.

4.  Configure the IP addresses for the interfaces before starting the node using the CML "Edit Config" tab for the node.  This cannot be changed later without wiping the configuration.  

	* This example contains cloud-init configuration that should be used in the CML ExaBGP node "edit config" tab.  Adjust the runcmd: section as needed.  

	* ens2 will default to a DHCP client configuration for management and is the first interface.  It is expected that ens2 have a link to a CML external connector in NAT or bridge mode with outbound internet access to retrieve packages.

	* ens3 is the first interface intended for use by BGP and is intended to connect to another router node in CML.  

	* Configure additional interfaces, if necessary

	* Choose "save" before moving on.
	```
	runcmd:
 	- sudo ip address add X.X.X.X/XX dev ens3  
 	- sudo ip link set dev ens3 up
	```
	<img width="886" alt="Screen Shot 2022-03-17 at 14 44 59" src="https://user-images.githubusercontent.com/49030026/158878934-90ad53d5-a075-432f-bc4d-107df5250a04.png">

	Note:  In CML 2.2, the interface names listed in the CML WebUI do not correctly align with the names in the operating system.  Ignore the CML WebUI names.

5.  Execute these commands to update Ubuntu, install the Python package manager (PIP), install ExaBGP, and download a copy of the route-smash script from Github.

	```
 	sudo apt update -y
 	sudo apt install pip -y
 	clear
 	sudo pip install exabgp
 	git clone https://github.com/dfex/route-smash
 	```

6.  Create your own or download an exabgp.conf configuration file.

 	```
 	git clone https://github/w4rfc/cml-exabgp/exabgp.conf
 	```
 	Note:  Carefully coordinate the next-hop defined in smash-route.py and the IP addressing used in exabgp.conf so that routes not hidden by downstream devices under test due to an invalid next-hop.

7.  Generate the exabgp environment configuration file and copy the output to /usr/local/etc/exabgp/exabgp.env.

	```
 	exabgp --fi
	```
	```
 	sudo nano /usr/local/etc/exabgp/exabgp.env
 	```

8.  Change this setting in the env configuration file.
 	```
	[exabgp.api]
	ack = false
	```

9.  To start ExaBGP
 	```
 	sudo exabgp exabgp.conf
  	```
<img width="1610" alt="Screen Shot 2022-03-18 at 15 38 41" src="https://user-images.githubusercontent.com/49030026/159072625-d06938c3-444f-46c2-abc3-e7b9de13582d.png">

<img width="824" alt="Screen Shot 2022-03-18 at 18 03 24" src="https://user-images.githubusercontent.com/49030026/159090870-c311a1d2-5e42-41f2-b060-7b428145fe42.png">
