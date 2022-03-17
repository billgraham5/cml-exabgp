# cml-exabgp

This repo contains the configuration to run ExaBGP and the route generation script (Smash Route)in Cisco Modeling Labs 2.x.

```
https://github.com/Exa-Networks/exabgp
```
```
https://github.com/dfex/route-smash
```

## Build Instructions

1.  Choose an Ubuntu type node in the CML UI and add it to your lab topology.

2.  Adjust the interface count to meet your requirements before starting the node.  The first interface should be used for management and the subsequent interfaces for BGP peering.  This cannot be changed later without wiping the configuration.

3.  Adjust the RAM before starting the node.  This cannot be changed later without wiping the configuration.  Sizing guidelines are TBD.

4.  Configure the IP addresses for the interfaces before starting the node using the CML "Edit Config" tab for the node.  This cannot be changed later without wiping the configuration.  
		A) This contains cloud-init configuration.  Adjust the runcmd: section as needed.  
		B) ens2 will deafult to DHCP client for management and is the first interface.  
		C) ens3 is the first interface intended for use by BGP and likely connected to another router node in CML.  
		D) Choose "save" before moving on.
	```
	runcmd:
 	- sudo ip address add X.X.X.X/XX dev ens3  
 	- sudo ip link set dev ens3 up
	```

5.  Execute these commands to update Ubuntu, install PIP, install ExaBGP, and download a copy of the route-smash script.

	```
 	sudo apt update -y
 	sudo apt install pip -y
 	clear
 	sudo pip install exabgp
 	git clone https://github.com/dfex/route-smash
 	```
 
6.  Create your own or download an exabgp.conf configuration file.
 
 	```
 	git clone https://github/jnprbill/cml-exabgp/exabgp.conf
 	```
 	Note:  Carefully coordinate the next-hop defined in smash-route.py and the IP addressing used in exabgp.conf so that routes not hidden by downstream devices under test.
 
7.  Generate the exabgp enviornment configuration file and copy the output to /usr/local/etc/exabgp/exabgp.env.
  
	```
 	exabgp --fi
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
