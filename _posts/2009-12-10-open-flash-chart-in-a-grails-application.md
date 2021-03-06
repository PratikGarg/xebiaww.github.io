---
layout: post
header-img: img/default-blog-pic.jpg
author: sunil.inteti
description: 
post_id: 2800
created: 2009/12/10 12:42:56
created_gmt: 2009/12/10 07:42:56
comment_status: open
---

# Open Flash Chart in a Grails Application

In my Grails project we had a requirement that we needed to show graphs. Our client wanted to go for a opensource solution. We decided on the option [OpenFlashChart][1]. Grails comes up with its [plugin for OpenFlashChart][2]. With Open Flash Chart we can easily show barchart, line chart, piechart, Area charts etc. Let me explain very briefly how Open Flash Chart works. Basically in a web page we need to add this open flash chart which is the swf object. We should provide swf object with a data-file which contains the data it displays. This data could come from backend as well. When the web page is rendered the swf object tries to get the data and render the graph. Lets look at how we can use this in a Grails application.

For example purpose **I choose to show a barchart here which shows goals scored by Striker in a EPL season by name**. We need to add the chart to our view page. Taglibrary of grails is of a great help here especially if we are using the chart at various places in our application.So first let us write a Taglibrary **OFCTaglibrary.groovy** and add a closure chart. Ofcourse this is put under the Taglibrary folder for grails for convention purposes.

[sourcecode language="java"] public class OFCTaglibrary { static namespace = "grailsOfc" def chart = { attrs -> if (!attrs.name) throwTagError("Tag [chart] is missing required attribute [name]") String name = attrs.remove('name') String width = attrs.width ? attrs.remove("width") : "350" String height = attrs.height ? attrs.remove("height") : "200" String url = attrs.url? attrs.remove("url") : "" String flashFileName = "open-flash-chart.swf" out << "<div id='$name' name='$name'></div>\n" out << """ <script type="text/javascript"> var params = {}; params.wmode = "transparent"; swfobject.embedSWF("${request.contextPath}/${flashFileName}", "$name", "$width","$height","9.0.0", "expressInstall.swf", {"data-file":"$url"},params);</script> """ } } [/sourcecode]

Now we have created a tag library which we can use in view pages to insert the OFC chart . I will expain the attributes in the closure a little later.(rather i feel it is self explainatory ;) )

This is the piece of code that we should put in the view to show a OFC chart.

[sourcecode language="html"] <grailsOfc:chart name="barChart" url="${createLink(controller:'stats',action: 'bar_graph')}" width="${chartWidth}" height="174" /> [/sourcecode]

So when this page is rendered grails looks for the Taglibrary with namespace **grailsOfc**. In this Taglibrary it looks for the chart closure, and puts the code for the swf object in the view. The **open-flash-chart.swf ** looks for the data-file in the url

[sourcecode language="java"]${createLink(controller:'stats',action: 'bar_graph')}[/sourcecode]

**[createLink][3]** is a GrailsTag which we can use to call an action on Grails Controller. The idea here is, we can put the logic to get the data from the server. We shall now look at the StatsController code.

[sourcecode language="java"] def bar_graph = { def players = Player.findAll() //could be a more complex logic. This is just to get a filtered list of players

def x_axis_labels = [] List values = [] players.each { x_axis_labels.add( it.playerName) Map bar = [:] bar.put("top", it.goals()) bar.put("colour", "#0080F7") values.add(bar) }

String backgroundColour = "#FFFFFF" List elements = [];

def barChart = [ type: "bar", values: values, tip: "#val# Goals", colour: "#E3E2DD", alpha:1 ] elements.add(barChart);

// x Axis Map xAxis = ['tick-height': 0, colour: "#74665D", stroke: 1, 'grid-colour': "#FFFFFF", labels: [labels: x_axis_labels, visible:true]]

//y Axis Map yAxis = [ 'tick-height': 0, colour: "#74665D", stroke: 1,'grid-colour': "#E0DDD8", min: 0, max: 50, steps: 15, labels:[text: "#val#"]]

//assemble the chart HashMap goalChart = [:]

if (values.size() == 0) { goalChart['title'] = [text:"noDataExists"] }

goalChart = [ x_axis:xAxis, y_axis:yAxis, elements:elements, bg_colour:"#FFFFFF" ]

render goalChart as JSON; //grails provides converters to convert map in to JSON format.

} [/sourcecode]

To summarize here we are building x-axis, y-axis and its labels, adding them to the chart which can be anything (bar or pie or any) in a map and convert this to JSON format.

**The graph would look like this :** ![bar graph][4] **Problem :** By this way we can show the Bar graph easily. How ever I faced a problem here.I couldn't get a scroll on x-axis inside the chart.The chart width was increasing. I couldn't find an option for the scroll for OFC chart. So I did a workaround. I put this OFC chart inside a <div> and and in the div i gave the style properties as : overflow-x:scroll; overflow-y:hidden; width : 550px; This put the chart inside the div with a scroll if the chart widht is increased to more than 550px. I dont know if this option is there in OFC by now. But i used this workaround and client was OK with that. :)

**Conclusion :** Finally i feel if your client is not ready to pay for the charts, OFC is a great option. With this we can achieve all sorts of graphs.For more examples you can visit the [OFC site][5]

   [1]: http://teethgrinder.co.uk/open-flash-chart/
   [2]: http://www.grails.org/Open+Flash+Chart+Plugin
   [3]: http://www.grails.org/Tag+-+createLink
   [4]: http://xebee.xebia.in/wp-content/uploads/2009/12/graph.jpg
   [5]: http://teethgrinder.co.uk/open-flash-chart-2/

## Comments

**[RAAM](#5405 "2011-04-06 15:07:19"):** could u provide a sample example for line chart. that sample example shoild have labels in y axis and values like 0 to 100 should be in x axis.It will be very thankful if you provide the same asap.

