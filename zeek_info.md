# Zeek Info

## Introduction

- Zeek is a network tracking software, which can be used to obtain more information the traffic ongoing, through logs, and, thus, Zeek can be a helpful resource to analyze anomalies in traffic. In this sense, Zeek is powered by analyzers, which define protocols and network patterns to track, and by scripts, that basically tell Zeek what to do when facing a certain pattern of traffic. The scripts can define actions and functions that have to be executed when a certain event happens, for instance; 
- A Zeek event is triggered when a certain pattern of traffic is detected. For example, whenever a message related to the DNS protocol is detected, this triggers the *dns_message* event. In this sense, Zeek events can be mentioned in scripts in order to promote the execution of certain actions and functions when a specific event is triggered;

## Creating a new script in Zeek

- Zeek documentation has plenty of information on the topic of creating [new scripts](https://docs.zeek.org/en/master/scripting/basics.html);
- If you want to develop a new script, you can create a new folder in the directory `/usr/local/zeek/share/zeek/policy/protocols`, and place your script in there. A zeek is script is a `.zeek` file.
- Then, to make Zeek recognize your script, you sould change the `local.zeek` file, located in the `/usr/local/zeek/share/zeek/site/` directory, and add some lines, like it is shown in the following example:

```
# Write a brief description of your script
@load protocols/<your_folder_name>/<your_script_name>
```

- You should **not** write the `.zeek` extension while adding tour script to `local.zeek` file.

## Creating a new analyzer

- The generation of logs in Zeek is related to analyzers which parse packets to detect a certain protocol. From this parsing, it is possible to determine events related to a protocol, and they can be mentioned in scripts to provoke the execution of some actions following their triggering, including the generation of logs;
- In this sense, the parsing of packets is realized by Spicy, a parser generator whose deep integration with Zeek simplifies the creation of new analyzers;
- You can read more information about Zeek analyzers in [Zeek's documentation](https://docs.zeek.org/en/master/devel/spicy/getting-started.html) and also in [Spicy's documentation](https://docs.zeek.org/projects/spicy/en/latest/getting-started.html).

To create a new analyzer in Zeek, you can run the following command:

``` 
zkg create --features=spicy-protocol-analyzer --packagedir MyProtocol
```

Where *MyProtocol* is just a mock name for the analyzer.

Then, you will have to make some configurations, like in the following example:


```c++
"package-template" requires a "name" value (the name of the package, e.g. "FooBar" or "spicy-http"): 
name: MyProtocol
"package-template" requires a "analyzer" value (name of the Spicy analyzer, which typically corresponds to the protocol/format being parsed (e.g. "HTTP", "PNG")): 
analyzer: myprotocol
"package-template" requires a "protocol" value (transport protocol for the analyzer to use: TCP or UDP): 
protocol: TCP
"package-template" requires a "unit_orig" value (name of the top-level Spicy parsing unit for the originator side of the connection (e.g. "Request")): 
unit_orig: my_unit
"package-template" requires a "unit_resp" value (name of the top-level Spicy parsing unit for the responder side of the connection (e.g. "Reply"); may be the same as originator side): 
unit_resp: my_unit
```

After that, you should change the files in the `analyzer` file, in order to define the parameters of parsing as well as defining events and confirm the activation of the protocol. It is essential to read the comments present in the files to understand which changes make in each file. You should also change the `scripts/main.zeek` file, in order to define logs over the events defined.

Then, you can compile the `.evt` and `.spicy` file in a binary and executable `.htlo` using spicyz:

```
spicyz -o myprotocol.htlo myprotocol.spicy myprotocol.evt
```

After that, you can read files using tour custom protocol, like in the following example:

```
zeek -Cr traffic_file.pcap myprotocol.htlo main.zeek
```

You can also install your custom analyzer in order to apply it in live network analysis usin the following command:

```
zkg install /directory/to/your/analyzer
```

Make sure you are using the most recent version of zkg. Zkg stands for Zeek Packet manager, which "makes it easy for Zeek users to install and manage third party scripts as well as plugins for Zeek and ZeekControl" [{1}](https://github.com/zeek/package-manager#zeek-package-manager).
