---
layout: post
header-img: img/default-blog-pic.jpg
author: nkhattar
description: 
post_id: 7981
created: 2011/03/24 08:56:55
created_gmt: 2011/03/24 03:56:55
comment_status: open
---

# Flex: Composite Charts with Multiple Series & Axes

Recently in my project, I came across a requirement to introduce Flex Charting components in the application as a means of **Data Visualization**. The application deals with Business Analysis of tons of raw data in tabular format with various categories & sub-categories, alphabetical & numeric data. That’s why for an end-user Data Visualization is a must feature.

Flex provides a wide range of charting controls with varied types & configurable properties. Flex supports the most common types of two-dimensional charts (such as bar, column, and pie charts) and gives you a great deal of control over the appearance of charts.

Creating Simple Charts turned out to be an easy task with the help of **[Adobe Help Reference][1]** and **[Flex Blog examples][2]**, but creating composite charts was not that easy as it appeared to me. So in this blog, I will elaborate on how to create composite charts and will showcase few examples on creating composite charts using multiple chart series and multiple axes.

Creating **Composite chart** accounts for an important consideration that one should keep in mind: 

  * Configuring specific chart type with its specific properties & behavior and then clubbing each of them in a composite way such that it suits for each chart type or series in a perfect manner.
Ok, let’s start exploring this concept with few examples:  **Example 1 - Composite Chart (Column & Line) with multiple column series**

Let’s consider a problem statement:

As a sales manager, I want to visualize the Data for the Sales (Bottle & Canned) of few types of Soft Drink (Coke, Pepsi & Sprite) in a City (Delhi) in the form of a Column Chart and a separate Line Chart depicting the Total Sales.

From the above mentioned statement we could easily make out some basic facts that ‘product’ as soft drink  will serve as the **horizontal axis**, ‘sales’ will serve as the **vertical axis**. Chart will consist of two **column series** (one for ‘bottled sales’ & another for ‘canned sales’) and one** line series** for ‘total sales’.

The chart may appear like this: 

![][3]

Ok, I think the problem is quite straightforward and the basic fact is to make a **Stacked** Column Chart with two column series and one line Series. So, following is a common approach & unluckily a **common mistake** that we all may commit ..

[sourcecode language="java"] ... [Bindable] private var delhi:ArrayCollection = new ArrayCollection([ {productName:"Coke",bottledSales:200,cannedSales:500,total:700}, {productName:"Pepsi",bottledSales:400,cannedSales:700,total:1100}, {productName:"Sprite",bottledSales:300,cannedSales:600,total:900} ]); ... <mx:ColumnChart id="multipleAxisColumn" showDataTips="true" dataProvider="{delhi}" type="stacked"> <mx:horizontalAxis> <mx:CategoryAxis categoryField="productName" title="Product"/> </mx:horizontalAxis> <mx:verticalAxis> <mx:LinearAxis id="series1Axis" title="Sales"/> </mx:verticalAxis> <mx:series> <mx:ColumnSeries yField="bottledSales" displayName="Bottled Sales" /> <mx:ColumnSeries yField="cannedSales" displayName="Canned Sales" /> <mx:LineSeries yField="total" displayName="Total Sales"/> </mx:series> </mx:ColumnChart> [/sourcecode]

Oops….an exception occurs when we try to run the application

ReferenceError: Error #1056: Cannot create property offset on mx.charts.series.LineSeries. mx.charts::ColumnChart/applySeriesSet() [E:\dev\hero_private\frameworks\projects\datavisualization\src\mx\charts\ColumnChart.as:530] mx.charts.chartClasses::CartesianChart/http://www.adobe.com/2006/flex/mx/internal::updateSeries() [E:\dev\hero_private\frameworks\projects\datavisualization\src\mx\charts\chartClasses\CartesianChart.as:1042]…

As you can see when we try to run the above code, it gives a runtime error implying that the Chart Type ‘Stacked’ cannot be applied to Line Series. So, now the question is how to apply the stacked type to the column series separately, since there is **no property named ‘Type’ in a Column Series**??

Answer is to exploit the concept of **ColumnSet**. This is what Flex Charting API has to say about Column Set –

“_**ColumnSet **__is a grouping set that can be used to stack or cluster column series in any arbitrary chart. A ColumnSet encapsulates the same grouping behavior used in a ColumnChart, but can be used to assemble custom charts based on CartesianChart. ColumnSets can be used to cluster any chart element type that implements the **IColumn interface**. It can stack any chart element type that implements the IColumn and IStackable interfaces. Since **ColumnSet itself implements the IColumn interface**, you can use ColumnSets to cluster other ColumnSets to build more advanced custom charts.”_

Summarizing the above statement, we can group the two column series in a column set and in this way we can apply same properties that a Column Chart persists like Chart Type which in our case is ‘stacked’. Similarly in Bar Charts, there is a concept of **BarSet**. 

![Exhibiting Column Set][4]

[sourcecode language="java"] <mx:series> <mx:ColumnSet type="stacked"> <mx:ColumnSeries yField="bottledSales" displayName="Bottled Sales" /> <mx:ColumnSeries yField="cannedSales" displayName="Canned Sales" /> </mx:ColumnSet> <mx:LineSeries yField="total" displayName="Total Sales"/> </mx:series> [/sourcecode]

The only change observed in the code is that now the two column series are wrapped into a column set and the **type = “stacked”** property is removed from the level of Column Chart & is added to the Column Set, instead.

Now if we run our modified code, it runs successfully showing the desired results.

[kml_flashembed publishmethod="dynamic" fversion="10.1.0" useexpressinstall="true" movie="http://xebee.xebia.in/wp-content/uploads/2011/03/SingleColumnSet.swf" width="550" height="400" targetclass="flashmovie"]

![Get Adobe Flash player][5]

[/kml_flashembed]

[Download Source Code][6]

**Example 2 – Composite Chart (Column & Line) with multiple axes**

** **

Using the same problem as stated above, idea is to create the above mentioned composite chart with the Line Series now representing a separate & distinct measure named ‘**No. of Sales Persons Involved**’ with a **separate vertical axis** for the same.

   [1]: http://help.adobe.com/en_US/flex/using/WS2db454920e96a9e51e63e3d11c0bf69084-7bdf.html
   [2]: http://blog.flexexamples.com/category/charting/
   [3]: http://xebee.xebia.in/wp-content/uploads/2011/03/SingleColumnSet.jpg (Single ColumnSet)
   [4]: http://xebee.xebia.in/wp-content/uploads/2011/03/ColumnSet-300x253.jpg (ColumnSet)
   [5]: http://www.adobe.com/images/shared/download_buttons/get_flash_player.gif
   [6]: http://xebee.xebia.in/wp-content/uploads/2011/03/SingleColumnSet.zip

## Comments

**[Nitin Khattar](#5605 "2011-06-01 12:31:35"):** @Mariya: Sorry for the late reply. I may not be right person to answer this as i have done charting in Flex not in Android. I'll ask my fellow colleague Yogesh to touch base with you as he has a clear idea on this.

**[Roopesh](#5604 "2011-06-01 11:28:53"):** Hey this is exactly what I am trying to do (composite chart with different axes) - only difference is there are four column series stacked with a separate line series. Now here's the wierd thing - when I load the entire thing with the data, it just doesn't load some of the data in the column series (for some x values, one or two series data is missing). What's more, If I refresh the page (the flex component reloads entirely in the same browser, it starts working properly. (Extremely wierd because right now the data is hardcoded as an object array - there is no dependency on any webservice or anything that can change!) Have you ever faced this kind of issue before?

**[Mariya](#5600 "2011-05-27 18:26:50"):** How to generate more than one chart in android... I am using afreechart jar file to generate the charts.... I want to display the charts one by one... I want to see that charts one by one using the scroll bar.... please guide me....

**[Nitin Khattar](#5608 "2011-06-02 08:11:28"):** @Roopesh: Haven't faced such an issue. If possible could you share the data as I tried out with four column series but couldn't find such a problem.

**[Mike J](#6052 "2011-10-25 02:22:02"):** this probably works on Flash Builder 4 or 4.5 but in Flex Builder 3.2 it does not. The code does not stack the bars you only see 1 bar and the line. There is a bug associated with this and they say to use the CartesianChart type.

**[vengatesh](#9272 "2012-07-30 11:36:50"):** great coding logic.

**[Nitin Khattar](#9273 "2012-07-30 11:46:04"):** Thanks Vengatesh

**[Sam](#7522 "2012-02-10 14:27:41"):** Thanks a lot for your blog. I resolved my issue with the help of your blog.

**[Nitin Khattar](#7523 "2012-02-10 14:38:11"):** Thanks Sam.

