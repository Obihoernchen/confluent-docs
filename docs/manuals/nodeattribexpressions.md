---
title: Manual for nodeattribexpressions
---

nodeattribexpressions(5) -- Confluent attribute expression syntax
=================================================================

## DESCRIPTION

In confluent, any attribute may either be a straightforward value, or an
expression to generate the value.

An expression will contain some directives wrapped in `{}` characters. Within
`{}` are a number of potential substitute values and operations.

Note that syntax of expressions can have overlap with the shell syntax.
For example:

`$ echo (n2)`  
`-bash: syntax error near unexpected token `n2'`  

In such a case, it helps to quote the expression to allow it to be passed:

`$ echo '(n2)'`  
`(n2)`  


The most common operation is to extract a number from the nodename. These 
values are available as n1, n2, etc. So for example attributes for a node named 
b1o2r3u4 would have {n1} as 1, {n2} as 2, {n3} as 3, and {n4} as 4. 
Additionally, {n0} is special as representing the last number in a name, so in
the b1o2r3u4 example, {n0} would be 4.

Frequently a value derives from a number in the node name, but must undergo a
transform to be useful. As an example, if we have a scheme where nodes are
numbered n1-n512, and they are arranged 1-42 in rack1, 43-84 in rack2, and so
forth, it is convenient to perform arithmetic on the extracted number. Here is
an example of codifying the above scheme, and setting the u to the remainder:

`location.rack=rack{(n1-1)/42+1}`  
`location.u={(n1-1)%42+1}`  

Note how text may be mixed into expressions, only data within {} will receive
special treatment. Here we also had to adjust by subtracting 1 and adding it
back to make the math work as expected.

It is sometimes the case that the number must be formatted a different way,
either specifying 0 padding or converting to hexadecimal. This can be done by a
number of operators at the end to indicate formatting changes.

`{n1:02x} - Zero pad to two decimal places, and convert to hexadecimal, as mightbe used for generating MAC addresses`  
`{n1:x} - Hexadecimal without padding, as may be used in a generated IPv6 address`  
`{n1:X} - Uppercase hexadecimal`  
`{n1:02d} - Zero pad a normal numeric representation of the number.`  

Another common element to pull into an expression is the node name in whole:

`hardwaremanagement.manager={node}-imm`

Additionally other attributes may be pulled in:

`hardwaremanagement.switchport={location.u}`  

Multiple expressions are permissible within a single attribute:

`hardwaremanagement.manager={node}-{hardwaremanagement.method}`

A note to developers: in general the API layer will automatically recognize a
generic set attribute to string with expression syntax and import it as an
expression. For example, submitting the following JSON:

`{ 'location.rack': '{n1}' }`

Will auto-detect {n1} as an expression and assign it normally. If wanting to
set that value verbatim, it can either be escaped by doubling the {} or by
explicitly declaring it as a value:

`{ 'location.rack': '{{n1}}' }`  

`{ 'location.rack': { 'value': '{n1}' } }`


[DESCRIPTION]: #DESCRIPTION "DESCRIPTION"


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
