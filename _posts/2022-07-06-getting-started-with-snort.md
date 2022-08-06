# Getting started with Snort IDS

**Snort** is a free and open-source **rule-based** Network Intrusion Detection/Prevention System (**NIDS/NIPS**)

# IDS/IPS

An IDS is an intrusion detection system that allows to proactively monitor network or hosts for malicious activities, abnormalities, security threats and incidents.

IPSs are IDSs with additional capabilities to not only detect security threats but also prevent them from occurring.  

IDS/IPS are basically of either of the following 2 categories:

- **Network Based IDS/IPS (NIDS/NIPS)**
- **Host Based IDS/IPS (HIDS/HIPS)**

The ability of an IDS/IPS to detect and or prevent threats depend upon rules. These rules work on either of the 3 principles:

**Signature -** Patterns of known malicious behavior. 

**Behavior -** Comparing ****Normal & Abnormal behavior.

**Policies -** System and Network Policies

# Snort Introduction

**Snort** is a free and open-source **rule-based** Network Intrusion Detection/Prevention System (**NIDS/NIPS**).  Based on the configurations and placement on a network, snort can perform both as a network based detection and prevention system. Snort is a really powerful tool that can be integrated with multiple log correlation platforms and **SIEM** tools like Wazuh and Splunk. The primary features that snort offers include:

- Network Based Intrusion Detection
- Network Based Intrusion Prevention
- PCAP file analyses
- Live traffic capture
- Logging of alerts
- Support for multiple modules
- Integration with tools like **Wazuh**, **Arkime**, **Splunk** & more.

In this guide we will walk through Snort installation on **Kali Linux** (or any other Debian distribution), Snort essential configurations, and a glance at primary Snort modes and features. 

# Installing Snort on Kali Linux

Since Snort is not part of Kali’s APT repository, so installing and configuring snort can be quite cumbersome and frustrating. Thus, we will use Ubuntu’s APT repository to install snort. If you are using Ubuntu, you can directly install snort with “**sudo apt install snort**” command. To install snort on Kali, or some other Debian distribution that doesn’t have snort available, please follow along.

- **Make a backup of current sources.list file**

```bash
sudo mv /etc/apt/sources.list /etc/apt/sources.list.bak
```

- **Remove currently installed Updates**

```bash
find /var/lib/apt/lists -type f -exec rm {} \;
```

- **Download Ubuntu’s sources.list file**

```bash
wget https://gist.githubusercontent.com/ishad0w/788555191c7037e249a439542c53e170/raw/3822ba49241e6fd851ca1c1cbcc4d7e87382f484/sources.list -O /etc/apt/sources.list
```

- **Add Ubuntu’s public keys**

```bash
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 3B4FE6ACC0B21F32
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 871920D1991BC93C
```

- **Update and install snort**

```bash
sudo apt update
sudo apt install snort
```

- Add your network interface and range when prompted

During snort installation,  a prompt will appear to enter your **network interface**, and **network range**. Carefully enter the network interface that you will use to **capture traffic**, and **subnet** of your network here. 

# Snort Configurations

Snort configurations are stored under “**/etc/snort/snort.conf**”. The configuration files contains the path to snort rules and define all values that are required to run snort. Though it contains a lot of functions that can be configured to efficiently work with snort, we will only see the essential configurations here. 

```
**/etc/snort/snort.conf** #default snort configuration file
**/etc/snort/rules/local.rules** #user defined rules
**/etc/snort/rules** #snort rules directory
**/var/log/snort** #default log directory for snort
```

## Important sections in the configuration file:

### #1 “Set network variables”

This section defines our network range. Change the “**any**” variable to your subnet.

```bash
ipvar HOME_NET any 
#change any to your local subnet
```

![Untitled](Getting%20started%20with%20Snort%20IDS%209cda4277a02e481aa80653fbe8d8d18c/Untitled.png)

### #7 “Customize your rule set”

This section defines the snort rules that snort uses for intrusion detection and prevention. For simplicity and getting started with snort, we will not use the default community rules. Therefore, please comment out all community rules. 

![Untitled](Getting%20started%20with%20Snort%20IDS%209cda4277a02e481aa80653fbe8d8d18c/Untitled%201.png)

Any custom rules that we want to add are saved in local rules file, which is stored under “**/etc/snort/rules/local.rules**”. In later articles we will see how to create our own snort rules.

![Untitled](Getting%20started%20with%20Snort%20IDS%209cda4277a02e481aa80653fbe8d8d18c/Untitled%202.png)

## Verify Configurations with Self-Test

Snort offers a self-test feature to verify configurations. The syntax is as follows:

```bash
sudo snort -T -c /path-to-config-file
sudo snort -T -c /etc/snort/snort.conf
```

Here **"-T"** is used for testing configuration, and **"-c"** is locating the configuration file **(snort.conf)**

![Untitled](Getting%20started%20with%20Snort%20IDS%209cda4277a02e481aa80653fbe8d8d18c/Untitled%203.png)

# Snort Modes

Snort has 3 main modes that it supports:

1. **Sniffe**r mode, that allows us to capture live traffic.
2. **Packet logger** mode, that allows to log the captured traffic for later analysis. 
3. **NIDS/NIPS** mode, the primary intrusion detection/prevention mode.

**Note** that every time you start the Snort, it will automatically show the default banner and initial information about your setup. You can prevent this by using the "**-q" (quiet mode)** switch.

Since our primary purpose is to deploy Snort as an Intrusion Detection System, thus we will not look at the other modes in details. 

# Sniffer Mode

The sniffer mode allows us to capture live traffic on our network, much like **Wireshark**. 

| Switch | Description |
| --- | --- |
| -v | Verbose mode, Display TCP/IP output. |
| -d | Display payload data. |
| -X | Display payload data in hex. |
| -e | Display link-layer headers. |
| -i | Network interface. |

![Untitled](Getting%20started%20with%20Snort%20IDS%209cda4277a02e481aa80653fbe8d8d18c/Untitled%204.png)

# Packet Logger Mode

The Packet logger mode allows us to save alerts in log format, which can later be used for analysis on SIEMs like **Wazuh** and **Splunk**. 

| Switch | Description |
| --- | --- |
| -l | Enable logger mode. |
| -K ASCII | Log output in ASCII format.  |
| -r | Read saved output. |
| -n | Log/Process specified number of packets. |

![Untitled](Getting%20started%20with%20Snort%20IDS%209cda4277a02e481aa80653fbe8d8d18c/Untitled%205.png)

**Note** that default snort logs are saved in binary format, which can be exported to Wireshark, Wazuh, Splunk, etc. However, this perk is lost when saving output in **ASCII**. Thus, it is recommended that output is always stored in default format. 

```bash
# save logs in human readble format 
sudo snort -dev -k ASCII -l /path-to-log

#read saved logs (default format only)
sudo snort -r log-file-to-read 
```

# Intrusion Detection/Prevention Mode

Primarily, Snort is built to be used as an **Intrusion Detection and Prevention System**. Snort’s Intrusion Detection/Prevention feature depends upon its deployment on a network . To be used for Intrusion Prevention, snort has to be deployed before a **switch** on the network. 

In this article we will only cover Snort’s Intrusion Detection mode. The following switches can be paired to do so:

| Switch | Description |
| --- | --- |
| -c | Specify the configuration file. |
| -N | Disable logging. |
| -D  | Run in daemon mode in background.  |
| -A | Alert modes: |
|  | full: default mode that  provides all possible information |
|  | fast: provides alert message, timestamp, src and dst ip, and port. |
|  | console: provides fast alerts on terminal |
| -i | Network interface |

```bash
sudo snort -i interface -c /etc/snort/snort.conf -A console
```

![Untitled](Getting%20started%20with%20Snort%20IDS%209cda4277a02e481aa80653fbe8d8d18c/Untitled%206.png)

**Note** that the alerts generated in the above screenshot will not work for you. This is a custom rule in my local files rule. 

## End

That’s it for this article. In the next Snort article we will leverage **Snort as an Intrusion Prevention System** on our local network and detect malicious traffic and anomalies.