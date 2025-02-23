---
title: "Stream the data and validate output"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 4.2 </b> "
---

Navigate to the Amazon Kinesis Analytics services and select Studio tab. Select **KDA-Studio-1** notebook. Select **Run
**.
Choose **Open in Apache Zeppelin.**

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-001.png?featherlight=false&width=90pc)

Select Import note to import [KDA-OpenSearch.zpln](/repo_pmt_ws-fcj-005/resources/4/KDA-OpenSearch.zpln) notebook.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-002.png?featherlight=false&width=90pc)

Check **OpenSearchSecretsManagerReference** value in the resource section of CloudFormation stack and note down.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-003.png?featherlight=false&width=90pc)

Navigate to the Amazon Secret Manager and select the secret name you noted in previous step.
Select **Retrieve secret value**. Note down the **username** and **password** value.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-004.png?featherlight=false&width=90pc)

Navigate to the Amazon OpenSearch Service. Select os-domain. Note down the O**penSearch Dashboard URL** and **Domain
endpoint** value.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-005.png?featherlight=false&width=90pc)

Return to the Zeppelin KDA-OpenSearch notebook and scroll to step 3.
Replace hosts, **username** and **password** value with **Domain endpoint**, username and password value note in
previous step. Make sure hosts should have format **<Domain endpoint>:443**.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-006.png?featherlight=false&width=90pc)

Execute the first two cells in the notebook by clicking on the **Run Icon** on the right corner.

Start ingesting data into the Kinesis Data Stream
Tip : Check and/or start the Producer client program now. Go
to [Run producer client program](2-Produce-data-to-Kinesis-Data-Streams/)to learn how to do this.

Wait for few seconds and you will see the data populating in the notebook.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-007.png?featherlight=false&width=90pc)

Now let’s validate the output in OpenSearch

{{% notice note %}}
Ensure you've run paragraphs 3 and 4 in the notebook prior to continuing.
{{% /notice %}}
Open a browser and type **OpenSearch Dashboard URL** you noted in previous step. Provide **username** and **password**
you noted in previous step.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-008.png?featherlight=false&width=90pc)

In the left panel, select **Query Workbench**.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-009.png?featherlight=false&width=90pc)

Type “**select * from trip_statistics**” in Query editor and select **Run**. You can see the aggregated records in
result panel.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-010.png?featherlight=false&width=90pc)

Now lets build a dashboard. (You can also skip the below steps by importing the dashboard. See Import index pattern and
Dashboards section at the end)

Select Stack Management under Management in left panel.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-011.png?featherlight=false&width=90pc)

Select Create index pattern.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-012.png?featherlight=false&width=90pc)

Provide trip_statistics as **Index pattern name** and click on Next.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-013.png?featherlight=false&width=90pc)

Select **Create index pattern**.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-014.png?featherlight=false&width=90pc)

You see list of fields and associated data type.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-015.png?featherlight=false&width=90pc)

Select Visualize from left panel.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-016.png?featherlight=false&width=90pc)

Select Create visualization. Select a visualization type. For example, lets select Gauge.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-017.png?featherlight=false&width=90pc)

Select trip_statistics index.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-018.png?featherlight=false&width=90pc)

Create a visualization with below definition.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-019.png?featherlight=false&width=90pc)

Repeat the steps from step 21 and create another visualization by selection Data Tables visualization types.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-020.png?featherlight=false&width=90pc)

Import index pattern and Dashboards (Optional)
This is optional section. You can import the index and pattern and OpenSearch Dashboard If you want to skip OpenSearch
dashboard development.

Let's download

* [trip_statistics_index_pattern](/repo_pmt_ws-fcj-005/resources/4/trip_statistics_index_pattern.ndjson)

* [trip_stats_dasboard](/repo_pmt_ws-fcj-005/resources/4/trip_stats_dasboard.ndjson)

* [avg_trip_count_dashboard](/repo_pmt_ws-fcj-005/resources/4/avg_trip_count_dashboard.ndjson)

Select Stack Management in the left hand panel.

Select Saved object. Select import button. You can import trip_statistics_index_pattern.ndjson,
avg_trip_count_dashboard.ndjson and trip_stats_dasboard.ndjson in correct order.

![Image](/repo_pmt_ws-fcj-005/images/4/2/42-021.png?featherlight=false&width=90pc)

This will import these objects in your account.