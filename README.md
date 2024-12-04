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




<br>
<br>
<br>







# Adding Rich Rules to Firewalld

*Rich rules in firewalld provide granular control over network traffic, allowing you to define more specific conditions such as source and destination addresses, ports, protocols, and services.*

### 1. Check if firewalld is Running
```yml
sudo systemctl status firewalld
```
  - Explanation: Checks the status of the firewalld service.

### 2. Understand the Syntax of a Rich Rule
  - A rich rule is written in XML format. Here's the basic structure:

```yml

<rule family="ipv4|ipv6">
  <source address="IP_ADDRESS" />
  <destination address="IP_ADDRESS" />
  <port protocol="tcp|udp" port="PORT" />
  <accept|reject|drop />
  <log prefix="LOG_PREFIX" />
</rule>

```
  - Explanation: A rich rule specifies conditions like the source address, destination address, protocol, port, and action (accept, reject, or drop). It can also log traffic.

### 3. Adding a Simple Rich Rule


- Allow incoming TCP traffic on port 8080 from IP 192.168.1.100
```yml
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.1.100" port port="8080" protocol="tcp" accept'
```
  - Explanation: Adds a rule to allow TCP traffic from a specific IP on port 8080.


- List all rules for the public zone

```yml
sudo firewall-cmd --zone=public --list-all
```

### 4. More Complex Rich Rules

- Example 1: Log SSH Attempts
```yml
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" port port="22" protocol="tcp" accept log prefix="SSH-ATTEMPT: "'
```
  - Explanation: Allows SSH traffic from a specific IP range and logs it.

- Example 2: Block Inbound Traffic from an IP
```yml
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.1.100" drop'
```
  - Explanation: Drops all traffic from a specific IP without notifying the sender.

- Example 3: Allow ICMP (Ping) During Specific Hours
```yml
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.1.100" service name="ping" accept time start="09:00:00" end="17:00:00"'
```
 - Explanation: Allows ping traffic from a specific IP only during business hours.

### 5. Making the Rich Rule Persistent
```yml
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.1.100" port port="8080" protocol="tcp" accept' --permanent
```
  - Explanation: Adds a permanent rule (persists after reboot).


### 6. Removing a Rich Rule
```yml
# Remove a previously added rich rule
sudo firewall-cmd --zone=public --remove-rich-rule='rule family="ipv4" source address="192.168.1.100" port port="8080" protocol="tcp" accept'
```
  - Explanation: Removes a specified rich rule.

### 7. Common Rich Rule Examples
  - Allow traffic from a specific IP to a service:

```yml
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.1.50" service name="http" accept'
```


  - Log all SSH traffic:

```yml
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" service name="ssh" accept log prefix="SSH-LOG: "'

```

  - Drop traffic from a specific subnet:

```yml
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.100.0/24" drop'
```

  - Allow HTTP traffic only from a specific IP and log it:

```yml
sudo firewall-cmd --zone=public --add-rich-rule='rule family="ipv4" source address="192.168.2.50" service name="http" accept log prefix="HTTP-ACCESS: "'
```

## Tips for Managing Rich Rules

  - Use Logging Carefully: Avoid excessive logging, as it can fill up system logs quickly.
    
  - Test Before Making Permanent: Test rules temporarily before making them permanent to prevent disruptions.
    
  - Backup Configurations: Always back up your firewall settings before making significant changes.


## Conclusion
*Rich rules in firewalld offer granular control over network traffic, allowing administrators to define rules based on specific conditions like IP addresses, ports, protocols, and even time-based conditions. By understanding and using rich rules, you can create a more secure and customized firewall configuration to suit your network’s needs.*






