---

copyright:
  years: 2016, 2017
lastupdated: "2017-02-07"

---

<!-- Common attributes used in the template are defined as follows: -->
{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen:.screen}
{:codeblock:.codeblock}

<!-- Additional task topic: OPTIONAL
This is the template for additional task topics that are needed beyond the basic tasks in the getting started index.md.  As needed, other task topics can be included, with titles such as "Configuring x", "Administering y", "Managing z", etc. This topic is a peer of the getting started index.md in the <servicename>.ditamap. This topic can have one level of children and they also can be referenced in <servicename>.ditamap -->

# 在 Kibana 中通过查询创建表和图形
<!-- for example, Uploading your data -->
{: #logging_kibana_tables_graphs}
<!-- Provide an appropriate ID above -->

<!-- The short description section should include a sentence describing why this task is needed. For search engine optimization, include the service long name and "Bluemix". For example: -->

使用 Kibana 可为查询创建图形和表，以可视化日志数据并比较结果。可以从 Cloud Foundry 应用程序的**日志**选项卡访问 Kibana 仪表板。
{:shortdesc}

<!-- Include a sentence to briefly introduce the steps/subtopics. Example: -->
Kibana 仪表板的布局是一系列行，其中每行包含一个或多个面板。可以配置这些面板以显示数据的图形表示法。使用查询可确定要显示哪些数据。要创建图形或表，必须首先创建一个空白行；然后，创建面板。如果从 CF 应用程序上的**日志**选项卡访问 Kibana 仪表板，那么此仪表板会自动显示两个面板：直方图和表。


要在 Kibana 仪表板上添加图形或表，请完成以下任务：

1. 要访问 Cloud Foundry 应用程序的**日志**选项卡，请单击 {{site.data.keyword.Bluemix_notm}} **应用程序**仪表板上 **Cloud Foundry 应用程序**表中的应用程序名称；然后，单击**日志**选项卡。这将显示应用程序的日志。

2. 要访问应用程序的 Kibana 仪表板显示，请单击**高级视图** ![“高级视图”链接](images/logging_advanced_view.jpg)。这将显示 Kibana 仪表板。

3. 在 Kibana 仪表板上，滚动到仪表板底部并单击**添加行** ![“添加行”图标](images/logging_add_row.jpg)，以为要添加的面板创建一行。这将显示“仪表板设置”窗格。 
	
	![“仪表板设置”窗格](images/logging_dashboard_settings.jpg)
	
	在“添加行”窗格的**标题**字段中，输入行的名称；然后，单击**创建行**。这将添加一个新行。可以通过单击行标题旁的**向上箭头**或**向下箭头**图标来调整行的顺序。设置行的顺序后，单击**保存**。这将在 Kibana 仪表板上创建一个空行。

4. 通过单击**向空行添加面板**来添加面板。这将显示“行设置”窗格。

    ![“行设置”窗格](images/logging_row_settings.jpg)
	
	可以从**选择面板类型**下拉列表中选择其他面板类型，例如**表**、**直方图**或**项**。选择**项**可基于查询创建条形图、饼图或表。一系列配置选项会显示在“行设置”窗格中。
	
	![在“行设置”窗格中添加面板](images/logging_add_panel.jpg)
	
	配置面板。输入图形显示的**标题**。从下拉列表中选择面板的**跨度**；**跨度**决定了面板在仪表板上的宽度。在“参数”部分中，删除**字段**的内容，并输入有效的日志字段；例如，`instance_id`。 

5. 在“视图选项”部分中，从**样式**下拉列表中选择**条形图**、**饼图**或**表**，以选择条形图、饼图或表。在“查询”部分中，从**查询**下拉列表中选择**所选项**，以使用来自仪表板查询的日志数据。最后，单击**保存**。新面板将显示在仪表板中。

	![显示包含条形图的面板的仪表板](images/logging_bar_chart_panel.jpg)
	
6. 要将此面板更改为显示表，请单击**配置**图标 ![“配置”图标](images/logging_dashboard_config_panel.jpg)。这将显示“项设置”窗格。 

	![“项设置”窗格](images/logging_terms_settings.jpg)
	
	单击**面板**选项卡；然后，从**样式**下拉列表中选择**表**。单击**保存**以更新面板并返回到仪表板。

7. 向仪表板添加更多行和面板。完成后，通过单击**保存**图标以保存对此仪表板的更改。

    **注：**如果尝试使用包含空格的名称来保存仪表板，那么不会保存该仪表板。请输入不包含空格的名称，然后单击**保存**图标。

    ![保存仪表板名称](images/logging_save_dashboard.jpg)。


