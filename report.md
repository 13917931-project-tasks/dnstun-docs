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

The fist command adds a rule to the Mininet-Sec firewall allowing incoming UDP traffic on port 53 directed to the IP address 10.0.0.2 to pass through the firewall. 

The second command promotes the establishement of a DNS tunnel server in *srv2*, which is a server of the Mininet-sec topology. There are 3 iodine parameters being used:

1. -c to disable check of client IP/port on each request;
2. -f to keep running in foreground;
3. -P to define a password used for authentication.

When establishing a DNS tunnel, it is necessary to define a network whose addresses will be used to create new interfaces in the server and in the clients hosts, and these interfaces will be used to promote the communication in the DNS tunnel. Another parameter that is applied is a domain, that is used by the client to establish the tunnel. 

### Connecting the client

```
iptables -I FORWARD -i s1+ -j ACCEPT
iptables -I FORWARD -i s2+ -j ACCEPT
mnsecx o1 iodine -f -P ChangeMe-123 10.0.0.2 iodine.hackinsdn.ufba.br
```

The first 2 commands add a rule to the machine firewall allowing any traffic coming from interfaces starting with "s1" and "s2" to be forwarded, knowing that *s1 and *s2* are switches present in the Mininet-sec topology.

In the third command, a DNS tunnel client is establised in the o1 host of Mininet-Sec topology. In this sense, it is used the IP of the server, that will used by the client establish the connection through DNS requests using the domain *iodine.hackinsdn.ufba.br* and the password defined for authentication. 

In this case, the domain does not exist, however, it is possible to establish a tunnel because the server can recognize requests involving subdomains related to *iodine.hackinsdn.ufba.br* as being request to establish a tunnel. In this sense, in the command is defined that the client will send DNS requests to the IP 10.0.0.2 (which hosts the DNS tunnel server), using subdomains related to *iodine.hackinsdn.ufba.br*.

## Communication in a DNS tunnel

As previously discussed, the firt packets sent in a DNS tunnel are the ones sent by the client to establish the tunnel. They contain requests related to subdomain linked to the domain related to the server, and are sent to the IP related to the server. This traffic is recognized as being DNS normal traffic by Zeek, with a NULL type. However, the sequent packets are classified by Wireshark as being malformed or unknown, and are not detected by Zeek as being part of a DNS traffic.

