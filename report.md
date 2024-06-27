# Report over the analysis of DNS tunneling detection using Zeek

Zeek is a network monitoring tool which applies a scripts framework to promote the generation of logs. With the logs generated, it is possible to obtain informations about the network and, thus, identify anomalous traffic. In this sense, this report will focus on the usage of Zeek to detect traffic related to DNS tunnelling. 

## What is DNS tunnelling

This attack is based on sending non-DNS traffic through DNS packets, and this is possible because an attacker creates a DNS server and then leads the victim to connect to it. DNS tunnelling takes advantage over the fact that some organisations do not implement security measures over their DNS traffic.

## Establishing the tunnelling

The simulation of a DNS tunnel was made on a [Mininet-Sec](https://github.com/mininet-sec/mininet-sec/?tab=readme-ov-file#mininet-sec) instance, using a pre defined topology which contains a firewall. [More information about the topology](https://github.com/13917931-project-tasks/mnsec-docs/blob/main/en/activation.md#2-iniciate-mnsec). Due to the ubuntu firewall, some commands were necessary to change the firewall configuration and make possible the establishement of the tunnel.

### Establishing the tunnel

```
mnsecx fw0 iptables -A FORWARD -d 10.0.0.2 -p udp --dport 53 -j ACCEPT

mnsecx srv2 iodined -f -c -P ChangeMe-123 10.199.199.1/24 iodine.hackinsdn.ufba.br 
```

The fist command adds a rule to the firewall allowing incoming UDP traffic on port 53 directed to the IP address 10.0.0.2 to pass through the firewall. 

-c to disable check of client IP/port on each request

-f to keep running in foreground

## Connecting the client

```
iptables -I FORWARD -i s1+ -j ACCEPT
iptables -I FORWARD -i s2+ -j ACCEPT
mnsecx o1 iodine -f -P ChangeMe-123 10.0.0.2 iodine.hackinsdn.ufba.br
```
