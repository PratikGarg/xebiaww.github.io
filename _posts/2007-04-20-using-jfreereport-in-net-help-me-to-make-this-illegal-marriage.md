---
layout: post
header-img: img/default-blog-pic.jpg
author: balajidl
description: 
post_id: 205
created: 2007/04/20 14:51:45
created_gmt: 2007/04/20 12:51:45
comment_status: open
---

# Using JFreeChart in .NET - Help me to make this illegal marriage

I am trying to use Jfreechart in .NET and I need someone to help me to get ride of issue. If you have some free time and if you are interested in doing weird things :) like this, please drop a comment so that we can try to get this working.

Yes I am not kidding.

Previously I have used Apache FOP in ASP.NET(vb.net) to generate PDF on the fly, i did that since there was no free and easy to use pdf content generator available for .NET. If you want to know how to use Apache FOP in .NET, then please click the link [Calling Apache FOP from .NET][1]. If you want to see the pdf that was generated out from .NET using fop, please click this [link][2].

You can use java standalone packages like Xalan, Apache FOP, Servlet, etc., in .NET using [IKVM][3], IKVM is a .NET implementation of Java.

IKVM can convert the Java jar files to .NET dll files, which you can import as a reference in your .NET project and start using them like how you use Java classes.

For instance, The Apache FOP jars after being converted in to .ddl can be used like .. [vbnet] Try AppDomain.CurrentDomain.Load("xercesImpl-2.2.1") AppDomain.CurrentDomain.Load("batik") java.lang.System.setProperty("javax.xml.parsers.SAXParserFactory", "org.apache.xerces.jaxp.SAXParserFactoryImpl") Dim filename As String = generateXSLFO() Dim isrc As org.xml.sax.InputSource = New org.xml.sax.InputSource(filename & ".fo") Dim fs As java.io.FileOutputStream = New java.io.FileOutputStream(filename & ".pdf") Dim driver As Driver = New Driver(isrc, fs) 'driver.setLogger(cl) driver.setRenderer(driver.RENDER_PDF) driver.run() fs.close() Label1.Text = "done" System.IO.File.Delete(filename & ".xml") System.IO.File.Delete(filename & ".fo") Catch ex As Exception Label1.Text = ex.ToString End Try [/vbnet]

The first step to use Jfreechart in .NET is to convert them to .dll's. For this, please download and install the ikvm on your PC. Also download the jfreechart lib's on your pc.

Go to bin directory of ikvm installation and run the following commands.

(For the command to work, i assume that the files like jfreechart-1.0.5.jar, servlet.jar and jcommon-1.0.9.jar were in the bin directory) .

[as]ikvmc -target:library -reference:/usr/lib/IKVM.GNU.Classpath.dll servlet.jar ikvmc -target:library -reference:/usr/lib/IKVM.GNU.Classpath.dll -reference:servlet.dll jcommon-1.0.9.jar ikvmc -target:library -reference:/usr/lib/IKVM.GNU.Classpath.dll -reference:servlet.dll reference:jcommon-1.0.9.dll jfreechart-1.0.5.jar[/as]

This will generate .dll files of jfreechart, jcommon and servlet.

Now in your VS.NET IDE, Start a new asp.net web application project with C# as backend code. Add the newly generated dll as references to the project. Also add the dll files **IKVM.AWT.WinForms.dll IKVM.GNU.Classpath.dll IKVM.Runtime.dll** as reference to your project. If you want to see the screenshot of my IDE(VS.NET 2003) with references added, please [click here][4].

Once you are done with the above tasks, start writing the C# code to call the Jfreechart from .NET.

Here is the code snippet that will be called on button click event of a web page. [csharp] using ikvm.lang; using org.jfree.chart; using javax.servlet.http; using org.jfree.data.category; using ikvm.awt; using ikvm.@internal;
    
    
        private void Button1_Click(object sender, System.EventArgs e)
        {
            try
            {
                AppDomain.CurrentDomain.Load("jcommon-1.0.9");
                org.jfree.data.general.DefaultPieDataset myDataSet=new org.jfree.data.general.DefaultPieDataset();
                myDataSet.setValue("Accepted", 100);
                myDataSet.setValue("Rejected", 200);
                JFreeChart pieChart=ChartFactory.createPieChart("Total requests: 300", myDataSet,true,true,true);
                pieChart.setBackgroundPaint(java.awt.Color.white);
                pieChart.setBorderVisible(false);
                ChartRenderingInfo thisImageMapInfo=new ChartRenderingInfo();
                java.io.OutputStream jos = new java.io.FileOutputStream("C:/Inetpub/wwwroot/csharp_jfree/test.jpeg");
                ChartUtilities.writeChartAsJPEG(jos,100,pieChart,240,230,thisImageMapInfo);
    
    
                Label1.Text = "ok";
            }
            catch(Exception exp)
            {
                Label1.Text = exp.ToString();
            }
        }
    

[/csharp]

Once you compile and deploy the web application, please click the button that will execute the code to generate the JfreeChart. Executing the code will throw the below error saying.. 

> System.NotImplementedException: The method or operation is not implemented. at ikvm.awt.NetGraphicsEnvironment.createGraphics(BufferedImage bi) at java.awt.image.BufferedImage.createGraphics() at org.jfree.chart.JFreeChart.createBufferedImage(Int32 width, Int32 height, Int32 imageType, ChartRenderingInfo info) at org.jfree.chart.JFreeChart.createBufferedImage(Int32 width, Int32 height, ChartRenderingInfo info) at org.jfree.chart.ChartUtilities.writeChartAsJPEG(OutputStream out, Single quality, JFreeChart chart, Int32 width, Int32 height, ChartRenderingInfo info) at csjfree.WebForm1.Button1_Click(Object sender, EventArgs e) in c:\inetpub\wwwroot\csjfree\webform1.aspx.cs:line 72

The problem is IKVM.AWT is not fully implemented with Java AWT features. The author of IKVM acclaims this in his website as well.

Now i am wondering how to get ride of this error and make use of jfreechart in asp.net. I was thinking like 

  * replacing ikvm.awt dll with another equivalent java Awt dll. 
  * Make use of .NET MFC image rendering and give it to Jfree chart (don't know how to do it)

So again, If you have some free time and if you are interested in doing weird things :) like this, please drop a comment so that we can try to get this working.

   [1]: http://radio.javaranch.com/balajidl/2006/01/12/1137047604301.html
   [2]: http://xebee.xebia.in/wp-content/uploads/2007/04/fop-vb-net.pdf
   [3]: http://www.ikvm.net/
   [4]: http://xebee.xebia.in/wp-content/uploads/2007/04/csjfree.gif