---
title: DAS/2.1/Spec/Get-Genomic/Sources
permalink: wiki/DAS/2.1/Spec/Get-Genomic/Sources/
layout: wiki
---

**<big>[DAS/2.1](/wiki/DAS/2.1/Spec/Get-Genomic "wikilink") Sources Document
Specification for Genome Data Retrieval</big>**

Overview
--------

A DAS server supplies information about genomic sequence data sources.
The collection of all sources, each data source, and each version of a
data source are accessible through a URL. All three classes of URLs
return a document of content-type 'application/x-das-sources+xml' though
likely with differing amounts of detail. A 'versioned source' request
returns information only about a specific version of a data source. A
'source' request returns the list of all the versioned source data for
that source. A 'sources' request returns the list of all the source
data, including all the versioned source data.

The URLs might not be distinct. For example, a server with only one
version of one data source may use the same URL for all three documents,
and a server for a single organism may use the same URL for the
'sources' and 'source' documents.

Most servers will list only the data sources provided by that server.
Some servers combine the sources documents from other servers into a
single document. These registry servers act as a centralized index and
reduce configuration and network overhead. A registry server uses the
same sources format as an annotation server.

Here is an example of a simple sources document which makes no
distinction between the three sources categories. It, like the other new
DAS formats, is in XML. All of the DAS elements are in the XML namespace
[`http://biodas.org/documents/das2`](http://biodas.org/documents/das2).
This namespace is reserved and authors of DAS extensions may not create
new XML elements in it.

### Example request and response

Request:

> <http://www.example.com/das/genome/yeast.xml>

Response:

    Content-Type: application/x-das-sources+xml

    &lt;?xml version="1.0" encoding="UTF-8"?&gt;
    &lt;SOURCES xmlns="http://biodas.org/documents/das2"
              xml:base="http://www.example.com/das/genome/"&gt;

      &lt;SOURCE uri="yeast.xml" title="Saccharomyces cerevisiae (Baker's yeast) genome"
             doc_href="http://www.example.com/yeast.html"&gt;
        &lt;VERSION uri="yeast.xml" created="2005-12-05"&gt;
          &lt;COORDINATES uri="http://sanger.ac.uk/das-registry/yeast-32-gene"
                 taxid="4932" source="Gene_ID" authority="SGD32" version="3"/&gt;
          &lt;CAPABILITY type="features" query_uri="features.xml" /&gt;
          &lt;CAPABILITY type="types" query_uri="types.xml"/&gt;
        &lt;/VERSION&gt;
      &lt;/SOURCE&gt;

    &lt;/SOURCES&gt;

All identifiers and href attributes in DAS documents follow the [XML
Base specification](http://www.w3.org/TR/xmlbase/) when resolving
partial identifiers and href attributes. In this case the relative id
"yeast.xml" is fully resolved using the xml:base of
"<http://www.example.com/das/genome/>" to
"<http://www.example.com/das/genome/yeast.xml>". If the result after
resolving through all the parent xml:base attributes is still a relative
URL then it is resolved once more with respect to the URL used to fetch
the document.

### Example request and response (complex)

Here is an example of a more complicated sources document with multiple
organisms each with multiple versions. Each of the two source documents
(one for each organism) has a distinct URL as does each of the version
for each organism. This is a pure registry server because the actual
annotation data comes from other machines.

Request:

> <http://www.biodas.org/known_das_servers>

Response:

    Content-Type: application/x-das-sources+xml

    &lt;SOURCES xmlns="http://biodas.org/documents/das2"&gt;
      &lt;SOURCE uri="http://das.ensembl.org/das/SPICEDS/" title="das_vega_trans"&gt;
        &lt;VERSION uri="http://das.ensembl.org/das/SPICEDS/127/" created="2005-05-23"&gt;
          &lt;MAINTAINER email="someone@sanger.ac.uk" /&gt;
          &lt;COORDINATES uri="http://sanger.ac.uk/das-registry/zv4-25-chr" taxid="7955"
                   source="Chromosome" authority="ZV4" version="25"
                   test_range="name=BX255914" /&gt;
          &lt;CAPABILITY type="segments"
                 query_uri="http://www.ebi.ac.uk/das-srv/genome/zebrafish-62" /&gt;
          &lt;CAPABILITY type="features"
               query_uri="http://das.ensembl.org/das/SPICEDS/127/features"&gt;
            &lt;SUPPORTS name="das2queries" /&gt;
          &lt;/CAPABILITY&gt;
          &lt;CAPABILITY type="types"
               query_uri="http://das.ensembl.org/das/SPICEDS/127/types" /&gt;
        &lt;/VERSION&gt;

        &lt;VERSION uri="http://das.ensembl.org/das/SPICEDS/128/" created="2005-08-13"&gt;
          &lt;MAINTAINER email="someone-else@sanger.ac.uk" /&gt;
          &lt;COORDINATES uri="http://sanger.ac.uk/das-registry/zv4-26-chr" taxid="7955"
                   source="Chromosome" authority="ZV4" version="26"
                   test_range="name=BX255914" /&gt;
          &lt;CAPABILITY type="segments"
                 query_uri="http://www.ebi.ac.uk/das-srv/genome/zebrafish-62" /&gt;
          &lt;CAPABILITY type="features"
               query_uri="http://das.ensembl.org/das/SPICEDS/128/features"&gt;
            &lt;SUPPORTS name="das2queries" /&gt;
          &lt;/CAPABILITY&gt;
          &lt;CAPABILITY type="types"
               query_uri="http://das.ensembl.org/das/SPICEDS/128/types" /&gt;
          &lt;CAPABILITY type="locks" query_uri="http://das.ensembl.org/das/SPICEDS/128/locks" /&gt;
          &lt;CAPABILITY type="writeback"
               query_uri="http://das.ensembl.org/das/SPICEDS/128/locks" /&gt;
        &lt;/VERSION&gt;
      &lt;/SOURCE&gt;

      &lt;SOURCE uri="http://www.example.com/das2/mus/sources.xml" title="Mus musculus"&gt;
        &lt;VERSION uri="http://www.example.com/das2/mus/42/sources.xml" created="2006-02-11"&gt;
          &lt;MAINTAINER email="pied-piper@hamlet.ac.uk" /&gt;
          &lt;COORDINATES uri="http://sanger.ac.uk/das-registry/yeast-12-clone" taxid="10090"
                    source="Clone" authority="Ensembl" test_range="name=AL935121" /&gt;
          &lt;CAPABILITY type="features"
               query_uri="http://www.example.com/cgi-bin/features-mus-v42.cgi"&gt;
            &lt;SUPPORTS name="das2queries" /&gt;
          &lt;/CAPABILITY&gt;
          &lt;CAPABILITY type="types"
               query_uri="http://www.example.com/das2/mus/v42/types.xml" /&gt;
        &lt;/VERSION&gt;
      &lt;/SOURCE&gt;
    &lt;/SOURCES&gt;

Each SOURCE id and VERSION id is fetchable. Fetching the URL
"<http://das.ensembl.org/das/SPICEDS/>" returns a sources document with
the SOURCE record for "das\_vega\_trans" and both of its VERSION
subelements and fetching "<http://das.ensembl.org/das/SPICEDS/128/>"
returns a sources document with only the second of its VERSION
subelements.

DAS documents refer to other documents through URLs. There are no
restrictions on the internal form of the URLs, other than the query
string portion. Server implementers are free to choose URLs which best
fit the architecture needs. For example, a simple DAS server may be
implemented as a set of XML files hosted by a standard web server while
more complex servers with search support may be implemented as CGI
scripts or through embedded web server extensions. The URLs do not need
to define a hierarchical structure nor even be on the same machine.
Compare this to the DAS1 specification where some URLs were constructed
by direct string modification of other URLs.

Details
-------

A sources request is a request for information about the data sets
available from a DAS server. This may be a list of all data sources, a
list of all versions of a given data source, or information about a
specific version. All three are done by fetching a sources document
given a URL. The returned format is identical for all three cases,
except that some portions will require one element instead a list of
zero or more elements.

The sources request does not use query parameters. A future version of
DAS/2 may add optional query parameters to the sources request, but for
now servers should respond with an HTTP error code 400 "Bad Request" if
any query parameters are included.

### Example response document

*\[Do we need this example in addition to the above?\]*

The response document looks like the following:

Response:

    Content-Type: application/x-das-sources+xml

    &lt;?xml version="1.0" encoding="UTF-8"?&gt;
    &lt;SOURCES
        xmlns="http://biodas.org/documents/das2"
        xml:base="http://dev.wormbase.org/das/genome/"&gt;

      &lt;MAINTAINER
        name="Yoyodyne DNA Systems"
        email="yoyodyna@example.com"
        href="http://www.example.com/" /&gt;

      &lt;SOURCE uri="volvox" title="Volvox Database"
          doc_href="http://www.example.org/volvox_db.pdf"&gt;

        &lt;VERSION uri="volvox/build_1" title="Build 1, October 2002"
               writeable="no" created="2002-10-15" modified="2002-10-25T09:56:23"&gt;

          &lt;MAINTAINER
            name="Volvox helpdesk"
            email="volvox-help@example.com" /&gt;
          &lt;COORDINATES uri="http://ncbi.nlm.nih.gov/das-genomes/human-35"
                       taxid="3066" source="chromosome" authority="NCBI" version="35" /&gt;

          &lt;COORDINATES uri="http://embl.ebi.ac.uk/genome/volvox-clone"
                       taxid="2034" source="clone" authority="EMBL" /&gt;

          &lt;CAPABILITY type="segments" query_uri="volvox/1/segments"&gt;
              &lt;FORMAT name="fasta"/&gt;
              &lt;FORMAT name="raw"/&gt;
          &lt;/CAPABILITY&gt;
          &lt;CAPABILITY type="types" query_uri="volvox/1/types"&gt;
              &lt;FORMAT name="das2xml" /&gt;
          &lt;/CAPABILITY&gt;
          &lt;CAPABILITY type="features" query_uri="volvox/1/features"&gt;
              &lt;FORMAT name="das2xml" /&gt;
          &lt;/CAPABILITY&gt;
          &lt;CAPABILITY type="locks" query_uri="volvox/1/locks" /&gt;

        &lt;/VERSION&gt;
      &lt;/SOURCE&gt;
    &lt;/SOURCES&gt;

### SOURCES element

The root element is named SOURCES. The MAINTAINER element is optional. A
server should provide at least one of the 'name', 'email' or 'href'
attributes. The 'name' is short human-readable text, the 'email' is an
email address and the 'href' is a URL meant for a human using a web
browser. The MAINTAINER element under the SOURCES element designates the
maintainer for the server, which may be different than the maintainer
for the data sources.

The SOURCES element has zero or more <a href="#link_element">LINK
elements</a> linking the sources document to some other document.

The SOURCES element has zero or more SOURCE elements. The 'uri'
attribute is a URI and must be a unique identifier within a SOURCES
document. It should be fetchable and if fetchable it must respond with a
sources document describing the given data source. The 'title' is a
short label describing the source to people. The optional 'description'
contains up to a paragraph of text (no HTML markup) with more details
about the data source. The optional 'doc\_href' is a URL for a web
browser to display human-readable documentation.

### MAINTAINER element

A SOURCES element may have an optional MAINTAINER element. The syntax is
the same as the one at the SOURCES level. If present it contains the
contact information for the maintainer of the given data source. If not
present clients may use the SOURCES MAINTAINER as the SOURCE MAINTAINER.

### VERSION element

A data source may have multiple VERSION elements. The definition of what
constitutes a new version is left to the data provider. VERSION elements
should be listed in creation time order, with the oldest versions first.
The 'uri' attribute is a URI and must be a unique identifier within the
data source. It should be fetchable and if fetchable it must respond
with a source document describing only the specific versioned data
source. The 'title' is a short label describing the version to people.
The optional description contains up to a paragraph of text (no HTML
markup) with more details about the version. The optional 'doc\_href' is
a URL for a web browser to display human-readable documentation.

The optional 'created' and 'modified' attributes are ISO timestamps
specifying when the version was first made available and most recently
modified.

Each VERSION element may contain an optional MAINTAINER element, which
has the same syntax and meaning as the MAINTAINER element at the SOURCES
and SOURCE level. It contains the contact information for the maintainer
of the specific version of the data source, which may be different than
the maintainer for the data source or for the server. If the VERSION
MAINTAINER is not present, clients may use the SOURCE MAINTAINER instead
for contact information and if that does not exist clients may use the
SOURCES MAINTAINER.

### COORDINATES element

The COORDINATES element characterizes a coordinate system used by the
versioned source. Each COORDINATES has a 'uri' attribute which exactly
identifies the coordinate system. If two annotation servers have a
COORDINATES with the same uri then they annotatate the same system.
Coordinate system URIs for human and many model organism genomes are
currently listed at <http://www.biodas.org/wiki/GlobalSeqIDs>. This list
will soon be incorporated into the Sanger DAS/2 registry.

To help people select an appropriate coordinate system to use, the
COORDINATES element contains a set of attributes with additional
details. The 'authority' attribute is the name of the organization that
determined the coordinate system. It is a name like 'NCBI', 'EMBL',
'Ensembl', 'HUGO\_ID', 'IPI' or 'UniProt'. The 'source' attribute refers
to the physical dimension of the coordinate system. It is a name like
'Chromosome', 'Clone', 'Contig', 'Gene\_ID', 'NT\_Contig', 'Protein
Sequence', 'Protein Structure', or 'Scaffold'. The Sanger registry
contains a full list of these restricted vocabulary terms.

The optional 'version' contains the version name of the build upon which
the coordinate system is based. The optional 'created' attribute is the
ISO timestamp for the coordinate system and is used when sorting by
time, as when identifying the most recent version. The optional 'taxid'
attribute is the NCBI taxonomy id of the organism, as a number.

The COORDINATES element may contain an optional 'test\_range' attribute
used to test that the server is operational. Experience with DAS1 found
that the web interface code often did not catch errors at the database
interface layer and would return empty results instead of correctly
reporting errors. The test\_range attribute contains a
<a href="#feature_filters">feature filter query string</a> which can be
passed to the "feature" CAPABILITY query\_uri. The response after doing
that feature filter request must contain at least one feature and should
contain no more than a modest number.

A versioned source may have more than one COORDINATES element. For
example, features may be given in both chromosome and contig space, or
in human and mouse coordinates.

### CAPABILITY element

The CAPABILITY elements describe what sort of queries a client may do
with the versioned source data. The query is done through the URL listed
in the 'query\_uri' field. Different query URLs support different query
interfaces. The specific interface is listed in the 'type' field. The
specification defines the following query URL types:

<table border="1">
<tr>
<th>
'type' value

</th>
<th>
for information about

</th>
</tr>
<tr>
<td>
segments

</td>
<td>
description of the regions on which features are located

</td>
</tr>
<tr>
<td>
types

</td>
<td>
the feature types

</td>
</tr>
<tr>
<td>
features

</td>
<td>
the features

</td>
</tr>
<tr>
<td>
locks

</td>
<td>
the locks

</td>
</tr>
<tr>
<td>
writeback

</td>
<td>
writeback support

</td>
</tr>
</table>
Relative 'query\_uri's are resolved according to the current xml:base.

A CAPABILITY has zero or more FORMAT elements, each with a 'name'
attribute. These list the supported formats for the given capability. To
get the document in a given format, use the format's name in the
"format" parameter of the query.

A CAPABILITY has zero or more SUPPORTS elements, each with a 'name'
attribute. These list the available extensions supported by the given
capability.

The [segments](/wiki/DAS/2.1/Spec/Get-Genome/Segments "wikilink") portion of
the specification describes in more detail how the FORMAT and SUPPORTS
elements are used.
