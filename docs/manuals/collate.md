---
title: Manual for collate
---

collate(1) -- Organize text input by node
==============================

## SYNOPSIS

`<var>other command</var> | collate [-a] [-b] [-d] [-w] [-g] [-s] [-c] [-r] [-l lognametemplate]`

## DESCRIPTION

**collate** takes input in the form of <var>nodename</var>: <var>data</var> and groups
the output for each <var>nodename</var> together, and checks for identical data to further group nodes together.
Output groups are sorted in descending size such that the topmost group is the largest, and the
last output group is the smallest.  In the event of equally sized output groups,
the groups are sorted alphanumerically by their header, and each header has
node and group names sorted alphanumerically.

## OPTIONS

* `-a`, `--abbreviate`:
   Use confluent to attempt to shorten the noderange.  This can help identify
   when output differs along a telling group boundary.  For example, if
   output suggests a large number of nodes are unreachable, abbreviate
   showing 'rack1' as being unreachable may make more obvious a possible cause.
   
* `-b BASE`, `--base=BASE`:
   Use given node as reference for comparison when using
   -d, instead of using the most common result      

* `-d`, `--diff`:
   Express all but the most common result group in terms of diff from
   the most common result group
   
* `-w`, `--watch`:
   Update results dynamically as data becomes available, rather than
   waiting    for the command to fully complete.
   
* `-g`, `--groupcount`:
   Show count of output groups rather than the actual
   output
   
* `-s`, `--skipcommon`:
   Suppress printing of the most common result text group.  This is used to
   focus on stray output against a well known and expected result.
   
* `-c`, `--count`:
   Print a count of the number of nodes in an output group under the 
   noderange.
  
* `-r`, `--reverse`:
   Rather than starting with most common to least common, start with
   the least common and print the most common last.
    
* `-l LOG`, `--log=LOG`:
   Save output per node to individual log files, replacing {node} in the name
   with the nodename of each
   
* `-h`, `--help`:
   Show help message and exit   
   
## EXAMPLES
 * Organizing power state of multiple nodes:  
  `# nodepower n1-n12 | collate`  
  `====================================`  
  `n1,n2,n3,n4,n7,n8,n9,n10,n11,n12`  
  `====================================`  
  `on`  
  ` `  
  `====================================`  
  `n5,n6`  
  `====================================`  
  `off`  

* Using diff to detect distinct UEFI configuration

  `# pasu n1-n4 show Processors|collate -d -s`  
  `====================================`  
  `n3`  
  `====================================`  
  `@@`  
  `  Processors.ProcessorPerformanceStates=Enable`  
  `  Processors.C-States=Enable`  
  `  Processors.PackageACPIC-StateLimit=ACPI C3`  
  `- Processors.C1EnhancedMode=Enable`  
  `+ Processors.C1EnhancedMode=Disable`  
  `- Processors.Hyper-Threading=Enable`  
  `+ Processors.Hyper-Threading=Disable`  
  `  Processors.ExecuteDisableBit=Enable`  
  `  Processors.IntelVirtualizationTechnology=Enable`  
  ` `  
  `====================================`  
  `n1`  
  `====================================`  
  `@@`  
  `  Processors.ProcessorPerformanceStates=Enable`  
  `  Processors.C-States=Enable`  
  `  Processors.PackageACPIC-StateLimit=ACPI C3`  
  `- Processors.C1EnhancedMode=Enable`  
  `+ Processors.C1EnhancedMode=Disable`  
  `  Processors.Hyper-Threading=Enable`  
  `  Processors.ExecuteDisableBit=Enable`  

   
   


[SYNOPSIS]: #SYNOPSIS "SYNOPSIS"
[DESCRIPTION]: #DESCRIPTION "DESCRIPTION"
[OPTIONS]: #OPTIONS "OPTIONS"
[EXAMPLES]: #EXAMPLES "EXAMPLES"


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
