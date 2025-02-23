---
title: "Configure AWS Cloud 9 IDE"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.1 </b> "
---

For this workshop, we will be using [Cloud9](https://aws.amazon.com/vi/cloud9/) , a cloud-based integrated development
environment (IDE) to read, edit and
run our code for a subset of these workshops.

In order to use Cloud9, we need to complete the following prerequisite steps:

* In your AWS management console, navigate to the Cloud9 service page by typing in Cloud9 in the search bar at the top
  of the console.
* You will now see a Cloud9 Instance (already created for you) called KinesisRealTimeStreaming-***, with the *** being
  your stack name.
* On this page, click the button which reads Open IDE to open your cloud9 instance for this workshop.
  The IDE can take a few seconds of loading to open--and once it does, you will be presented with the following screen:

  ![Image](/repo_pmt_ws-fcj-005/images/2/1/21-001.png?featherlight=false&width=90pc)

We will now proceed to configure Cloud9 for the rest of our exercises:



{{% notice info %}}
Java should already be installed, and so long as it is > Java 1.8, the exercises following will work! You can verify
this by typing java -version in the terminal.
{{% /notice %}}

### Install Maven

Type the following in a new terminal to install Maven on your Cloud9 IDE:

`sudo yum install maven -y`

Once maven has been installed, let's make sure we are pointed to the right java version.

### Point to Java 11

In your terminal, type

```
sudo update-alternatives --config java
sudo update-alternatives --config javac
```
Ensure or reassign the java version(s) to `/usr/lib/jvm/java-11-amazon-corretto.x86_64/bin/java.`

You may be prompted to enter this number twice.
You now may proceed with the next step in the workshop!