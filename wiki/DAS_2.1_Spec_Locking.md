---
title: DAS/2.1/Spec/Locking
permalink: wiki/DAS/2.1/Spec/Locking/
layout: wiki
---

**<big>[DAS/2.1](/wiki/DAS/2.1/Spec "wikilink") Locking Specification</big>**

A DAS/2 server may support property-based locking of sets of objects, in
which case the client is required to obtain a valid lock before creating
new objects or updating existing ones via the
[writeback](/wiki/DAS/2.1/Spec/Writeback "wikilink") mechanism. Locking is
accomplished by creating one or more lock objects in one of the
supported namespaces. The contents of the lock object indicate the scope
of objects that the user is authorized to update. Once a lock object is
created, it must be referenced whenever updating an object. The server
compares the provided lock object to the updated objects and grants the
update if the two are "compatible." (Compatibility rules are described
in detail later in this section.)

Lock objects are distinguished from other objects by having a "expires"
attribute. The value of this attribute indicates how long the lock is
valid for. Additional lock-specific attributes indicate the
credentialing information used for authenticating access to the lock.

Creating Locks
--------------

To create a lock the client sends a POST request to the /lock namespace.
The POSTed object must be a document of type <i>text/x-das-lock+xml</i>,
and will contain at least one <LOCK> section. Example:

REQUEST:

> `   POST `[`http://www.wormbase.org/das/genome/volvox/1/lock/`](http://www.wormbase.org/das/genome/volvox/1/lock/)` `

    Content-Type: text/x-das-sources+xml

    <?xml version="1.0" standalone="no"?>
    <!DOCTYPE LOCKS SYSTEM "http://www.biodas.org/dtd/das2lock.dtd">
    <LOCKS
         xmlns="http://www.biodas.org/ns/das/genome/2.00"
         xmlns:xlink="http://www.w3.org/1999/xlink"
         xmlns:das="http://www.biodas.org/ns/das/genome/2.00"
         xml:base="http://www.wormbase.org/das/genome/volvox/1/">
     <LOCK id="lock/mylock1"
           filter_href="feature?overlaps=Chr3/1000:2000" 
           expires="2005-04-25T12:50:00+00:00" />
     <LOCK id="lock/mylock2"
           filter_href="feature?overlaps=Chr20/50000:60000" 
           expires="2005-04-25T12:50:00+00:00" />
    </LOCKS>

This requests the creation of two lock objects, one of which applies to
features contained within the region Chr3/1000:2000 and the other of
which applies to features contained within the regio Chr20:50000:60000.
Both locks are requested to persist until 12:50 UTCon April 25, 2005.

The response to this request is a *text/x-das-updatelist+xml* document,
as described earlier. In addition to the attributes shown earlier, the
<UPDATE_STATUS> tag will contain a <b>lock\_expires</b> attribute
indicating the lifetime of the lock. This may be more or less than what
the request asked for. As in earlier examples, the local ID of the
requested lock is replaced by a stable ID provided by the server:

RESPONSE:

    Content-Type: text/x-das-sources+xml

    <?xml version="1.0" standalone="no"?>
    <!DOCTYPE DAS2UPDATELIST SYSTEM "http://www.biodas.org/dtd/das2updatelist.dtd">
    <UPDATELIST
         xmlns="http://www.biodas.org/ns/das/genome/2.00"
         xml:base="http://dev.wormbase.org/das/genome/volvox/1/lock" >
     <UPDATE_STATUS localid="mylock1" id="lock00004" status="200" expires="2005-04-25T13:00:00+00:00" />
     <UPDATE_STATUS localid="mylock2" id="lock00005 status="200" expires="2005-04-25T13:00:00+00:00" />
    </UPDATELIST>

The response in this example indicates that the two locks were granted
and assigned stable ids of
"<http://www.wormbase.org/das/genome/volvox/1/feature/lock00004> and
lock00005, respectively.

The value of the <b>filter\_href</b> attribute determines the scope of
the lock. It is a valid DAS/2 namespace, and, optionally, namespace
filters, as described in [Lock Scopes](#Lock_Scopes "wikilink").

Retrieving Locks
----------------

DAS/2 implements a lock object that is used to control concurrent
writeback access to the database. Locks are scoped to a versioned data
source, are owned by a particular user (or client application), and have
a limited lifetime. At any time there may be no, one, or multiple locks
in a database.

To fetch a list of all locks in the current source, perform a GET on the
versioned data source URL appended with "/lock": text/x-das-lock+xml

REQUEST:

> `   `[`http://www.wormbase.org/das/genome/volvox/1/lock`](http://www.wormbase.org/das/genome/volvox/1/lock)` `

RESPONSE:

    Content-Type: text/x-das-lock+xml

    <?xml version="1.0" standalone="no"?>
    <!DOCTYPE LOCKS SYSTEM "http://www.biodas.org/dtd/das2lock.dtd">
    <LOCKS
         xmlns="http://www.biodas.org/ns/das/genome/2.00"
         xmlns:xlink="http://www.w3.org/1999/xlink"
         xmlns:das="http://www.biodas.org/ns/das/genome/2.00"
         xml:base="http://www.wormbase.org/das/genome/volvox/1/">
     <LOCK id="lock/lock000001"
           filter_href="feature?inside=Chr3/1000:2000" 
           expires="2005-04-25T12:39:53+00:00"
         auth_type="Basic"
      credentials="janderson" />
     <LOCK id="lock/lock000018"
           filter_href="feature?inside=Chr20/50000:60000" 
           expires="2005-04-25T12:50:18+00:00"
         auth_type="Basic"
      credentials="janderson" />
     <LOCK id="lock/lock00091"
           filter_href="property" 
           expires="2005-04-25T12:10:00+00:00"
         auth_type="Certificate"
      credentials="C=UK/SP=CA/L=Hinxton/O=Sanger Institute/CN=James Gilbert" />
    </LOCKS>

The request for a list of locks returns a document of type
*text/x-das-lock+xml*. This document's tags and attributes are as
follows:

Get schema as: [RNC](http://biodas.org/documents/das2/das2lock.rnc)
[RNG](http://biodas.org/documents/das2/das2lock.rng)
[DTD](http://biodas.org/documents/das2/das2lock.dtd)

<dl>
<dt>
&lt;LOCKS&gt;

<dd>
<span>A set of &lt;LOCK&gt; tags.</span>  
<b>xml:base</b> – The xml:base of the tag should be set to the start of
the /region namespace of the current data source.

</dl>
<dl>
<dt>
&lt;LOCK&gt; (zero or more)

<dd>
<span>A lock object</span> <b>id</b> – the URL for the lock  
<b>filter\_href</b> (optional) – the filter for the lock This is a
relative URI that gives locked namespace and (optionally) query filter:
e.g. feature?overlaps=Chr3/1000:2000;type=exon,intron,splice\_site
<b>expires</b> (optional) – a datetime that indicates when the lock
object expires  
<b>auth\_type</b> (optional) – an HTTP authentication type indicating
the kind of authentication in use when lock created  
<b>credentials</b> (optional) – an HTTP credential indicating the
credentials presented by the peer

</dl>
Each <LOCK> section contains a unique lock ID (relative to the
xml:base). The lock-expires attribute is a date time string that
indicates when the lock will become invalid. After a lock expires it
will eventually be removed from the database, although this garbage
collection may take some time to occur.

The <b>auth\_type</b> and <b>credentials</b> attributes provides
information about who owns the lock and what type of authentication was
used when the user created this lock. The authentication process is
controlled at the HTTP level and is described in more detail in DAS2
Updates. Possible values for auth\_type include:

<table border="1">
<tr>
<th>
auth\_type

</th>
<th>
credentials

</th>
<th>
Example

</th>
</tr>
<tr>
<td>
Basic

</td>
<td>
Username

</td>
<td>
JAnderson

</td>
</tr>
<tr>
<td>
Digest

</td>
<td>
Username

</td>
<td>
JAnderson

</td>
</tr>
<tr>
<td>
Host

</td>
<td>
IP Address

</td>
<td>
143.48.1.250

</td>
</tr>
<tr>
<td>
Certificate

</td>
<td>
Client certificate distinguished name

</td>
<td>
C=US/SP=MA/L=Boston/O=Capricorn

`   Organization/OU=Sales/CN=James`  
`   Anderson/Email=janderson@capricorn.com`

</td>
</tr>
</table>
The <b>filter\_href</b> attribute, if present, limits the scope of the
lock. In the absence of the attribute, the lock's scope extends to the
entire versioned datasource, which means that only the owner of the lock
can make changes while the lock is in effect. If this attribute is
present, then it is interpreted as a partial URL corresponding to a
DAS/2 namespace, and the scope of the lock is limited to that namespace.
For feature objects, additional filter arguments can be used to restrict
the scope of the lock further.

<table border="1">
<tr>
<th>
filter\_href

</th>
<th>
Interpretation

</th>
</tr>
<tr>
<td>
feature

</td>
<td>
Lock all features

</td>
</tr>
<tr>
<td>
type

</td>
<td>
Lock all feature types

</td>
</tr>
<tr>
<td>
property

</td>
<td>
Lock all feature properties

</td>
</tr>
<tr>
<td>
feature?inside=Chr3/50000:60000

</td>
<td>
Lock all features Chr3 from 50,000 to 60,000.

</td>
</tr>
<tr>
<td>
feature?inside=Chr3/50000:60000,Chr4/100000:110000

</td>
<td>
Lock all features inside the two indicated regions.

</td>
</tr>
<tr>
<td>
feature?inside=Chr3/50000:60000;type=exon

</td>
<td>
Lock all exon features inside the indicated region.

</td>
</tr>
</table>
While the syntax allows arbitrary subsets of features to be locked,
server implementations may not allow the full range of possibilities. In
particular, a DAS/2 writeback server is only required to support a
single inside filter, and is not required to support either type or
property locks.

Information about a single lock can be obtained by requesting the lock
namespace with the lock ID appended:

REQUEST

> `   `[`http://www.wormbase.org/das/genome/volvox/1/lock/lock000018`](http://www.wormbase.org/das/genome/volvox/1/lock/lock000018)` `

This will retrieve a <i>text/x-das-lock+xml</i> document containing a
single <LOCK> subsection.

Authentication
--------------

As described in [GET Requests on DAS/2
URLs](/wiki/DAS/2.1/Spec/Get-Genomic "wikilink"), authentication of users is
handled at the HTTP level. This allows DAS/2 administrators to implement
any combination of host, user or certificate-based authentication. If
authentication is in use, the lock object will contain fields indicating
the type of authentication in use and the credentials used for
authentication. These values are for informational purposes only and
cannot be altered at the client side. Possible values include:

<table border="1">
<tr>
<th>
lock-auth-type

</th>
<th>
lock-credentials

</th>
<th>
Example

</th>
</tr>
<tr>
<td>
Basic

</td>
<td>
Username

</td>
<td>
JAnderson

</td>
</tr>
<tr>
<td>
Digest

</td>
<td>
Username

</td>
<td>
JAnderson

</td>
</tr>
<tr>
<td>
Host

</td>
<td>
IP Address

</td>
<td>
143.48.1.250

</td>
</tr>
<tr>
<td>
Certificate

</td>
<td>
Client certificate distinguished name

</td>
<td>
C=US/SP=MA/L=Boston/O=Capricorn

`   Organization/OU=Sales/CN=James`  
`   Anderson/Email=janderson@capricorn.com`

</td>
</tr>
</table>
The server can readily retrieve these values from the runtime
environment of the request.

Lock Scopes
-----------

When locking is in use, the client must append one or more lock IDs to
the URI whenever creating or updating objects. The syntax is:

REQUEST:

> `   PUT `[`http://dev.wormbase.org/das/genome/volvox/1/feature/exon_e000323?lock=lock00004;lock=lock00005`](http://dev.wormbase.org/das/genome/volvox/1/feature/exon_e000323?lock=lock00004;lock=lock00005)` `

Before committing the updated objects to the database, the server will
check that the indicated lock objects are valid and that they allow the
requested operation. Criteria for lock validity are:

1.  The lock has not expired.
2.  The credentials of the user making the current request match the
    `     credentials of the user who owns the lock.`

3.  The requested operation affects the same namespace as the lock;
    `     e.g. if the lock object is in the /feature namespace, then it`  
    `     only allows operations updates to the /feature namespace.`

4.  If the lock contains scope restrictions (described next), the
    `     requested operation is compatible with those restrictions.`

If any of these criteria fail to be satisified, the create or update
operation will fail.

By default, a lock object locks the entire namespace referred to in the
<b>lock\_href</b> attribute. For example, a lock whose <b>lock\_href</b>
attribute is on the "type" namespace (relative to the xml:base
attribute), will lock all feature types, allowing only the lock owner to
add, update or delete types. A lock without any <b>lock\_href</b>
attribute at all will lock the entire database.

Locks that are scoped to the feature namespace can accept filter
arguments as described in the [Feature
request](/wiki/DAS/2.1/Spec/Get-Genomic/Features "wikilink"). These arguments
further restrict the scope across which the lock applies. In principle,
any of the filter arguments are accepted. For example, the following
<b>lock\_href</b> would lock all features that overlap bases 1000-2000
of Chr3 and are of type exon, intron or splice\_site:

>     <LOCK id="lock23" lock_href="feature?overlaps=Chr3/1000:2000;type=exon,intron,splice_site" />

In practice, however, writeback servers are only required to implement
the "inside" filter, which is sufficient for region-based locking:

>     &lt;LOCK id="lock23" lock_href="feature?inside=Chr3/1000:2000,Chr4/50000:60000" /&gt;

This lock applies to features completely contained within region
1000-2000 of Chr3, or 50000-60000 of Chr4.

When a <i>inside</i> region scope is in effect, the lock acts only on
features that are entirely contained entirely within the indicated
region (feature start &gt;= region start and feature end &lt;= region
end). Features that are outside of this region or which are only
partially contained within the region are considered unlocked, and an
attempt to update them will fail. The following cases hold:

<dl>
<dt>
Creation of a new feature object

<dd>
The boundaries of the feature object must be entirely contained within
the locked region. In the case of a batch creation of multiple objects
such as a feature and its subfeatures, each of the subfeatures must be
contained within the region specified by one of the locks given in the
lock= argument.

<dt>
Deletion of an object

<dd>
The existing boundaries of the object to be deleted must be entirely
contained within the locked region.

<dt>
Update of an object

<dd>
The existing boundaries of the object must be entirely contained within
the locked region. In the case of an update that changes the location of
the object, the new coordinates must also be contained within one of the
locked regions given by the lock argument.

</dl>
A lock cannot be granted if there are conflicts with existing valid
locks. In practice, this means that there can only be one property or
type lock at any time, and that a feature lock will only be granted if
none of its inside regions overlap with regions covered by existing
feature locks. In particular, a whole-genome feature lock (one without
the inside filter) will only be granted if there are no outstanding
feature locks.

Querying Locks
--------------

Once a lock has been created, it may be queried using a GET requests. In
this example, a client requests information about a previously allocated
lock with id lock00004:

REQUEST:

> `   GET `[`http://www.wormbase.org/das/genome/volvox/1/lock/lock00004`](http://www.wormbase.org/das/genome/volvox/1/lock/lock00004)` `

RESPONSE:

    Content-Type: text/x-das-sources+xml
       
    <?xml version="1.0" standalone="no"?>
    <!DOCTYPE LOCKS SYSTEM "http://www.biodas.org/dtd/das2lock.dtd">
    <LOCKS
         xmlns="http://www.biodas.org/ns/das/genome/2.00"
         xmlns:xlink="http://www.w3.org/1999/xlink"
         xmlns:das="http://www.biodas.org/ns/das/genome/2.00"
         xml:base="http://www.wormbase.org/das/genome/volvox/1/">
     <LOCK id="lock00004"
           basic="JAnderson"
           Host="143.48.1.250"
           expires="2005-04-25T12:50:00+00:00" />
    </LOCKS>

When authentication is in use some servers may restrict querying of
locks or restrict the information returned by the query.

After a lock has expired it can no longer be queried. Updating Locks

Once a lock has been created, it may be updated or deleted using PUT,
POST and DELETE requests as described earlier. Fields that can be
changed include the lock-expires attribute (e.g. to make the lock
persist longer) or nested tags contained within the object.

In this example, a client requests the extension of the existing lock's
expiration date for an additional hour, and extends the lock so that
applies to the region of Chr3 between 10000 and 60000:

REQUEST:

> `   PUT `[`http://www.wormbase.org/das/genome/volvox/1/lock/lock00004`](http://www.wormbase.org/das/genome/volvox/1/lock/lock00004)` `

    Content-Type: text/x-das-sources+xml

    <?xml version="1.0" standalone="no"?>
    <!DOCTYPE LOCKS SYSTEM "http://www.biodas.org/dtd/das2locks.dtd">
    <FEATURELIST
        xmlns="http://www.biodas.org/ns/das/genome/2.00"
        xmlns:das="http://www.biodas.org/ns/das/properties/2.00"
        xmlns:xlink="http://www.w3.org/1999/xlink"
        xml:base="http://www.wormbase.org/das/genome/volvox/1/lock/">
      <FEATURE      id="lock00004"
             lock-expires="2006-04-25T1:00:00+00:00" />
                lock_href="feature?inside=Chr3/10000:60000" /> 
    </FEATURELIST>

When authentication is in use a lock object can only be deleted or
modified by the owner of the lock, as indicated by lock-credentials. A
server implementation may, at its discretion, choose to implement a
"superuser" who is allowed to delete or modify any lock on the system.

After a lock has expired it can no longer be updated. Instead, a new
lock must be created.
