---
title: Manual for nodeattrib
---

nodeattrib(8) -- List or change confluent nodes attributes
=========================================================

## SYNOPSIS

`nodeattrib [-b] <var>noderange</var> [all|<var>nodeattribute</var>...]`
`nodeattrib <var>noderange</var> [<var>nodeattribute1=value1</var> <var>nodeattribute2=value2</var> ...]`  
`nodeattrib -c <var>noderange</var> <var>nodeattribute1</var> <var>nodeattribute2</var> ...`  
`nodeattrib -e <var>noderange</var> <var>nodeattribute1</var> <var>nodeattribute2</var> ...`  
`nodeattrib -p <var>noderange</var> <var>nodeattribute1</var> <var>nodeattribute2</var> ...`
`nodeattrib <var>noderange</var> -s <attributes.batch> ...` 

## DESCRIPTION

**nodeattrib** manages the attributes of confluent nodes.  In
the simplest form, it simply takes the given noderange(5) and lists the
matching nodes, one line at a time.

If a list of node attribute names are given, the value of those are also
displayed.  If `-b` is specified, it will also display information on
how inherited and expression based attributes are defined.  Attributes can be
straightforward values, or an expression as documented in nodeattribexpressions(5).
For a full list of attributes, run `nodeattrib <var>node</var> all` against a node.
If `-c` is specified, this will set the nodeattribute to a null value.
This is different from setting the value to an empty string.

Attributes may be specified by wildcard, for example `net.*switch` will report
all attributes that begin with `net.` and end with `switch`.

If the word all is specified, then all available attributes are given.
Omitting any attribute name or the word 'all' will display only attributes
that are currently set.

For the `groups` attribute, it is possible to add a group by doing
`groups,=<var>newgroup</var>` and to remove by doing `groups^=<var>oldgroup</var>`

Note that `nodeattrib <var>group</var>` will likely not provide the expected behavior.
See nodegroupattrib(8) command on how to manage attributes on a group level.  Running
nodeattrib on a group will simply set node-specific attributes on each individual
member of the group.

Additionally, if wanting to change from a node level attribute value to inherit from a
member group, it is required to use '-c' to clear the attribute.  Conversely, assigning
to a blank value will allow masking a group defined attribute with an empty value.

## OPTIONS

* `-b`, `--blame`:
  Annotate inherited and expression based attributes to show their base value.
  
* `-c`, `--clear`:
  Clear specified nodeattributes.
  
* `-e`, `--environment`:
  Set specified attributes based on exported environment variable of matching name. 
  Environment variable names may be lower case or all upper case. 
  Replace . with _ as needed (e.g. info.note may be specified as either $info_note or $INFO_NOTE)
  
* `-p`, `--prompt`:
  Request interactive prompting to provide values rather than the command line
  or environment variables.

* `-s`, `--set`:
  Set attributes using a batch file rather than the command line. The attributes in the batch file 
  can be specified as one line of key=value pairs simmilar to command line or each attribute can
  be in its own line. Lines that start with # sign will be read as a comment. See EXAMPLES for batch
  file syntax.   
 
* `-m MAXNODES`, `--maxnodes=MAXNODES`:
  Prompt if trying to set attributes on more than
  specified number of nodes.

* `-h`, `--help`:
  Show help message and exit

## EXAMPLES
* Listing matching nodes of a simple noderange:
    `# nodeattrib n1-n2`  
    `n1: console.method: ipmi`  
    `n1: hardwaremanagement.manager: 172.30.3.1`  
    `n2: console.method: ipmi`  
    `n2: hardwaremanagement.manager: 172.30.3.2`  

* Getting an attribute of nodes matching a noderange:
    `# nodeattrib n1,n2 hardwaremanagement.manager`  
    `n1: hardwaremanagement.manager: 172.30.3.1`  
    `n2: hardwaremanagement.manager: 172.30.3.2`  

* Getting a group of attributes while determining what group defines them:
    `# nodeattrib n1,n2 hardwaremanagement --blame`  
    `n1: hardwaremanagement.manager: 172.30.3.1`  
    `n1: hardwaremanagement.method: ipmi (inherited from group everything)`  
    `n1: net.switch: r8e1`  
    `n1: net.switchport: 14`  
    `n2: hardwaremanagement.manager: 172.30.3.2`  
    `n2: hardwaremanagement.method: ipmi (inherited from group everything)`  
    `n2: net.switch: r8e1`  
    `n2: net.switchport: 2`  

* Listing matching nodes of a simple noderange that are set:
    `# nodeattrib n1-n2 current`  
    `n1: console.method: ipmi`  
    `n1: hardwaremanagement.manager: 172.30.3.1`  
    `n2: console.method: ipmi`  
    `n2: hardwaremanagement.manager: 172.30.3.2`  

* Change attribute on nodes of a simple noderange:
    `# nodeattrib n1-n2 console.method=serial`  
    `n1: console.method: serial`  
    `n1: hardwaremanagement.manager: 172.30.3.1`  
    `n2: console.method: serial`  
    `n2: hardwaremanagement.manager: 172.30.3.2`  

* Clear attribute on nodes of a simple noderange, if you want to retain the variable set the attribute to "":
    `# nodeattrib n1-n2 -c console.method`  
    `# nodeattrib n1-n2 console.method`  
    `n1: console.method: `  
    `n2: console.method: `  

* List all switches that a node is described as connected to:
    `# nodeattrib d1 net.*switch`  
    `d1: net.mgt.switch: mgtswitch1`  
    `d1: net.pxe.switch: pxeswitch1`  
    `d1: net.switch:`  

* Setting attributes using a batch file with syntax similar to command line:
    `# cat nodeattributes.batch`  
    `# power`
    `power.psu1.outlet=3 power.psu1.pdu=pdu2`
    `# nodeattrib n41 -s nodeattributes.batch`
    `n41: 3`
    `n41: pdu2`

* Setting attributes using a batch file with syntax where each attribute is in its own line:
    `# cat nodeattributes.batch`  
    `# management`
    `custom.mgt.switch=switch_main`
    `custom.mgt.switch.port=swp4`
    `# nodeattrib n41 -s nodeattributes.batch`
    `n41: switch_main`
    `n41: swp4`

  

## SEE ALSO

nodegroupattrib(8), nodeattribexpressions(5)

## ATTRIBUTES

* `collective.manager`:
  When in collective mode, the member of the collective currently considered to be responsible for this node.  At a future date, this may be modified automatically if another attribute indicates candidate managers, either for high availability or load balancing purposes.

* `collective.managercandidates`:
  A noderange of nodes permitted to be a manager for the node. This controls failover and deployment.  If not defined, all managers may deploy and no automatic failover will be performed. Using this requires that collective members be defined as nodes for noderange expansion

* `console.logging`:
  Indicate logging level to apply to console.  Valid values are currently "full", "interactive", "memory", and "none". Defaults to "full".

* `console.method`:
  Indicate the method used to access the console of the managed node.  If not specified, then console is disabled.  "ipmi" should be specified for most systems if console is desired.

* `crypted.grubpassword`:
  Password required to modify grub behavior at boot

* `crypted.rootpassword`:
  The password of the local root password. This is stored as a non-recoverable hash. If unspecified and confluent is used to deploy, then login at console using password will be impossible and only key based login can work for root.

* `crypted.selfapikey`:
  Crypt of api key for self api requests by node

* `deployment.apiarmed`:
  Indicates whether the node authentication token interface is armed.  If set to once, it will grant only the next request. If set to continuous, will allow many requests, which greatly reduces security, particularly when connected to untrusted networks. Should not be set unless an OS deployment is pending on the node. Generally this is not directly modified, but is modified by the "nodedeploy" command

* `deployment.encryptboot`:
  Specify a strategy for encrypting the volume. Support for this setting is currently only enabled for RedHat 8 and CentOS 8 profiles. If blank or unset, no encryption is done. If set to "tpm2" then the OS will freely decrypt so long as the same Trusted Platform Module is available to decrypt the volume. Note that versions earlier than 8.2 may malfunction at boot time if this feature is attempted, depending on configuration.

* `deployment.pendingprofile`:
  An OS profile that is pending deployment.  This indicates to the network boot subsystem what should be offered when a potential network boot request comes in

* `deployment.profile`:
  The profile that has most recently reported completion of deployment. Note that an image may opt to leave itself both current and pending, for example a stateless profile would be both after first boot.

* `deployment.sealedapikey`:
  This attribute is used by some images to save a sealed version of a node apikey, so that a subsequent run with same TPM2 will use the TPM2 to protect the API key rather than local network verification. If this is set, then an api key request will receive this if the api key grant is not armed

* `deployment.stagedprofile`:
  A profile that has been staged, but is awaiting final boot to be activated. This allows an OS profile to remove itself from netboot without indicating completion to any watcher.

* `deployment.state`:
  Profiles may push more specific state, for example, it may set the state to "failed" or "succeded"

* `deployment.state_detail`:
  Detailed state information as reported by an OS profile, when available

* `deployment.useinsecureprotocols`:
  What phase(s) of boot are permitted to use insecure protocols (TFTP and HTTP without TLS.  By default, only HTTPS is used.  However this is not compatible with most firmware in most scenarios.  Using "firmware" as the setting will still use HTTPS after the initial download, though be aware that a successful attack during the firmware phase will negate future TLS protections.  The value "always" will result in tftp/http being used for most of the deployment.  The value "never" will allow HTTPS only. Note that Ubuntu will still use HTTP without TLS for a phase of the installation process.

* `discovery.nodeconfig`:
  Set of nodeconfig arguments to apply after automatic discovery

* `discovery.passwordrules`:
  Any specified rules shall be configured on the BMC upon discovery.  "expiration=no,loginfailures=no,complexity=no,reuse=no" would disable password expiration, login failures triggering a lockout, password complexity requirements,and any restrictions around reusing an old password.

* `discovery.policy`:
  Policy to use for auto-configuration of discovered and identified nodes. Valid values are "manual", "permissive", or "open". "manual" means nodes are detected, but not autoconfigured until a user approves. "permissive" indicates to allow discovery, so long as the node has no existing public key. "open" allows discovery even if a known public key is already stored

* `dns.domain`:
  DNS Domain searched by default by the system

* `dns.servers`:
  DNS Server or servers to provide to node

* `enclosure.bay`:
  The bay in the enclosure, if any

* `enclosure.extends`:
  When using an extendable enclosure, this is the node representing the manager that is one closer to the uplink.

* `enclosure.manager`:
  The management device for this node's chassis

* `groups`:
  List of static groups for which this node is considered a member

* `hardwaremanagement.manager`:
  The management address dedicated to this node.  This is the address of, for example, the Lenovo XCC.  It may optionally include /<var>prefixlen</var> CIDR suffix to indicate subnet length, which is autodetected by default where possible.

* `hardwaremanagement.method`:
  The method used to perform operations such as power control, get sensor data, get inventory, and so on. ipmi is used if not specified.

* `hardwaremanagement.port`:
  The port the BMC should be configured to connect to network.  This only has effect during deployment and does not apply to out of band discovery. Example values include "ocp", "ml2", "lom" (for on board port shared with operating system), or "dedicated"

* `hardwaremanagement.vlan`:
  The vlan that a BMC should be configured to tag traffic. This only has effect during OS deployment and does not apply to out of band discovery.

* `id.model`:
  The model number of a node.  In scenarios where there is both a name and a model number, it is generally expected that this would be the generally more specific model number.

* `id.serial`:
  The manufacturer serial number of node

* `id.uuid`:
  The UUID of the node as presented in DMI.

* `info.note`:
  A field used for administrators to make arbitrary notations about nodes. This is meant entirely for human use and not programmatic use, so it can be freeform text data without concern for issues in how the server will process it.

* `location.height`:
  Height in RU of the system (defaults to query the systems)

* `location.rack`:
  Rack number of the rack the node is in

* `location.room`:
  Room description for the node

* `location.row`:
  Row description for the rack the node is in

* `location.u`:
  Position in the rack of the node

* `net.bootable`:
  Whether or not the indicated network interface is to be used for booting.  This is used by the discovery process to decide where to place the mac address of a detected PXE nic.

* `net.connection_name`:
  Name to use when specifiying a name for connection and/or interface name for a team.  This may be the name of a team interface, the connection name in network manager for the interface, or may be installed as an altname as supported by the respective OS deployment profiles.  Default is to accept default name for a team consistent with the respective OS, or to use the matching original port name as connection name.

* `net.hostname`:
  Used to specify hostnames per interface. Can be a comma delimited list to indicate aliases

* `net.hwaddr`:
  The hardware address, aka MAC address of the interface indicated, generally populated by the PXE discovery mechanism

* `net.interface_names`:
  Interface name or comma delimited list of names to match for this interface. It is generally recommended to leave this blank unless needing to set up interfaces that are not on a common subnet with a confluent server, as confluent servers provide autodetection for matching the correct network definition to an interface. This would be the default name per the deployed OS and can be a comma delimited list to denote members of a team or a single interface for VLAN/PKEY connections.

* `net.ipv4_address`:
  When configuring static, use this address.  If unspecified, it will check if the node name resolves to an IP address.  Additionally, the subnet prefix may be specified with a suffix, e.g. "/16".  If not specified, it will attempt to autodetect based on current network configuration.

* `net.ipv4_gateway`:
  The IPv4 gateway to use if applicable.  As is the case for other net attributes, net.eth0.ipv4_gateway and similar is accepted.

* `net.ipv4_method`:
  Whether to use static or dhcp when configuring this interface for IPv4. "firmwaredhcp" means to defer to external DHCP server during firmware execution, but use static for OS. "firmwarenone" means to suppress even the no-IP dhcp offers, to fully delegate to an external dhcp/pxe configuration, even for confluent deployment.

* `net.ipv6_address`:
  When configuring static, use this address.  If unspecified, it will check if the node name resolves to an IP address.  Additionally, the subnet prefix may be specified with a suffix, e.g. "/64".  If not specified, it will attempt to autodetect based on current network configuration.

* `net.ipv6_gateway`:
  The IPv6 gateway to use if applicable.  As is the case for other net attributes, net.eth0.ipv6_gateway and similar is accepted.

* `net.ipv6_method`:
  Whether to use static or dhcp when configuring this interface for IPv6. "firmwaredhcp" means to defer to external DHCP server during firmware execution, but use static for OS. "firmwarenone" means to suppress even the no-IP dhcp offers, to fully delegate to an external dhcp/pxe configuration, even for confluent deployment

* `net.switch`:
  An ethernet switch the node is connected to.  Note that net.* attributes may be indexed by interface. For example instead of using net.switch, it is possible to use net.eth0.switch and net.eth1.switch or net.0.switch and net.1.switch to define multiple sets of net connectivity associated with each other.

* `net.switchport`:
  The port on the switch that corresponds to this node. See information on net.switch for more on the flexibility of net.* attributes.

* `net.team_mode`:
  Indicates that this interface should be a team and what mode or runner to use when teamed. If this covers a deployment interface, one of the member interfaces may be brought up as a standalone interface until deployment is complete, as supported by the OS deployment profile. To support this scenario, the switch should be set up to allow independent operation of member ports123654 (e.g. lacp bypass mode or fallback mode).

* `net.vlan_id`:
  Ethernet VLAN or InfiniBand PKEY to use for this connection. Specify the parent device using net.interface_names.

* `ntp.servers`:
  NTP server or servers to provide to node during deployment. An OS profile may default to internet NTP, depending on default configuration of the respective operating system

* `power.outlet`:
  Species the outlet identifier on the PDU associoted with a power input on the node

* `power.pdu`:
  Specifies the managed PDU associated with a power input on the node

* `pubkeys.addpolicy`:
  Policy to use when encountering unknown public keys.  Choices are "automatic" to accept and store new key if no key known and "manual" to always reject a new key, even if no key knownNote that if the trusted CA verifies the certificate, that is accepted ignoring this policy.  Default policy is "automatic"

* `pubkeys.ssh`:
  Fingerprint of the SSH key of the OS running on the system.

* `pubkeys.tls_hardwaremanager`:
  Fingerprint of the TLS certificate recognized asbelonging to the hardware manager of the server

* `secret.hardwaremanagementpassword`:
  Password to use when connecting to the hardware manager.  Aliases for this attribute include bmcpass and switchpass

* `secret.hardwaremanagementuser`:
  The username to use when connecting to the hardware manager. Aliases for this attribute include bmcuser and switchuser

* `secret.ipmikg`:
  Optional Integrity key for IPMI communication.  This should generally be ignored, as mutual authentication is normally done with the password alone (which is a shared secret in IPMI)

* `secret.selfapiarmtoken`:
  A one-time use shared secret to authenticate a node api token

* `secret.snmpcommunity`:
  SNMPv1 community string, it is highly recommended tostep up to SNMPv3

* `ssh.trustnodes`:
  Nodes that are allowed to ssh into the node, expressed in noderange syntax.  This is used during deployment if the confluent SSH certificate authority is configured.  Default behavior is for all nodes to trust each other.

* `trusted.subnets`:
  Remote subnets in CIDR notation that should be considered as trusted as local networks

* `type`:
  The type of node.  This may be switch, server, rackmount, dense, enclosure or not set to be generic.


[SYNOPSIS]: #SYNOPSIS "SYNOPSIS"
[DESCRIPTION]: #DESCRIPTION "DESCRIPTION"
[OPTIONS]: #OPTIONS "OPTIONS"
[EXAMPLES]: #EXAMPLES "EXAMPLES"
[SEE ALSO]: #SEE-ALSO "SEE ALSO"
[ATTRIBUTES]: #ATTRIBUTES "ATTRIBUTES"


[collate(1)]: collate.html
[collective(1)]: collective.html
[confetty(8)]: confetty.html
[confluent2hosts(8)]: confluent2hosts.html
[confluentdbutil(8)]: confluentdbutil.html
[confluent(8)]: confluent.html
[l2traceroute(8)]: l2traceroute.html
[nodeapply(8)]: nodeapply.html
[nodeattribexpressions(5)]: nodeattribexpressions.html
[nodeattrib(8)]: nodeattrib.html
[nodebmcpassword(8)]: nodebmcpassword.html
[nodebmcreset(8)]: nodebmcreset.html
[nodeboot(8)]: nodeboot.html
[nodeconfig(8)]: nodeconfig.html
[nodeconsole(8)]: nodeconsole.html
[nodedefine(8)]: nodedefine.html
[nodedeploy(8)]: nodedeploy.html
[nodediscover(8)]: nodediscover.html
[nodeeventlog(8)]: nodeeventlog.html
[nodefirmware(8)]: nodefirmware.html
[nodegroupattrib(8)]: nodegroupattrib.html
[nodegroupdefine(8)]: nodegroupdefine.html
[nodegrouplist(8)]: nodegrouplist.html
[nodegroupremove(8)]: nodegroupremove.html
[nodehealth(8)]: nodehealth.html
[nodeidentify(8)]: nodeidentify.html
[nodeinventory(8)]: nodeinventory.html
[nodelicense(8)]: nodelicense.html
[nodelist(8)]: nodelist.html
[nodemedia(8)]: nodemedia.html
[nodeping(8)]: nodeping.html
[nodepower(8)]: nodepower.html
[noderange(5)]: noderange.html
[noderemove(8)]: noderemove.html
[nodereseat(8)]: nodereseat.html
[nodersync(8)]: nodersync.html
[noderun(8)]: noderun.html
[nodesensors(8)]: nodesensors.html
[nodesetboot(8)]: nodesetboot.html
[nodeshell(8)]: nodeshell.html
[nodestorage(8)]: nodestorage.html
[nodesupport(8)]: nodesupport.html
[osdeploy(8)]: osdeploy.html
[stats(8)]: stats.html
