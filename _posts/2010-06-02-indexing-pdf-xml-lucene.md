---
layout: post
header-img: img/default-blog-pic.jpg
author: tarunsapra
description: 
post_id: 3775
created: 2010/06/02 22:56:55
created_gmt: 2010/06/02 17:56:55
comment_status: open
---

<!--Index and Search meta-data rich text documents using Lucene.-->

# Indexing PDF, XML, Office docs with Lucene

Since Lucene by default does not handle anything but _java.lang.String _and_ java.io.Reader_ for indexing, we have to do something extra to handle the indexing for PDF, Microsoft Word and Excel. In this blog we'll talk about indexing these metadata documents.

We will take a look at how to extract text from these meta-data documents and how to use Lucene's Core APIs to index and search. To get a snapshot on Lucene's Core APIs, you may want to visit ["Jump Start Lucene Indexing and Search"][1] blog.

So let’s begin with looking at how to extract text from **PDF**. For PDF, we'll be using the [PDFBox][2] open source API from Apache. We'll extract text as well as other information like author, title etc. Once we have the required texts available, we'll put those texts as the value of the [Field ][3]class of Lucene and add these Fields to the [Document][4] class.  So let's take a look at the code:

[java] public Document getDocument(InputStream is) throws Exception { COSDocument cosDoc = null; cosDoc = parseDocument(is); PDDocument pdDoc = new PDDocument(cosDoc); String docText = null; PDFTextStripper stripper = new PDFTextStripper(); docText = stripper.getText(pdDoc); Document doc = new Document(); if (StringUtils.isNotEmpty(docText)) { doc.add(new Field("content", docText,Field.Store.NO,Field.Index.ANALYZED)); } // extract PDF document's meta-data

PDDocumentInformation docInfo = pdDoc.getDocumentInformation(); String author = docInfo.getAuthor(); String title = docInfo.getTitle(); String keywords = docInfo.getKeywords(); String summary = docInfo.getSubject(); if (StringUtils.isNotEmpty(author)) {

doc.add(new Field("author", author,Field.Store.YES,Field.Index.NOT_ANALYZED)); } if (StringUtils.isNotEmpty(title)) { doc.add(new Field("title", title ,Field.Store.YES,Field.Index.ANALYZED)); } if (StringUtils.isNotEmpty(keywords)) {

doc.add(new Field("keywords", keywords, Field.Store.YES,Field.Index.ANALYZED)); } if (StringUtils.isNotEmpty(summary)) { doc.add(new Field("summary", summary,Field.Store.YES,Field.Index.ANALYZED)); }

return doc; }

private static COSDocument parseDocument(InputStream is) throws IOException { PDFParser parser= null; parser = new PDFParser(is);

parser.parse(); return parser.getDocument(); } [/java]

Above method `getDocument()` takes the `InputStream` of PDF document as an argument. Since this method returns a `Document` instance, it can be added to an `IndexWriter` as follows:

[java] doc.add(new Field("filename",new File(String pdfLocation).getCanonicalPath(),Field.Store.YES,Field.Index.NOT_ANALYZED)); IndexWriter writer = new IndexWriter(indexDir,new StandardAnalyzer(),IndexWriter.MaxFieldLength.UNLIMITED);//indexDir is the location of the Index writer.addDocument(doc); [/java]

In the above code we have also added the complete canonical path of the file to the Document instance so that while searching the text we can retrieve the full file name. Now for **XML** we will use [SAX][5] parser to extract the text.

[java] public Document getDocument(InputStream is) throws Exception { StringBuilder elementBuffer = new StringBuilder(); HashMap<String, String> attributeMap; Document doc; StringBuilder contents = new StringBuilder(); SAXParserFactory spf = SAXParserFactory.newInstance(); try { SAXParser parser = spf.newSAXParser(); parser.parse(is, this); } catch (IOException e) { throw new Exception("Cannot parse XML document", e); } catch (SAXException e) { throw new Exception("Cannot parse XML document", e); } return doc; }

public void startDocument() { doc = new Document(); }

public void startElement(String uri, String localName, String qName, Attributes atts) throws SAXException { elementBuffer.setLength(0);

if (atts.getLength() > 0) { attributeMap = new HashMap<String,String>(); for (int i = 0; i < atts.getLength(); i++) { attributeMap.put(atts.getQName(i), atts.getValue(i)); } } }

// called when cdata is found public void characters(char[] text, int start, int length) { elementBuffer.append(text, start, length); contents.append(elementBuffer.toString()).append(" "); System.out.println(contents.toString());

}

public void endElement(String uri, String localName, String qName) throws SAXException { if(attributeMap !=null) { Iterator<String> iter = attributeMap.keySet().iterator(); while (iter.hasNext()) { String attName = (String) iter.next(); String attValue = (String) attributeMap.get(attName); doc.add(new Field(attName, attValue,Field.Store.YES , Field.Index.ANALYZED));

} }

doc.add(new Field(qName, elementBuffer.toString(),Field.Store.YES , Field.Index.ANALYZED));

} public void endDocument() { //The all in one field which contains all the data. doc.add(new Field("content",contents.toString(),Field.Store.YES , Field.Index.ANALYZED)); } [/java]

Now since the above method returns the Document instance, we can add this Document instance to the IndeWriter

[java] doc.add(new Field("filename",new File(String XmlLocation).getCanonicalPath(),Field.Store.YES,Field.Index.NOT_ANALYZED)); writer.addDocument(doc); [/java]

Now let us look at the scenario for **Excel 2007** documents. We will be using [Apache poi API ][6]for text extraction

[java] public Document getDocument(InputStream is) throws Exception { StringBuilder contents = new StringBuilder(); POIFSFileSystem fileSystem = new POIFSFileSystem(is); HSSFWorkbook workBook = new HSSFWorkbook(fileSystem); for (int i =0; i<workBook.getNumberOfSheets();i++) { HSSFSheet sheet = workBook.getSheetAt(i); Iterator<Row> rows = sheet.rowIterator(); while (rows.hasNext()) { HSSFRow row = (HSSFRow)rows.next(); //Display the row number System.out.println(row.getRowNum()); Iterator<Cell> cells=row.cellIterator(); while(cells.hasNext()) { HSSFCell cell = (HSSFCell) cells.next(); //Disply the cell number of the current Row

switch(cell.getCellType()) { case HSSFCell.CELL_TYPE_NUMERIC : { System.out.println(String.valueOf(cell.getNumericCellValue())); contents.append(String.valueOf(cell.getNumericCellValue())).append(" "); break; } case HSSFCell.CELL_TYPE_STRING : {

HSSFRichTextString richTextString =cell.getRichStringCellValue(); System.out.println(richTextString.toString()); contents.append(richTextString.toString()).append(" "); break; } case HSSFCell.CELL_TYPE_BOOLEAN : { contents.append(String.valueOf(cell.getBooleanCellValue())).append(" "); break; } }

} }

} Document doc = new Document(); doc.add(new Field("content",contents.toString(),Field.Store.YES,Field.Index.ANALYZED)); System.out.println(contents.toString()); return doc; } [/java]

So again from the above method you can see we are getting Document instance which can be added to the IndexWriter instance _writer_. Let us now see the scenario for **Word 2007** documents. We will again use [Apache POI API][7] for text extraction.

[java] public Document getDocument(InputStream is) throws POIXMLException,Exception{ String bodyText = null; StringBuilder content = new StringBuilder(); try { XWPFDocument wd = new XWPFDocument(is); XWPFWordExtractor wde = new XWPFWordExtractor(wd); bodyText = wde.getText();

}catch(Exception e) { e.printStackTrace(); } Document doc = new Document();

if(!bodyText.equals("") && bodyText != null) { doc.add(new Field("content",bodyText,Field.Store.NO,Field.Index.ANALYZED)); } return doc; } [/java]

Thus again we can add the returned Document instance to the IndexWriter’s _writer_ method.

   [1]: http://xebee.xebia.in/2010/05/24/jump-start-lucene-indexing-and-search/
   [2]: http://pdfbox.apache.org/
   [3]: http://lucene.apache.org/java/2_2_0/api/org/apache/lucene/document/Field.html
   [4]: http://lucene.apache.org/java/2_2_0/api/org/apache/lucene/document/Document.html
   [5]: http://www.saxproject.org/
   [6]: http://poi.apache.org/
   [7]: http://poi.apache.org/hwpf/index.html

## Comments

**[Elnourso](#3044 "2010-10-21 16:21:08"):** Hello, This can also be done with Apache Tika :o) http://tika.apache.org/index.html Cheers,

**[Dushi](#5549 "2011-05-04 15:58:46"):** Hi, Do you know how Open Office documents can be indexed via Lucene? (Without using Apache Tika) Cheers,

**[Tarun Sapra](#5551 "2011-05-05 08:54:24"):** Hello Dushi, You can try to save the open office doc as MS word formats and then use the above technique. Cheers,

