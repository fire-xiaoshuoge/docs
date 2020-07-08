## Apache Solr

### Solr

Solr 是一个基于 Apache Lucene 之上的搜索服务器，它是一个开源的、基于 Java 的信息检索库。它旨在驱动功能强大的文档检索应用程序 - 无论您需要根据用户的查询将数据服务到何处，Solr 都可以为您服务。

![Solr与应用程序的集成](https://atts.w3cschool.cn/attachments/image/20171103/1509691910631328.png)

Solr 可以通过以下步骤轻松地添加在在线商店搜索的功能：

1. 定义一个模式。该模式告诉 Solr 关于它将被索引的文档的内容。在在线商店的示例中，模式将定义产品名称、描述、价格、制造商等定义的字段。Solr 的模式是强大而灵活的，可以让您根据自己的应用程序定制 Solr 的行为。有关详细信息，请参阅文档、字段和模式设计。
2. 您的用户将搜索的 Feed Solr 文档。
3. 在您的应用程序中公开搜索功能。

### Solr 配置文件

在 Solr 的主页中，你会发现这些文件：

- solr.xml：为您的 Solr 服务器实例指定配置选项。有关 solr.xml 的更多信息，请参阅：Solr Cores 和 solr.xml。
- 每个 Solr 核心：
  - core.properties：为每个核心定义特定的属性，例如其名称、核心所属的集合、模式的位置以及其他参数。有关 core.properties 的更多详细信息，请参阅定义 core.properties 一节。
  - solrconfig.xml：控制高级行为。例如，您可以为数据目录指定一个备用位置。有关 solrconfig.xml 的更多信息，请参阅 配置 solrconfig.xml。
  - managed-schema（或用 schema.xml 替代）描述您将要求 Solr 索引的文档。模式将文档定义为字段集合。您可以同时定义字段类型和字段本身。字段类型定义功能强大，包含有关 Solr 如何处理传入字段值和查询值的信息。有关 Solr 架构的更多信息，请参阅文档、字段和模式设计以及模式 API。
  - data/：包含低级索引文件的目录。