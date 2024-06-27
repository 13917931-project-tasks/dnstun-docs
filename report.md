# Report over the analysis of DNS tunneling detection using Zeek

Zeek is a network monitoring tool which applies a scripts framework to promote the generation of logs. With the logs generated, it is possible to obtain informations about the network and, thus, identify anomalous traffic. In this sense, this report will focus on the usage of Zeek to detect traffic related to DNS tunnelling. 

## What is DNS tunnelling

This attack is based on sending non-DNS traffic through DNS packets, and this is possible because an attacker creates a DNS server and then leads the victim to connect to it. DNS tunnelling takes advantage over the fact that some organisations do not implement security measures over their DNS traffic.

## Establishing the tunnelling

The simulation of a DNS tunnel was made on a (Mininet-Sec)[https://github.com/mininet-sec/mininet-sec/?tab=readme-ov-file#mininet-sec] instance
