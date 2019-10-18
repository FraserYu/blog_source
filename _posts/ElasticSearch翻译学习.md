---
title: ElasticSearch翻译学习
tags:
  - ElasticSearch
categories:
  - ELK
abbrlink: b68d43b9
date: 2018-07-19 10:43:38
---

# Getting Started

Elasticsearch is a highly scalable open-source full-text search and analytics engine. It allows you to store, search, and analyze big volumes of data quickly and in near real time. It is generally used as the underlying engine/technology that powers applications that have complex search features and requirements.

> Elasticsearch是一个高度可扩展的，开源的全文本搜索和分析引擎，通过它你可以快速并接近实时的存储，搜索，和分析大数量级的数据，它通常被作为基本的引擎或技术，来强化有复杂搜索需求的应用

Here are a few sample use-cases that Elasticsearch could be used for:
> 这里列出几个 Elasticsearch 的使用样例：

- You run an online web store where you allow your customers to search for products that you sell. In this case, you can use Elasticsearch to store your entire product catalog and inventory and provide search and autocomplete suggestions for them.

    + > 你运行一个在线商店，该商店允许你的顾客来索索你所卖的商品。在这种情况下，你可以使用 Elasticsearch 来存储你的全部产品目录和库存，并且为顾客提供搜索和自动补全建议

- You want to collect log or transaction data and you want to analyze and mine this data to look for trends, statistics, summarizations, or anomalies. In this case, you can use Logstash (part of the Elasticsearch/Logstash/Kibana stack) to collect, aggregate, and parse your data, and then have Logstash feed this data into Elasticsearch. Once the data is in Elasticsearch, you can run searches and aggregations to mine any information that is of interest to you.

    + > 你想收集日志或者交易数据，并且你想分析和挖掘这些数据来寻求趋势，统计，概要或者异常现象。在这种情况下，你可以使用 Logstash （ElasticSearch/Logstash/kibana 栈的一部分）来收集，聚合和解析你的数据，并且然后使用 Logstash 将数据装入到 Elasticsearch.  一旦数据进入 Elasticsearch, 你可以运行搜索和聚合来挖掘任何你感兴趣的信息。

- You run a price alerting platform which allows price-savvy customers to specify a rule like "I am interested in buying a specific electronic gadget and I want to be notified if the price of gadget falls below $X from any vendor within the next month". In this case you can scrape vendor prices, push them into Elasticsearch and use its reverse-search (Percolator) capability to match price movements against customer queries and eventually push the alerts out to the customer once matches are found.

    + > 你可以运行一个价格提醒平台，该平台允许价格内行的顾客来制定一个类似“我想买某个电子元件，并且接到通知，如果在下个月，任意供应商提供的元件的价格低于$X”的规则。在这种情况下，你可以抓取供应商的价格数据，将这些数据推送到 Elasticsearch 并且使用它的反向查询（Percolator）能力来匹配价格变动，一旦匹配成功则发送通知给顾客。

- You have analytics/business-intelligence needs and want to quickly investigate, analyze, visualize, and ask ad-hoc questions on a lot of data (think millions or billions of records). In this case, you can use Elasticsearch to store your data and then use Kibana (part of the Elasticsearch/Logstash/Kibana stack) to build custom dashboards that can visualize aspects of your data that are important to you. Additionally, you can use the Elasticsearch aggregations functionality to perform complex business intelligence queries against your data.

    + > 你有分析和商业情报需求，并且想在许多数据上（想象数百万或数十亿条记录）快速调查，分析，可视化和询问专业的问题，在这种情况下，你可以使用 ElasticSearch 来存储你的数据，然后使用 kibana（ElasticSearch/Logstash/kibana 栈的一部分）构建自定义仪表盘来可视化对你来说很重要的数据的方方面面，另外，你可以使用 ElasticSearch 聚合功能来演示复杂的商业情报查询。

For the rest of this tutorial, you will be guided through the process of getting Elasticsearch up and running, taking a peek inside it, and performing basic operations like indexing, searching, and modifying your data. At the end of this tutorial, you should have a good idea of what Elasticsearch is, how it works, and hopefully be inspired to see how you can use it to either build sophisticated search applications or to mine intelligence from your data.

> 对于接下来的学习指导，你将会通过 ElasticSearch 的启动和运行过程来获取指导，查看它的一角和演示基本的操作，如索引，查询，和修改数据，在学习指导的末尾，你应该有一个 ElasticSearch 是什么，它怎样工作的的概念，并且非常想看到你是如何使用它来构建复杂的查询应用或者从你的数据中挖掘情报。

   [Basic Concepts  »](https://www.elastic.co/guide/en/elasticsearch/reference/current/_basic_concepts.html)


## Basic Concepts

There are a few concepts that are core to Elasticsearch. Understanding these concepts from the outset will tremendously help ease the learning process.



<!-- more -->

### Near Realtime (NRT)[edit](https://github.com/elastic/elasticsearch/edit/6.3/docs/reference/getting-started.asciidoc)

Elasticsearch is a near real time search platform. What this means is there is a slight latency (normally one second) from the time you index a document until the time it becomes searchable.

### Cluster[edit](https://github.com/elastic/elasticsearch/edit/6.3/docs/reference/getting-started.asciidoc)

A cluster is a collection of one or more nodes (servers) that together holds your entire data and provides federated indexing and search capabilities across all nodes. A cluster is identified by a unique name which by default is "elasticsearch". This name is important because a node can only be part of a cluster if the node is set up to join the cluster by its name.

Make sure that you don’t reuse the same cluster names in different environments, otherwise you might end up with nodes joining the wrong cluster. For instance you could use `logging-dev`, `logging-stage`, and `logging-prod` for the development, staging, and production clusters.

Note that it is valid and perfectly fine to have a cluster with only a single node in it. Furthermore, you may also have multiple independent clusters each with its own unique cluster name.

### Node[edit](https://github.com/elastic/elasticsearch/edit/6.3/docs/reference/getting-started.asciidoc)

A node is a single server that is part of your cluster, stores your data, and participates in the cluster’s indexing and search capabilities. Just like a cluster, a node is identified by a name which by default is a random Universally Unique IDentifier (UUID) that is assigned to the node at startup. You can define any node name you want if you do not want the default. This name is important for administration purposes where you want to identify which servers in your network correspond to which nodes in your Elasticsearch cluster.

A node can be configured to join a specific cluster by the cluster name. By default, each node is set up to join a cluster named `elasticsearch`which means that if you start up a number of nodes on your network and—assuming they can discover each other—they will all automatically form and join a single cluster named `elasticsearch`.

In a single cluster, you can have as many nodes as you want. Furthermore, if there are no other Elasticsearch nodes currently running on your network, starting a single node will by default form a new single-node cluster named `elasticsearch`.

### Index[edit](https://github.com/elastic/elasticsearch/edit/6.3/docs/reference/getting-started.asciidoc)

An index is a collection of documents that have somewhat similar characteristics. For example, you can have an index for customer data, another index for a product catalog, and yet another index for order data. An index is identified by a name (that must be all lowercase) and this name is used to refer to the index when performing indexing, search, update, and delete operations against the documents in it.

In a single cluster, you can define as many indexes as you want.

### Type[edit](https://github.com/elastic/elasticsearch/edit/6.3/docs/reference/getting-started.asciidoc)

![Warning](https://www.elastic.co/guide/en/elasticsearch/reference/current/images/icons/warning.png)

### Deprecated in 6.0.0.

See [*Removal of mapping types*](https://www.elastic.co/guide/en/elasticsearch/reference/current/removal-of-types.html)

A type used to be a logical category/partition of your index to allow you to store different types of documents in the same index, eg one type for users, another type for blog posts. It is no longer possible to create multiple types in an index, and the whole concept of types will be removed in a later version. See [*Removal of mapping types*](https://www.elastic.co/guide/en/elasticsearch/reference/current/removal-of-types.html) for more.

### Document[edit](https://github.com/elastic/elasticsearch/edit/6.3/docs/reference/getting-started.asciidoc)

A document is a basic unit of information that can be indexed. For example, you can have a document for a single customer, another document for a single product, and yet another for a single order. This document is expressed in [JSON](http://json.org/) (JavaScript Object Notation) which is a ubiquitous internet data interchange format.

Within an index/type, you can store as many documents as you want. Note that although a document physically resides in an index, a document actually must be indexed/assigned to a type inside an index.

### Shards & Replicas[edit](https://github.com/elastic/elasticsearch/edit/6.3/docs/reference/getting-started.asciidoc)

An index can potentially store a large amount of data that can exceed the hardware limits of a single node. For example, a single index of a billion documents taking up 1TB of disk space may not fit on the disk of a single node or may be too slow to serve search requests from a single node alone.

To solve this problem, Elasticsearch provides the ability to subdivide your index into multiple pieces called shards. When you create an index, you can simply define the number of shards that you want. Each shard is in itself a fully-functional and independent "index" that can be hosted on any node in the cluster.

Sharding is important for two primary reasons:

- It allows you to horizontally split/scale your content volume
- It allows you to distribute and parallelize operations across shards (potentially on multiple nodes) thus increasing performance/throughput

The mechanics of how a shard is distributed and also how its documents are aggregated back into search requests are completely managed by Elasticsearch and is transparent to you as the user.

In a network/cloud environment where failures can be expected anytime, it is very useful and highly recommended to have a failover mechanism in case a shard/node somehow goes offline or disappears for whatever reason. To this end, Elasticsearch allows you to make one or more copies of your index’s shards into what are called replica shards, or replicas for short.

Replication is important for two primary reasons:

- It provides high availability in case a shard/node fails. For this reason, it is important to note that a replica shard is never allocated on the same node as the original/primary shard that it was copied from.
- It allows you to scale out your search volume/throughput since searches can be executed on all replicas in parallel.

To summarize, each index can be split into multiple shards. An index can also be replicated zero (meaning no replicas) or more times. Once replicated, each index will have primary shards (the original shards that were replicated from) and replica shards (the copies of the primary shards). The number of shards and replicas can be defined per index at the time the index is created. After the index is created, you may change the number of replicas dynamically anytime but you cannot change the number of shards after-the-fact.

By default, each index in Elasticsearch is allocated 5 primary shards and 1 replica which means that if you have at least two nodes in your cluster, your index will have 5 primary shards and another 5 replica shards (1 complete replica) for a total of 10 shards per index.

![Note](https://www.elastic.co/guide/en/elasticsearch/reference/current/images/icons/note.png)

Each Elasticsearch shard is a Lucene index. There is a maximum number of documents you can have in a single Lucene index. As of [`LUCENE-5843`](https://issues.apache.org/jira/browse/LUCENE-5843), the limit is `2,147,483,519` (= Integer.MAX_VALUE - 128) documents. You can monitor shard sizes using the [`_cat/shards`](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/cat-shards.html) API.

With that out of the way, let’s get started with the fun part…
