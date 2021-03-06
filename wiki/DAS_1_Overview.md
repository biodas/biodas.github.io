---
title: DAS/1/Overview
permalink: wiki/DAS/1/Overview/
layout: wiki
tags:
 - Documentation
---

<big>**An Overview of the Concepts Concerning the Distributed Annotation
System (DAS/1)**</big>

Information Source
------------------

The full specification, available at
<http://www.biodas.org/documents/spec-1.6.html>, serves as the primary
source for this document and will be plagiarized without explicit
notice. Any reference to this document will be made through the
convention <U>Specs</U>.

DAS Glossary
------------

(Definitions later defined are *italicized*; queries are **bolded**;
optional portions within names are \[bracketed\].)

### Distributed Annotation System (DAS)

A client-server system for the sharing of <I>Reference Sequences</I>, a
system<I> </I>conceptually composed of a <I>Reference Server </I>and
<I>Annotation Server(s)</I>.

### \[Reference\] Sequence

A sequence, consisting of a set of <I>entry points</I> into the sequence
and of the lengths of each entry point, which possesses a <I>reference
sequence ID</I>.

### \[Reference\] Sequence ID

The identification for a sequence, which corresponds to sequences of
either a low-level (<I>e.g.</I>, clones) or a high-level (<I>e.g.</I>,
contigs) and which is composed of any set of printable characters, save
for the colon, newline, tab, and carriage return characters.

### Entry point

A position defined for each genome at which the server may begin
dispensing data for a sequence, for a given length (of variable size),
<I>e.g.</I>, the head of a chromosome, the beginning of a series of
contigs, and the beginning of a contig. A list of entry points for a
given species may be retrieved via <B>entry\_points</B>.

### Annotation Server

A server specialized for returning lists of <I>annotations </I>across a
certain segment of the genome.

### Reference \[Sequence\] Server

An annotation server that, given a reference sequence ID, can also
return the following data:

1.  The raw DNA of the sequence;
2.  The annotations of the "component" of a <I>category
    </I>(<I>E.g.</I>, a contig is the component of a chromosome; thus,
    reference servers can return the annotation for a contig);
3.  The annotations of "supercomponents" of a <I>category
    </I>(<I>E.g.</I>, a chromosome is the supercomponent of a contig.).

### Annotation

An entity which:

1.  Is anchored to the genome map via a stop and start value relative to
    the reference subsequence;
2.  Possesses an ID unique to the server and a structured description of
    its nature and attributes;
3.  Optionally associated with Web URLs providing human-readable
    information about the annotation (via <B>link</B>);
4.  Possesses <I>types</I>, <I>methods</I>, and <I>categories</I>.

### \[Annotation\] Type

An entity selected from a list of types which have biological
significance and which roughly correspond to EMBL/GenBank feature table
tags, <I>e.g.</I>, exon, intron, CDS, and splice3.

### \[Annotation\] Method

A description of how the annotated feature was discovered, possibly
including a reference to a software program.

### \[Annotation\] Category

A intentionally broad functional genre that can be used to filter,
group, and sort annotations, <I>e.g.</I>, homology, variation, and
transcribed. (For the sake of consistency, <I>cf.</I> <U>Specs</U>:
"Feature Types and Categories" for a general list of types and
categories.)

### Source

A project containing data on DAS (a list of which may be retrieved via
<B>dsn</B>).

### Stylesheet

The server's non-binding recommendations on formatting retrieved
annotations for a given source (via <B>stylesheet</B>), using a General
Feature Format (GFF) document. (<I>Cf.</I>, <U>Specs</U>: "The Queries:
Retrieving the Stylesheet" and <U>Specs</U>: "Glyph Types.")

DAS Queries
-----------

A query can be made via a URL according to HTTP conventions, through
either GET or (more preferably because of size) POST. The response is
composed of:

1.  A standard HTTP header with DAS status information pertaining to the
    validity of the query (<I>cf.</I> <U>Specs</U>: "Client/Server
    Interactions: The Response.");
2.  (Optionally) an XML file containing the answer to the query,
    according to the specifications listed in <U>Specs</U>:
    "The Queries".

*PREFIX* denotes the URL prefix for the DAS server, e.g.,
<http://servlet.sanger.ac.uk:8080> is the prefix for
&lt;<http://servlet.sanger.ac.uk:8080/das/dsn%3E>. *DAS* denotes the
Data Source Name for a data source.

### dsn

> |            |                                                               |
> |------------|---------------------------------------------------------------|
> | *Command*  | *`PREFIX`*`/das/dsn`                                          |
> | *Function* | Retrieves the list of data sources available from this server |
> | *Scope*    | Reference and annotation servers                              |
>
### entry\_points

> |            |                                                                                 |
> |------------|---------------------------------------------------------------------------------|
> | *Command*  | *`PREFIX`*`/das/`*`DSN`*`/entry_points`                                         |
> | *Function* | Retrieves the list of entry points and their respective sizes for a data source |
> | *Scope*    | Reference servers                                                               |
>
### dna

> |            |                                                                          |
> |------------|--------------------------------------------------------------------------|
> | *Command*  | *`PREFIX`*`/das/`*`DSN`*`/dna?segment=`*`RANGE`*`[;segment=`*`RANGE`*`]` |
> | *Function* | Retrieves the DNA associated with a subsequence                          |
> | *Scope*    | Reference servers                                                        |
>
### sequence

> |            |                                                                               |
> |------------|-------------------------------------------------------------------------------|
> | *Command*  | *`PREFIX`*`/das/`*`DSN`*`/sequence?segment=`*`RANGE`*`[;segment=`*`RANGE`*`]` |
> | *Function* | Retrieves the sequence associated with a subsequence                          |
> | *Scope*    | Reference servers                                                             |
>
### types

> |            |                                                                                                |
> |------------|------------------------------------------------------------------------------------------------|
> | *Command*  | *`PREFIX`*`/das/`*`DSN`*`/types[?segment=`*`RANGE`*`][;segment=`*`RANGE`*`][;type=`*`TYPE`*`]` |
> | *Function* | Retrieves the types available for a segment of a sequence                                      |
> | *Scope*    | Reference and annotation servers                                                               |
>
### features

> |            |                                                                                                                                                                                          |
> |------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | *Command*  | *`PREFIX`*`/das/`*`DSN`*`//features?segment=`*`REF:start,stop`*`[;segment=`*`REF:start,stop`*`][;type=`*`TYPE`*`][;type=`*`TYPE`*`][;category=`*`CATEGORY`*`][;category=`*`CATEGORY`*`]` |
> | *Function* | Retrieves the annotations across a segment                                                                                                                                               |
> | *Scope*    | Reference and annotation servers                                                                                                                                                         |
>
### link

> |            |                                                                                   |
> |------------|-----------------------------------------------------------------------------------|
> | *Command*  | *`PREFIX`*`/das/`*`DSN`*`/link?field=`*`TAG`*`;id=`*`ID`*                         |
> | *Function* | Retrieves and HTML page describing human-readable information about an annotation |
> | *Scope*    | Reference servers                                                                 |
>
### stylesheet

> |            |                                          |
> |------------|------------------------------------------|
> | *Command*  | *`PREFIX`*`/das/`*`DSN`*`/stylesheet`    |
> | *Function* | Retrieves a stylesheet for the given DSN |
> | *Scope*    | Annotation servers                       |
>
Genome Assembly
---------------

In a client application, Genome Assembly consists of moving "up" or
"down" (the nomenclature of the <U>Specs</U>, analogous to zooming "in"
or "out"), along component children and supercomponent parent(s).[1]
Genome Assembly occurs only upon Reference Servers, a necessary
deduction from its definition. This data is contained within the TYPE
description for a feature. (*Cf.* <U>Specs</U>: "Fetching Sequence
Assemblies".)

Thus, in describing such a paradigm, the <U>Specs</U> appear to convey
that the client application will have to assemble information for a
given segment from its component children (i.e., moving down). (E.g., a
requested segment of a chromosome must be composed by the assembly of
several contigs.) Conversely, this paradigm simply facilitates the
client application to visit the supercomponent category (i.e., moving
up). (E.g., a user would like to zoom out from a contig to view the
entire chromosome.) However, the programmer should note well that it is
a logical possibility for a segment to span more than one supercomponent
parent (e.g., a segment may span two contigs).

**Notes**:

<references />
Extensions
----------

The reference DAS/1 specification has been extended with additional new
features, including an ontology and several new commands:

-   alignment
-   structure
-   interaction
-   volmap

For more information, see the [1.53E
specification](http://www.dasregistry.org/spec_1.53E.jsp) on the
[DasRegistry](/wiki/DasRegistry "wikilink") website.

[1] Following Lincoln Stein, the words *component* and *supercomponent*
refer to categories alone. (E.g., the category contig is a component of
the category chromosome, whereas chromosome is a supercomponent of
contig.) The words *children* and *parent(s)* refer to entities of the
given category. (E.g., contigs 17, 18, 19, and 20 are the children of
the parent chromosome 4).
