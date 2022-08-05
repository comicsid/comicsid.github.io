# Active Response with Wazuh

Wazuh has the ability to prevent incidents in real time or near real time by leveraging it’s active response features. In respect to Wazuh, an **active response** is a script that is configured to execute when a specific alert, alert level, or rule group is triggered. 

Each active response specifies where its associated command will be executed: on the agent that triggered the alert, on the manager, on another specified agent, or on all agents, which also includes the manager(s). The `location` options are:

- `Local`. It runs the script on the agent that generated the alert.
- `Server`. It runs the script on the Wazuh manager.
- `Defined agent`. It specifies the IDs of the agents that run the script regardless of where the event has been observed.
- `All`. Every agent in the environment will run the script. Use with caution.

# Configuring Active Response in Wazuh

- Edit the **ossec.conf** file in Wazuh manager and define the command to initiate an active response.

 

```xml
<active-response>
	<command>command-name</command>
	<location>local</location>    #location (**local, server, defined-agent, or all**)
	<rules_id>xxxx</rules_id>    #execute when this alerts with this rule are logged
	<timeout>x-seconds</timeout> #timeout in seconds
<active-response>
```

Information about the options and more options can be found at Wazuh Documentation. 

- Restart the Wazuh Manager

# Active Response Scripts

Wazuh is preconfigured with the following scripts for Linux.

Located at: `/var/ossec/active-response/bin` 

| Script name | Description |
| --- | --- |
| disable-account | Disables an account by setting passwd-l |
| firewall-drop | Adds an IP to the iptables deny list |
| firewalld-drop | Adds an IP to the firewalld drop list |
| host-deny | Adds an IP to the /etc/hosts.deny file |
| ip-customblock | Custom OSSEC block, easily modifiable for custom response |
| ipfw | Firewall-drop response script created for ipfw |
| npf | Firewall-drop response script created for npf |
| wazuh-slack | Posts modifications on Slack |
| pf | Firewall-drop response script created for pf |
| restart-wazuh | Automatically restarts Wazuh when ossec.conf has been changed |
| route-null | Adds an IP address to null route |
|  |  |

Wazuh is preconfigured with the following scripts for Windows

Located at: `C:\Program Files\ossec-agent\active-response\bin`

| Script name | Description |
| --- | --- |
| netsh.exe | Blocks an ip using netsh |
| restart-wazuh.exe | Restarts wazuh agent |
| route-null.exe | Adds an IP to null route |
|  |  |
|  |  |

# Active Response To Brute Force Attacks

Let’s write an active response rule to respond to a brute force attacks against SSH server. 

The first thing is to look for the rule for which we want to setup an active response. To protect against brute force attacks on our SSH server lets find a suitable rule. 

![                                                                        Rule ID: 5712](Active%20Response%20with%20Wazuh%201ce36d5601b44594aa467195ae32892b/Untitled.png)

                                                                        Rule ID: 5712

![Untitled](Active%20Response%20with%20Wazuh%201ce36d5601b44594aa467195ae32892b/Untitled%201.png)

This rule (**rule id:5712**) generates alert for possible SSH brute force attempts when someone tries to login with non-existent user at least 8 times under 2 minutes. Let’s change this to a frequency of 5 times. 

**NOTE**: Edit the rule file in wazuh agent locally. Editing rules file is not supported in wazuh manager. 

Now  let’s write an active response rule to block the IP of an attacker whenever alerts are generated for this rule. 

```xml
<active-response>
<command>firewall-drop</command>
<location>defined-agent</location>
<agent_id>agent-xxx</agent_id>
<rules_id>5712</rules_id>
<timeout>600</timeout> #in_seconds
<repeated_offenders>30,60,90</repeated_offenders>
</active-response>

```

# Active Response in Action

Lets perform a brute force attack and protect our system by implementing the active response rule we created.