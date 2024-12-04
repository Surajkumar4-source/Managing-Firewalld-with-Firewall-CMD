# Managing Firewalld with Firewall-CMD

<br>


## Introduction

*firewalld is a dynamic firewall management tool used in Linux systems. It provides a more flexible way to manage firewall rules using zones and services. firewall-cmd is the command-line interface to interact with firewalld. This guide will help you configure, manage, and troubleshoot your firewall effectively.*

## Prerequisites

- Firewalld Installed: Ensure firewalld is installed on your system:
```yml
sudo yum install firewalld -y   # For CentOS/RHEL
sudo apt install firewalld -y   # For Ubuntu/Debian
```
  - Root or Sudo Privileges: Administrative access is required for firewall configuration.


## Firewalld Modes:- Firewalld operates in two modes:

1. Runtime Configuration: Changes apply immediately but revert after reboot.
  
2. Permanent Configuration: Persistent across reboots but require a reload to 



## Zones in Firewalld

*Zones represent predefined trust levels for network connections. Each zone has a specific set of rules that define how incoming and outgoing traffic is handled.*



## **Zone Descriptions**

| **Zone**    | **Description**                                                                 |
|-------------|---------------------------------------------------------------------------------|
| **block**   | Rejects all incoming connections; only outgoing connections are allowed.        |
| **dmz**     | For systems in the Demilitarized Zone; allows selected incoming connections.    |
| **drop**    | Drops all incoming connections without notification; allows outgoing traffic.   |
| **external**| For external networks with NAT masquerading; allows selected incoming traffic.  |
| **home**    | Trusted home network; allows selected incoming connections.                    |
| **internal**| For internal networks with routing; allows selected incoming connections.       |
| **public**  | For untrusted networks; allows selected incoming connections.                   |
| **trusted** | Accepts all network connections.                                               |
| **work**    | Trusted work network; allows selected incoming connections.                    |










<br>
<br>


## Step-by-Step Commands with Explanations and Use Cases

### 1. Activating Firewalld
  - Before using firewall-cmd, ensure firewalld is running.

      - Check the status of firewalld
```yml

sudo systemctl status firewalld
```
  - Explanation: Checks if firewalld is active.

      - Start the firewalld service

```yml

sudo systemctl start firewalld
```
  - Explanation: Starts the firewalld service.

### 2. Checking Available Zones
  - Zones represent levels of trust for network connections.

```yml
# List all available zones
sudo firewall-cmd --get-zones
```
  - Explanation: Displays all predefined zones.

### 3. Viewing Current Zone and Rules
```yml
# Get the default zone
sudo firewall-cmd --get-default-zone
```
  - Explanation: Shows the default zone for new connections.


- List rules for the default zone

```yml
sudo firewall-cmd --list-all
```

  - Explanation: Displays the active rules for the default zone.

### 4. Changing Interface Zones
  - Move an interface (ens33) to a specific zone.


    - Change the zone of an interface (runtime)
```yml
sudo firewall-cmd --zone=dmz --change-interface=ens33
```
  - Explanation: Temporarily moves ens33 to the dmz zone.


    - Make the change permanent

```yml
sudo firewall-cmd --zone=dmz --change-interface=ens33 --permanent
sudo firewall-cmd --reload
```
  - Explanation: Makes the zone assignment persistent.

### 5. Managing Services

  - List all predefined services

```yml
sudo firewall-cmd --get-services
```
  - Explanation: Shows predefined services (e.g., ssh, http).

  #### Adding and Removing Services

 - Remove SSH access in the public zone (permanent)

```yml
sudo firewall-cmd --zone=public --remove-service=ssh --permanent
sudo firewall-cmd --reload
```
  - Explanation: Removes SSH service from the public zone.


- Add SSH access in the public zone (permanent)


```yml
sudo firewall-cmd --zone=public --add-service=ssh --permanent
sudo firewall-cmd --reload
```
  - Explanation: Adds SSH service to the public zone.

### 6. Managing Ports

   1. Remove port 8080 from the public zone
```yml
sudo firewall-cmd --zone=public --remove-port=8080/tcp --permanent
sudo firewall-cmd --reload
```
  - Explanation: Removes port 8080 from the public zone.

 2. Adding a Port
```yml
# Add port 2020 to the public zone
sudo firewall-cmd --zone=public --add-port=2020/tcp --permanent
sudo firewall-cmd --reload
```

  - Explanation: Opens port 2020 in the public zone.



## Conclusion
*Using firewalld and firewall-cmd, you can efficiently manage firewall rules, zones, services, and ports in Linux systems. Regularly audit your rules to enhance security and adapt configurations to your network's trust levels.*

