---
title: Dazzle Structure and Methodology
permalink: wiki/Dazzle_Structure_and_Methodology/
layout: wiki
---

Background information on Dazzle and it's structure/methodology.
----------------------------------------------------------------

If you wish to get stuck into coding straight away jump this secion. If
you wish to get an overview of the workings of Dazzle read-on. Your
dazzle servlet will be initialized through the init(..) method of the
DazzleServlet java class. this sets up the data handlers that you have
configured in your dazzlecfg.xml file in the form of the DazzleHandler
interface.

`public interface DazzleHandler {`  
`    public boolean accept(DazzleDataSource dds);`  
  
`    public String[] capabilities(DazzleDataSource dds);`  
  
`    public String[] commands(DazzleDataSource dds);`  
  
`    public void run(`  
`        DazzleServlet dazzle,`  
`        DazzleDataSource dds,`  
`        String cmd,`  
`        HttpServletRequest req,`  
`        DazzleResponse resp`  
`    )`  
`        throws IOException, ServletException, DataSourceException, DazzleException;`  
`}`

these DazzleHandlers handle the DazzleDataSource implementations for
your datasources that you have configured. The important method here is
the run(..) method that is called by the main DazzleServlet doGet(..)
when a cmd is made such as features or entry\_points that is not a top
level cmd (such as sources and is a command) that needs to be delegated
to the specific DAS source/DazzleDataSource.

the init method is below and is where any initialization errors will be
thrown if your configuration file is not implemented correctly or Dazzle
for some reason fails to start properly.

`/**`  
`    * Initialize DAS services, and load the XML configurations.`  
`    */`  
  
`   public void init(ServletConfig config)`  
`   throws ServletException`  
`   {`  
`       super.init(config);`  
  
`       // Initialize handlers`  
  
`       try {`  
`           Set handlerNames = Services.getImplementationNames(`  
`                   DazzleHandler.class,`  
`                   getClass().getClassLoader()`  
`           );`  
`           List`

All dazzle http get requests/DAS cmds go through the
org.biojava.servlets.dazzle.DazzleServlet classes

Sources cmd
-----------

For example the sources cmd is handled directly through the
DazzleServlet class, In the Main DazzleServlet.java class there is a
variable called DAS\_SOURCES\_LIST

`public static final String DAS_SOURCES_LIST   = "/sources.xml";`

in the same class there is a sendSources(..) method which returns by
default the sources.xml file contained in the dazzle-webapp dir or if
you are developing in eclipse the webcontent dir of eclipse. So to edit
the content of your soures.xml file you can open in any text or xml
editor and edit so that it conforms to the DAS 1.6 specification
(http://www.biodas.org/wiki/DAS1.6\#Response). Once your sources.xml
file is as it should be you can test it using the dasregistry validation
page at <http://www.dasregistry.org/validation.jsp> and select the
sources capability.

`/** DAS2- style sources description of the DAS sources, as is also supported by the DAS registry.`  
`    * e.g. see `[`http://www.dasregistry.org/das1/sources/`](http://www.dasregistry.org/das1/sources/)  
`    *`  
`    * @param req`  
`    * @param resp`  
`    * @throws ServletException`  
`    * @throws IOException`  
`    */`  
`   private void sendSources(HttpServletRequest req,`  
`           HttpServletResponse resp)`  
`   throws ServletException, IOException`  
`   {`  
  
`       //TODO: automatically build up the sources description form the data-sources`  
`       //add optional configuration fields to the dazzleconfig.xml file that allows`  
`       //to specify the information that can not be inferred automatically`  
  
`       System.out.println("getting sources listing from file: "  DAS_SOURCES_LIST );`  
  
`       InputStream is = getServletContext().getResourceAsStream(DAS_SOURCES_LIST);`  
  
  
`       resp.setContentType(XML_CONTENT_TYPE);`  
  
`       PrintWriter pw = resp.getWriter();`  
  
`       if (is != null) {`  
`           BufferedReader br = new BufferedReader(new InputStreamReader(is));`  
`           String line;`  
`           while ((line = br.readLine()) != null) {`  
`               pw.println(line);`  
`           }`  
`       }`  
`       pw.flush();`  
  
`       pw.close();`  
  
`   }`  

Entry Points cmd implementation in Dazzle:
------------------------------------------

As only DAS reference servers implement the entry\_points command you
need a class that implements the DazzleReferenceSource interface which
specifies that getEntryPoints must be implemented. A very basic
implementation is shown below:

`/** This method deals with the DAS -entry points command.`  
`    * @return a set containing the references to the entry points`  
`    */`  
`   public Set getEntryPoints() {`  
`       Set s = new TreeSet ();`  
`       // this example has only one feature.`  
`       // for your real data you might want to add a SQL query here.`  
`       s.add("123");`  
`       return s;`  
`   }`  

Sequence cmd
------------

This is a reference source only cmd so your class must implement the
DazzleReferenceSource which specifies the getSequence method below be
implemented: this method returns a Biojava Sequence object
org.biojava.bio.seq.Sequence.

Interface to be implemented:

`public Sequence getSequence(String ref)`  
`        throws NoSuchElementException, DataSourceException;`

Example implemented method:

`public Sequence getSequence(String ref)`  
`        throws NoSuchElementException, DataSourceException`  
`    {`  
`        Sequence seq = (Sequence) seqs.get(ref);`  
`        if (seq == null) {`  
`            throw new NoSuchElementException("No sequence "   ref);`  
`        }`  
`        return seq;`  
`    }`  

Types cmd
---------

f you are reading all your das features into memory as with a standard
GFF source does, then you would set up your a types list and then serve
this list using a method such as the one below:

setting up features in gff and recording types as well:

`public void addRecordLine(GFFRecord record) {`  
`        System.out.println("allTypes adding:" record.getFeature());`  
`       allTypes.add(record.getFeature());`  
`        String seq = record.getSeqName();`  
`        registerSeq(seq);`  
`        List seqGFF = (List) gffSets.get(seq);`  
`        if (seqGFF == null) {`  
`            seqGFF = new ArrayList();`  
`            gffSets.put(seq, seqGFF);`  
`        }`  
  
`        seqGFF.add(record);`  
`    }`

then you can get types by something like the following:

`protected Set allTypes;`  
  
`public Set getAllTypes() {`  
`        return Collections.unmodifiableSet(allTypes);`  
`    }`

note: unmodifiable method :Returns an unmodifiable view of the specified
list. This method allows modules to provide users with "read-only"
access to internal lists. Query operations on the returned list "read
through" to the specified list, and attempts to modify the returned
list, whether direct or via its iterator, result in an
UnsupportedOperationException.

Features Command:
-----------------

from the java docs it says we should use the
AbstractBiojavaFeatureSource as a starting point for writing data
sources: the important method obviously for this section is the
getFeatures(String ref) method.

`/**`  
` * Abstract DazzleDataSource implementation which provides default implementations`  
` * for many methods.  This is a useful starting point for writing many`  
` * data source implementations.`  
` *`  
` * @author Thomas Down`  
` * @version 1.00`  
` */`  
  
`public abstract class AbstractBiojavaFeatureSource`  
`    extends    AbstractDazzleDataSource`  
`    implements BiojavaFeatureSource`  
  
`/**`  
`     * Return all the features on the requested sequence`  
`     */`  
  
`    public FeatureHolder getFeatures(String ref)`  
`        throws DataSourceException, NoSuchElementException`  
`    {`  
`        return getSequence(ref);`  
`    }`  

getSequence is called and looks like this:

`/**`  
`     * Get the specified sequence.  This is used by the AbstractDataSource ``getFeatures``,`  
`     * ``getLandmarkLength``, and ``getFeaturesByID`  
`     * methods, and is a required part of the interface for DazzleReferenceSources.`  
`     */`  
  
`    public abstract Sequence getSequence(String ref)`  
`        throws DataSourceException, NoSuchElementException;`  
  
`this method is from the GFFAnnotationSource class which`  
  
`public FeatureHolder getFeatures(String ref)`  
`       throws NoSuchElementException, DataSourceException`  
`    {`  
`        ref = mapName(ref);`  
  
`        System.out.println("getFeatures for "   ref);`  
`        //System.out.println(gffSets.containsKey(ref));`  
`        if (!gffSets.containsKey(ref)) {`  
`            return null;`  
`        }`  
`        FeatureHolder features = (FeatureHolder) featureSets.get(ref);`  
`        if (features == null) {`  
`            try {`  
`                Sequence seq = getSequence(ref);`  
`                ViewSequence vseq = new ViewSequence(seq);`  
`                annotate(vseq);`  
  
`                features = vseq.getAddedFeatures();`  
`                featureSets.put(ref, features);`  
`            } catch (BioException ex) {`  
`                throw new DataSourceException(ex, "Error annotating sequence "   ref);`  
`            } catch (ChangeVetoException ex) {`  
`                throw new DataSourceException("ViewSequence isn't accepting features :(");`  
`            }`  
`        }`  
`        return features;`  
`    }`  

### Handling Error Segments

spec link is here
<http://www.biodas.org/wiki/DAS1.6#Exception_Handling_for_Invalid_Segments>
example from class FeaturesHandler which extends AbstractDazzleHandler (
a Handler which implements one or more DAS commands).

`private void featuresOutput_dasgff(`  
`           HttpServletRequest req,`  
`           DazzleResponse resp,`  
`           BiojavaFeatureSource dds,`  
`           Map segmentResults,`  
`           FeatureFilter generalFilter,`  
`           boolean categorize)`  
`   throws IOException, ServletException, DazzleException`  
`   {`  
  
`       XMLWriter xw = resp.startDasXML("DASGFF", "dasgff.dtd");`  
  
`       try {`  
`           xw.openTag("DASGFF");`  
`           xw.openTag("GFF");`  
`           xw.attribute("version", DASGFF_VERSION);`  
`           xw.attribute("href", DazzleTools.fullURL(req));`  
  
`           for (Iterator i = segmentResults.entrySet().iterator(); i.hasNext(); ) {`  
`               Map.Entry me = (Map.Entry) i.next();`  
`               Segment seg = (Segment) me.getKey();`  
`               Object segv = me.getValue();`  
`               if (segv instanceof FeatureHolder) {`  
`                   FeatureHolder features = (FeatureHolder) segv;`  
  
`                   xw.openTag("SEGMENT");`  
`                   xw.attribute("id", seg.getReference());`  
`                   xw.attribute("version", dds.getLandmarkVersion(seg.getReference()));`  
`                   if (seg.isBounded()) {`  
`                       xw.attribute("start", ""   seg.getStart());`  
`                       xw.attribute("stop", ""   seg.getStop());`  
`                   } else {`  
`                       xw.attribute("start", "1");`  
`                       xw.attribute("stop", ""   dds.getLandmarkLength(seg.getReference()));`  
`                   }`  
`                   // TODO: Labels here?`  
  
`                   FeatureFilter ff = featuresOutput_buildSegmentFilter(generalFilter, seg);`  
`                   features = features.filter(ff, true);`  
  
`                   List nullList = Collections.nCopies(1, null);`  
  
`                   for (Iterator fi = features.features(); fi.hasNext(); ) {`  
`                       Feature feature = (Feature) fi.next();`  
  
`                       if (dds.getShatterFeature(feature)) {`  
`                           int idSeed = 1;`  
`                           String baseID = dds.getFeatureID(feature);`  
`                           List baseGroupList = dds.getGroups(feature);`  
`                           for (Iterator bi = feature.getLocation().blockIterator(); bi.hasNext(); ) {`  
`                               Location shatterSpan = (Location) bi.next();`  
`                               List groupList = new ArrayList();`  
`                               groupList.add(feature);`  
`                               groupList.addAll(baseGroupList);`  
`                               writeDasgffFeature(dds, xw, feature, groupList, shatterSpan, baseID   "-"   (idSeed  ), categorize);`  
`                           }`  
`                       } else {`  
`                           List groups = dds.getGroups(feature);`  
`                           writeDasgffFeature(dds, xw, feature, groups, null, null, categorize);`  
`                       }`  
`                   }`  
  
`                   xw.closeTag("SEGMENT");`  
`               } else if (segv instanceof String) {`  
`                   xw.openTag("ERRORSEGMENT");`  
`                   xw.attribute("id", seg.getReference());`  
`                   if (seg.isBounded()) {`  
`                       xw.attribute("start", ""   seg.getStart());`  
`                       xw.attribute("stop", ""   seg.getStop());`  
`                   }`  
`                   xw.closeTag("ERRORSEGMENT");`  
`               } else if (segv == null) {`  
`                   xw.openTag("UNKNOWNSEGMENT");`  
`                   xw.attribute("id", seg.getReference());`  
`                   xw.closeTag("UNKNOWNSEGMENT");`  
`               }`  
`           }`  
  
`           xw.closeTag("GFF");`  
`           xw.closeTag("DASGFF");`  
`           xw.close();`  
`       } catch (Exception ex) {`  
`           throw new DazzleException(ex, "Error writing DASGFF FEATURES document");`  
`       }`  
`   }`

Stylesheet cmd
--------------

spec for the stylesheet cmd is here
<http://www.biodas.org/wiki/DAS1.6#Stylesheet_Command> e.g.
<http://localhost:8080/das/tss/stylesheet>

not to be confused with the css or xsl stylesheets this stylesheet cmd
should respond with a xml document for specifying how a track should be
displayed in a browser example:

`<DASSTYLE>`  
`  <STYLESHEET>`  
`    <CATEGORY id="default">`  
`      <TYPE id="TSS">`  
`        <GLYPH>`  
`     <TICK>`  
`       <HEIGHT>25>HEIGHT>`  
`       <COLOR>blue</COLOR>`  
`       <OUTLINECOLOR>black</OUTLINECOLOR>`  
`     </TICK>`  
`   </GLYPH>`  
`      </TYPE>`  
`    </CATEGORY>`  
`  </STYLESHEET>`  
`</DASSTYLE>`  

the location of this is file is specified by you when you set up your
source in dazzlecfg.xml. You can see the example in the TSS source that
is packaged with the basic dazzle installation and looks like this:

`<string name="stylesheet" value="/tss.style" />`

and it's contained in the element. This points to the root of the
WebContent directory (if you are building in eclipse) and must have a
"/" in front of it. Of course if you wish to keep your web content
directory neater you can specify a stylesheet directory within it and
put it there and modify the stylesheet element of your dazzlecfg.xml
source accordingly.

`<datasource id="tss" jclass="org.biojava.servlets.dazzle.datasource.GFFAnnotationSource">`  
`    <string name="name" value="TSS" />`  
`    <string name="description" value="Transcription start sites" />`  
`    <string name="version" value="default" />`  
`    <string name="fileName" value="/fickett-tss.gff" />`  
`    <boolean name="dotVersions" value="true" />`  
`    <string name="mapMaster" value="`[`http://localhost:8080/das/test/`](http://localhost:8080/das/test/)`" />`  
`    <string name="stylesheet" value="/stylesheet/tss.style" />`  
`  </datasource>`
