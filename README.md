# OVH CLI V2

## How to use

#### 1 - Download executable

* Windows : Soon
* MacOs : Soon
* Linux : Soon


#### 2 - Get a consumer key
In order to access to your account, the app need your authorization. You have to get "a consumer key" and record it as an environement variable.

"Keep calm and carry on", the app will do the job for you, just run it and follow instructions :

On Linux and MacOs run app with :

	./ovh
	
On windows with :

	./ovh.exe
	

## Avalaible commands
We will consider Linux|MacOs version, just replace *ovh* by *ovh.exe* if you are using Windows.

* WORDS in uppercase are variables
* words in lower cases are parts of the command to be executed.
* []: optionnal
* word...: means one or multiple

You can at any moment get help by adding the --help flag.

Example :

	$ ./ovh fw --help
	NAME:
   		ovh fw - Firewall commands

	USAGE:
   		ovh fw [subsection] command [command options] [arguments...]

	COMMANDS|SUBSECTION:
   		list			List IPs, of a given block, that are under firewall.
   		add				Add an IP of IPBLOCK on firewall.
   		remove			Remove an IP of IPBLOCK from firewall.
   		getProperties	Get properties of an IP on the firewall.
   		update			Update an IP on the firewall.
   		addRule			Add a new rule on an IP.
   		removeRule		Remove a firwall rule.
   		help, h			Shows a list of commands or help for one command

	OPTIONS:
   		--help, -h	show help
   
* IP
	* List IP blocks
* Firewall
	* List IP under firewall
	* Add an IP on the firewall
	* Remove an IP from firewall 
	* Get properties of an IP
	* Update properties of an IP
	* Add a rule on an IP	
  
## IP
#### List IP blocks
	./ovh ip list [--flag...]
Will return your IP blocks

You can use filter flags :

* --desc: by descrition 
* --ip: by IP 
* --routedTo: by routing
* --type: by type (all|cdn|dedicated|failover|hosted_ssl|housing|loadBalancing|mail|pcc|pci|private|vps|vpn|vrack|xdsl)

Example: if you want IP blocks attached to dedicated server, run the command :

	./ovh ip list --type dedicated
	
 
 
## FIREWALL
All commands concerning firewall start with :

	./ovh fw
	
#### List IPs of an IP block which are under firewall

	./ovh fw list IPBLOCK
	
Where :

* IPBLOCK : an ip block given by "ovh ip list"

Response : Return a list of IPV4, one per line. Or error.	

Example :
	
	./ovh fw list 176.31.189.121/32
	176.31.189.121	
	
#### Add an IP on firewall

	./ovh fw add IPBLOCK IPV4
	
Where :

* IPBLOCK : an ip block given by "ovh ip list"
* IPV4 : an IP v4 from IPBLOCK	

Response : "Done!" if the command succeed an error otherwise.
	
Example :

	./ovh fw 176.31.189.121/32 176.31.189.121 add
	Done!	

#### Remove an IP from firewall

	./ovh fw remove IPBLOCK IPV4

Where :

* IPBLOCK : an ip block given by "ovh ip list"
* IPV4 : an IP v4 from IPBLOCK	

Response : "IPV4 removed from firewall" if the command succeed an error otherwise.	
	
Example :
	
	./ovh ip fw 176.31.189.121/32 176.31.189.121 remove
	176.31.189.121 removed from firewall
		
#### Get Properties of a firewalled IP
	
	./ovh fw getProperties IPBLOCK IPV4 

Where :

* IPBLOCK : an ip block given by "ovh ip list"
* IPV4 : an IP v4 from IPBLOCK		

Response : Properties on success, one per line. Error otherwise.

Example 

	./ovh fw getProperties 176.31.189.121/32 176.31.189.121
	Ip: 176.31.189.121
	Enabled: false
	State: ok				

#### Update IP properties

	./ovh fw update IPBLOCK IPV4 [--flag...]

Where :

* IPBLOCK : an ip block given by "ovh ip list"
* IPV4 : an IP v4 from IPBLOCK		

Available flags :

* --enabled bool: enabled|disabled IP on firewall 


Response : "Done!" if the command succeed. An error if not.

Example :
	
	ovh fw update 92.222.14.249/32 92.222.14.249 --enabled true
	Done!
	
#### List firewall rules

	ovh fw listRules IPBLOCK IPV4 [--state creationPending|ok|removalPending]	
	
With:

* IPBLOCK : an ip block given by "ovh ip list"
* IPV4 : an IP v4 from IPBLOCK	

Flags:

 * --state: State of the rule (creationPending|ok|removalPending)
 
Example:

	ovh fw listRules 92.222.14.249/32 92.222.14.249 --state ok
	1
	0	 	
	
#### Add a firewall rule

	 ovh fw addRule IPBLOCK IPV4 [--flag...]
	 
With:

* IPBLOCK : an ip block given by "ovh ip list"
* IPV4 : an IP v4 from IPBLOCK	

Flags:

 * --action: Action on this rule (deny|permit). Required.
 * --sequence: Sequence number of your rule. Required.
 * --protocol: Network protocol (ah|esp|gre|icmp|ipv4|tcp|udp). Requiered.
 * --fromPort: Source port for your rule. Only with TCP/UDP protocol.
 * --fromIp: Source ip for your rule. Any if not set.
 * -- toPort: Destination port for your rule. Only with TCP/UDP protocol.
 * --tcpFragment: Can only be used with TCP protocol (true|false).
 * --tcpOption: Can only be used with TCP protocol (established|syn)

Examples :	 
Add a rule	which deny all incoming udp traffic:
	 
	  ovh fw addRule 92.222.14.249/32 92.222.14.249  --sequence 0 --action deny --protocole udp
	  
Add a rule which allow connection from IP 46.105.152.56 to port 22 (SSH)

	ovh fw addRule 92.222.14.249/32 92.222.14.249 --sequence 0 --action permit --toPort 22 --fromIp 46.105.152.56/32 --protocol tcp  

Add a rule wich deny any connection to port 22 (SSH)

	ovh fw addRule 92.222.14.249/32 92.222.14.249 --sequence 1 --action deny --toPort 22 --protocol tcp
	
Will add a rule wich deny any connection to port 22 (SSH).

Rules are tested from sequence 0 to sequence n. When a rule matches it is applied and no other rules are tested. That mean with those examples that only IP 46.105.152.56 will be able to connect thru SSH to IP 92.222.14.249.
	  	
	
#### Remove a firewall rule

	 ovh fw removeRule IPBLOCK IPV4 SEQUENCE

With :

* IPBLOCK : an ip block given by "ovh ip list"
* IPV4 : an IP v4 from IPBLOCK	
* SEQUENCE : Seqeunce number of the rule
	 
Example :

	ovh fw removeRule 92.222.14.249/32 92.222.14.249 1
	Done!

	
#### Get info about a firewall rule
	
	ovh fw getRuleProperties IPBLOCK IPV4 SEQUENCE
	
With :

* IPBLOCK : an ip block given by "ovh ip list"
* IPV4 : an IP v4 from IPBLOCK	
* SEQUENCE : Sequence number of the rule

Response : Formatted rule (see example) or error.

Example :

	ovh fw getRuleProperties 92.222.14.249/32 92.222.14.249 1
	Sequence: 1
	Created: 2014-06-02 07:49:42 +0000 UTC
	Protocol: tcp
	FromIp: 46.105.152.55/32
	FromPort:
	ToIP: 92.222.14.249/32
	ToPort: eq 23
	Action: permit
	Rule:permit tcp 46.105.152.55/32 92.222.14.249/32 eq 23 syn
	State: ok
	TcpOption: syn
	Fragments: false



	
### SPAM
#### List spamming IP of an IP block

	ovh spam listIp IPBLOCK [--state (blockedForSpam|unblocked|unblocking)]	

With :

* IPBLOCK : an ip block given by "ovh ip list"

Flags:

* --state (optional) : current state of IP to list
	* blockeForSpam : Currently blocked for spam
	* unblocked : previously blocked
	* unblocking : an unblocking quiery have been made
	
Response : A list of IPv4 (one per line) or error.

Example :

	$ ovh spam listIp 178.33.223.32/28 --state unblocked
	178.33.223.41
	178.33.223.42
	178.33.223.43


	
#### Get more info about a spamming IP

	ovh spam getProperties IPBLOCK IPV4 
	
With :

* IPBLOCK : an ip block given by "ovh ip list"
* IPV4 : an IP v4 from IPBLOCK	

Response : Formated info (see example) or error

Example :

	ovh spam getProperties 91.121.228.135/32 91.121.228.135
	Blocked since (duration sec):0
	Last time: 2013-12-28T21:51:37+01:00
	IP: 91.121.228.135
	State: unblocked
	

 		
#### Unblock a blocked IP

	ovh spam unblock IPBLOCK IPV4

With :

* IPBLOCK : an ip block given by "ovh ip list"
* IPV4 : an IP v4 from IPBLOCK	

Response : "Done!" on success or error.

Exemple :
	
	ovh spam unblock 178.33.223.32/28 178.33.223.42
	ok
			 		

#### Get statistics about a spamming IP for a time period

	ovh spam getStats IPBLOCK IPV4 --from UNIX_TIMESTAMP_START --to UNIX_TIMESTAMP_STOP 
	
With :

* IPBLOCK : an ip block given by "ovh ip list"
* IPV4 : an IP v4 from IPBLOCK		

Flags

* --from: Unix timestamp representing the begining of the peiod (required).
* --to: Uni timestamp representing the end of the period (required).

Response : formated statistics (see example) or error

Example :

	ovh spam getStats 178.33.223.32/28 178.33.223.42 --from 1385251200 --to 1387882630
	Blocked for the last time: 12 Dec 13 22:52 +0100
	Number of emails sent: 141
	Number of spams sent: 124
	Average score: 521

	Detected Spams :


	Date: 12 Dec 13 20:49 +0100
	Message ID: 7847419050.QUJ8UNYQ543146@qcjeu.iiirdkehxxspb.va
	Destination IP: 91.121.228.128
	Score: 553

	Date: 12 Dec 13 20:49 +0100
	Message ID:
	Destination IP: 91.121.228.128
	Score: 303

	Date: 12 Dec 13 20:49 +0100
	Message ID:
	Destination IP: 91.121.228.128
	Score: 303
	
#### Get IP which are currently blocked for spam (helper)

	./ovh spam getBlocked
	
Response : List of blocked IP, one per line or error

Example :

	./ovh spams getBlocked
	178.33.223.41
	178.33.223.42
	178.33.223.43	
	
## WARNING : commands below are not (re)implemented yet		
	
## SMS
#### List SMS services

	./ovh sms listServices	

Response : list of SMS services, one per line, or Error

Example :
	
	./ovh sms listServices	
	sms-st2-1
	
#### Create a new SMS job (eg send a sms)

	./ovh sms SMS_SERVICE new 'JSON_ENCODED_SMS_JOB'
	
With :

* SMS_SERVICE : A sms service returned by "./ovh sms listServices"
* JSON_ENCODED_SMS_JOB : a JSON encoded SMS Job, see below.

##### JSON encoded SMS job format
It's an JSON object, with thoses properties (* = requiered)

* message* : message to send (string)
* receivers* : array of receivers (array of string)
* sender* : sender (string) 
* noStopClause : bool
* priority : hight|low|medium|veryLow (string)
* validityPeriod : The maximum time -in minute(s)- before the message is dropped. default 2880 (int)
* senderForResponse : (bool)
* charset : default UTF-8 (string)
* coding : defaut 7bit (string)
* differedPeriod : The time -in minute(s)- to wait before sending the message (int)
* receiversSlotId :The receivers document url link in csv format (string)
* class : flash|sim|phoneDisplay|toolkit (string)
* serviceName : The internal name of your SMS offer (string) 

Response :
* on success : string ""Done" + jobs ID + credits removed
* on failure : error


Example :

	./ovh sms new sms-su22-1 '{"message": "Test sms message","receivers": ["+336222XXXX"],"sender": "+33979XXXXX"}'
	Done
	Job ID: 17185893
	Credits removed: 1
	
## SERVER
#### List your dedicated servers
	./ovh server list
	
Response: all your server names. One per line.

Example :

	./ovh server list
	ns309865.ovh.net
	ns205931.ovh.net
	ks323462.kimsufi.com
	ns212758.ovh.net
	ns26462.ovh.net

### Get server properties
	./ovh server properties SERVER_NAME 

With :

* SERVER_NAME : name of the server (from ./ovh server list)

Response : String with one property by line

Example :

	./ovh server properties ns309865.ovh.net
	ID: 100800
	Name: ns309865.ovh.net
	Ip: 188.165.197.13
	Datacenter: rbx3
	ProfessionalUse: true
	CommercialRange: sp
	Os: proxmox_64
	State: ok
	Reverse: 8.vz.protecmail.com.
	Monitored: true
	Rack: 41G07
	RootDevice:
	LinkSpeed: 100
	Bootid: 1


### Reboot server
	./ovh server reboot SERVER_NAME 
	
With :

* SERVER_NAME : name of the server (from ./ovh server list)

Response : A server task as string (see example)

Example :

	./ovh server reboot ks323462.kimsufi.com
	Task ID: 2296596
	Function: hardReboot
	Status: init
	Comment: Reboot asked
	Last Upadte: 2014-04-01T08:43:23+02:00
	Start Date: 2014-04-01T08:43:23+02:00
	Done Date:


### Get server tasks
	./ovh server getTasks SERVER_NAME FUNCTION STATUS
	
With :

* SERVER_NAME : name of the server (from ./ovh server list)
* FUNCTION (optional) : filter by function. See [OVH doc](https://api.ovh.com/console/#/dedicated/server/%7BserviceName%7D/task#GET) for availables functions.
* STATUS (optional) : filter by status. See [OVH doc](https://api.ovh.com/console/#/dedicated/server/%7BserviceName%7D/task#GET) for availables status.

For STATUS and FUNCTION you can use "all" as wildcard if you want to filter by STATUS (./ovh server getTasks SERVER_NAME all done)

Response : A list of task ID (1 per line)	

Example :
	
	./ovh server getTasks ns309865.ovh.net hardReboot done
	2296596
	2296597


### Get server task properties
	./ovh server getTaskProperties SERVER_NAME TASK_ID
	
With :

* SERVER_NAME : name of the server (from ./ovh server list)
* TASK_ID : id of the task as returned by getTasks

Response : 	A server task as string (see example)

Example :

	./ovh server getTaskProperties ks323462.kimsufi.com 2296596
	Task ID: 2296596
	Function: hardReboot
	Status: done
	Comment: Reboot asked
	Last Upadte: 2014-04-01T08:45:00+02:00
	Start Date: 2014-04-01T08:43:23+02:00
	Done Date: 2014-04-01T08:45:00+02:00

	
### Cancel server task
	./ovh server cancelTask SERVER_NAME TASK_ID
	
With :

* SERVER_NAME : name of the server (from ./ovh server list)
* TASK_ID : id of the task as returned by getTasks

Response : "Success task TASK_ID cancelled" on success, error... on error

Examples :
	
	./ovh server cancelTask ks323462.kimsufi.com 2297463
	Success task 2297463 cancelled

	./ovh server cancelTask ks323462.kimsufi.com 2297463
	Error : [401 You cannot stop this task]	
	
	./ovh server cancelTask ks323462.kimsufi.com 2297463
	Error : [403 This task is already done]

		
		
	 	
	
	

	
	
	
	




	


