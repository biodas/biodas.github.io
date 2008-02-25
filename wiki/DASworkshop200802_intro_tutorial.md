---
title: DASworkshop200802:intro tutorial
permalink: wiki/DASworkshop200802:intro_tutorial/
layout: wiki
---

[ &lt;-- Back to DAS Workshop 2008
Programme](/wiki/DASworkshop200802 "wikilink")

Tutorial: An Introduction to the Basics of DAS
==============================================

Introduction
------------

By making use of DAS you can take advantage of being able to view
integrated information from multiple sources, without these sources
needing to be aware of each other. You can also add your own DAS data
source, perhaps privately in your own institution and then view the
information served from this source in the context of features from
other institutions.

This tutorial will enable you to understand how DAS servers function and
how you can find relevant DAS data sources.

Example DAS Reference Server – The UniProt DAS Reference Server
---------------------------------------------------------------

This section will illustrate the structure of typical DAS requests and
responses, including those that you might expect to see when things go
wrong - the entry point you have asked for is not available for example.

DAS information is made available from DAS servers. A DAS server is a
web application that serves data in the form of a series of XML
documents that can be read and processed by DAS client software. There
are several different types of XML document, each having a specific
function, such as:

-   providing the sequence of the molecule (nucleic acid or protein);
-   providing details of the features coordinated on a molecule;
-   providing a summary of the features coordinated on a molecule.

If features on a specific protein are being obtained from several
different locations, it is very useful to ensure that one reliable,
common sequence has been obtained. To achieve this, DAS servers are
separated into two kinds:

-   **Reference servers** provide the molecule sequence.
-   **Annotation servers** provide features upon the sequence, such as
    gene structures, regulatory motifs, protein domains, or related to
    the protein, such as journal references. Annotation servers often
    refer to a specific reference server as their 'map master', i.e. the
    server that you should expect to be able to retrieve the
    corresponding sequence from. This mechanism has become less
    important since the inception of the DAS Registry Server however.

You will now investigate an example of each kind of server.

The UniProt DAS Reference Server
--------------------------------

The UniProt DAS reference server has the primary purpose of providing
sequence information to DAS clients from the UniProt Knowledge Bank. It
can be queried using UniProt accession numbers, Swiss-Prot protein Ids
and also IPI (International Protein Index) accessions.

> ***Navigate to <http://www.ebi.ac.uk/das-srv/uniprot/das/dsn> Here you
> will find a summary page describing the UniProt DAS server and listing
> the data sources it provides.*** *(If you look at the source of the
> page, you will see that the browser view is actually an* **XSLT
> transformation** *of the DAS XML returned by the dsn command. The use
> of this technology with DAS servers is becoming increasingly common.)*

> ***Now navigate to
> <http://www.ebi.ac.uk/das-srv/uniprot/das/uniprot/sequence?segment=Q14974>***

You are now presented with a very simple XML file that contains the
sequence of the protein Q14974 (Importin beta-1 subunit). This is the
primary function of the UniProt DAS server. Note that the server also
indicates the length of the protein as an integer and provides a version
in the form of an MD5 digest of the sequence. This is used to allow DAS
clients to allow a check that all the DAS sources are referring to the
same version of the protein sequence as the reference server.

In addition to being a reference server, the UniProt DAS server also
acts as an annotation server, allowing the client to query details of
features in the UniProt knowledge bank. This also incorporates InterPro
features.

> ***Browse to
> <http://www.ebi.ac.uk/das-srv/uniprot/das/uniprot/features?segment=Q14974>***

> ***Take a careful look at the result of this search. You should be
> able to find positional features (note that the start and end
> coordinates of each feature are included) including Swiss-Prot
> annotation, non-positional features such as a description of the
> protein (with start and end coordinates of 0) and also literature
> references relating to the protein.***

It is possible for a DAS client to query a coordinate range on an entry
point.

> ***Take a look at the [definition of the features command in the DAS
> 1.53 specification](http://biodas.org/documents/spec.html#features).
> Use this information to modify the previous query to include only the
> features located between residues 20 and 100 on the protein
> sequence.***

As indicated above, the purpose of DAS is to allow you to retrieve
protein information from multiple sources at the same time. An example
of a completely separate but compatible DAS annotation server that is
able to contribute further annotation of the same protein is given in
the next task:

> ***Navigate to
> <http://www.ebi.ac.uk/msd-srv/msdmotif/das/s3dm/features?segment=Q14974>***

Here you will find additional annotation of the same protein from the
MSD Motif database at the EBI. Notice that in this case the version is
not the same as the version from the UniProt DAS server for the same
protein accession. This could mean that the MSD DAS server is not
providing annotation on the same version of the sequence being served
from the UniProt DAS server. A typical DAS client should note this
discrepancy and warn the user that the protein sequence being annotated
is possibly not the same.

> ***Navigate to
> <http://bioinf.cs.ucl.ac.uk:8000/servlet/pdas.pdasServlet2/das/features?segment=Q14974>***

This is the annotation server based at UCL in London that also provides
features for the same protein. In this case (at the time of writing) the
version is the same as the UniProt DAS server.

Clearly there are potentially many different DAS servers that may
provide annotation that is useful for you. The next section describes
how you can manually find DAS services.

Finding DAS sources – the DAS Registry Service at the Sanger Institute
----------------------------------------------------------------------

<center>
**<http://www.dasregistry.org>**

</center>
*“The purpose of the DAS registration service is to keep track which DAS
services are around, which DAS commands they are understanding and about
the coordinate systems of their data.”*<sup>1</sup>

The DAS Registration Server allows DAS clients to discover DAS services.
At the same time it provides an elegant browsable web interface that
allows you to search for DAS sources, test their status, examine their
reliability and learn more about what they offer. At the time of
writing, a total of 383 servers from 47 institutions in 18 different
countries are included in the registry.

The following activities will familiarise your with the content of the
registry, both the 'human' interface and also the web-service interface
that can be queried by DAS clients (and your client code!)

> ***Navigate to [The DAS Registration
> Server](http://www.dasregistry.org)***

> ***Follow the "list" ... "list sources" menu item at the top of this
> page. On the resulting page, find all DAS annotation servers that use
> UniProt as their 'authority'.***

> ***Select any one of these services and follow the information link
> ('i' in a blue circle) to find out details of this server including
> how reliable it has been recently.***

> ***For the same DAS service, use the DAS Registration Server to test
> its current capabilities to find out if it is supporting all of the
> functionality that it claims to at the present time. (Start with the
> green tick)***

> ***view-source: <http://www.dasregistry.org/das1/sources>***

> ***view-source: <http://www.dasregistry.org/das1/sources/DS_109>***

> ***<http://www.dasregistry.org/coordsys/CS_DS91>***

> ***<http://www.dasregistry.org/listServices.jsp>***

> ***<http://www.dasregistry.org/listProjects.jsp>***

For details of web services and WSDL:

> ***Finally, you may like to take a look at the [DAS Registry Server
> Help with Scripting
> Page](http://www.dasregistry.org/help_scripting.jsp) that provides
> more information about how to use the available web services in your
> own scripts.***