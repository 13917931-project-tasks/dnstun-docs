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

- Zeek also has a wide about creating analyzers. Zeek has a deep integration with Spicy parser generator 

