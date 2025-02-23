---
title: "Clean, Aggregate, and Enrich Events with Amazon Managed Service for Apache Flink"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 4.  </b> "
---

In this section, you will learn how to connect Amazon Managed Service for Apache Flink Studio to your existing stream
and clean, aggregate, and enrich the incoming events.

![Image](/repo_pmt_ws-fcj-005/images/4/4-001.png?featherlight=false&width=90pc)

We use a scenario to analyze the telemetry data of a taxi fleet in New York City in near-real time to optimize the fleet
operation. In this scenario, every taxi in the fleet is capturing information about completed trips. The tracked
information includes the pickup and drop-off locations, number of passengers. This information is ingested into a
Kinesis data stream as a simple JSON blob. From there, the data is processed by a Flink application, which is deployed
to Kinesis Data Analytics for SQL in Managed Service for Apache Flink Studio. This application identifies areas that are
currently requesting a high number of taxi rides. The derived insights are finally persisted in Amazon OpenSearch, where
they can be accessed and visualized using [OpenSearch](https://aws.amazon.com/vi/what-is/elk-stack/) Dashboard.

![Image](/repo_pmt_ws-fcj-005/images/4/4-002.png?featherlight=false&width=90pc)