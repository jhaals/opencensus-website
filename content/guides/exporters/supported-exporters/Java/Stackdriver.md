---
title: "Stackdriver (Stats and Tracing)"
date: 2018-07-21T14:27:35-07:00
draft: false
weight: 3
class: "resized-logo"
aliases: [/supported-exporters/java/stackdriver]
logo: /images/logo_gcp_vertical_rgb.png
---

- [Introduction](#introduction)
- [Creating the exporters](#creating-the-exporters)
    - [Import Packages](#import-packages)
    - [Instrument Metrics and Traces](#instrument-metrics-and-traces)
    - [All Exporters Combined](#all-exporters-combined)
- [Viewing your metrics](#viewing-your-metrics)
- [Viewing your traces](#viewing-your-traces)

## Introduction
Stackdriver Trace is a distributed tracing system that collects latency data from your applications and displays it in the Google Cloud Platform Console.

You can track how requests propagate through your application and receive detailed near real-time performance insights.
Stackdriver Trace automatically analyzes all of your application's traces to generate in-depth latency reports to surface performance degradations, and can capture traces from all of your VMs, containers, or Google App Engine projects.

Stackdriver Monitoring provides visibility into the performance, uptime, and overall health of cloud-powered applications.
Stackdriver collects metrics, events, and metadata from Google Cloud Platform, Amazon Web Services, hosted uptime probes, application instrumentation, and a variety of common application components including Cassandra, Nginx, Apache Web Server, Elasticsearch, and many others.

Stackdriver ingests that data and generates insights via dashboards, charts, and alerts. Stackdriver alerting helps you collaborate by integrating with Slack, PagerDuty, HipChat, Campfire, and more.

OpenCensus Java has support for this exporter available through packages:
* Stats [io.opencensus.exporter.stats.stackdriver](https://www.javadoc.io/doc/io.opencensus/opencensus-exporter-stats-stackdriver)
* Trace [io.opencensus.exporter.trace.stackdriver](https://www.javadoc.io/doc/io.opencensus/opencensus-exporter-trace-stackdriver)

{{% notice tip %}}
For assistance setting up Stackdriver, [Click here](/codelabs/stackdriver) for a guided codelab.
{{% /notice %}}

## Creating the exporters
To create the exporters, you'll need to:

* Have a GCP Project ID
* Have already enabled Stackdriver Tracing and Metrics, if not, please visit the [Code lab](/codelabs/stackdriver)
* Use Maven setup your pom.xml file
* Create the exporters in code

{{% notice warning %}}
Stackdriver's minimum stats reporting period must be >= 60 seconds. Find out why at this [official Stackdriver advisory](https://cloud.google.com/monitoring/custom-metrics/creating-metrics#writing-ts)
{{% /notice %}}

### Import Packages
Insert the following snippet in your `pom.xml`:
```xml
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <opencensus.version>0.15.0</opencensus.version> <!-- The OpenCensus version to use -->
    </properties>

    <dependencies>
        <dependency>
            <groupId>io.opencensus</groupId>
            <artifactId>opencensus-api</artifactId>
            <version>${opencensus.version}</version>
        </dependency>

        <dependency>
            <groupId>io.opencensus</groupId>
            <artifactId>opencensus-impl</artifactId>
            <version>${opencensus.version}</version>
        </dependency>

        <dependency>
            <groupId>io.opencensus</groupId>
            <artifactId>opencensus-exporter-stats-stackdriver</artifactId>
            <version>${opencensus.version}</version>
        </dependency>

        <dependency>
            <groupId>io.opencensus</groupId>
            <artifactId>opencensus-exporter-trace-stackdriver</artifactId>
            <version>${opencensus.version}</version>
        </dependency>
    </dependencies>
```
### Instrument Metrics and Traces
Instrument your code with the following snippets:

{{% notice tip %}}
To enable each of the respective APIs, please click on the respective tabs and then refer to [the final section](http://localhost:1313/guides/exporters/supported-exporters/java/stackdriver/#all-exporters-combined).
{{% /notice %}}

{{<tabs Tracing Metrics>}}
{{<highlight java>}}
import io.opencensus.exporter.trace.stackdriver.StackdriverTraceConfiguration;
import io.opencensus.exporter.trace.stackdriver.StackdriverTraceExporter;

StackdriverTraceExporter.createAndRegister(
    StackdriverTraceConfiguration.builder()
    .setProjectId(gcpProjectId)
    .build());
{{</highlight>}}

{{<highlight java>}}
import io.opencensus.exporter.stats.stackdriver.StackdriverStatsConfiguration;
import io.opencensus.exporter.stats.stackdriver.StackdriverStatsExporter;

StackdriverStatsExporter.createAndRegister(
    StackdriverStatsConfiguration.builder()
    .setProjectId(gcpProjectId)
    .build());
{{</highlight>}}
{{</tabs>}}


### All exporters combined
Finally, we will combine tracing and stats exporters together.

Our final code snippet is the following:

{{<highlight java>}}
package io.opencensus.tutorial.stackdriver;

import io.opencensus.exporter.stats.stackdriver.StackdriverStatsConfiguration;
import io.opencensus.exporter.stats.stackdriver.StackdriverStatsExporter;
import io.opencensus.exporter.trace.stackdriver.StackdriverTraceConfiguration;
import io.opencensus.exporter.trace.stackdriver.StackdriverTraceExporter;

public class StackdriverTutorial {
    public static void main(String ...args) {
        String gcpProjectId = envOrAlternative("GCP_PROJECT_ID");

        // The trace exporter
        StackdriverTraceExporter.createAndRegister(
                StackdriverTraceConfiguration.builder()
                .setProjectId(gcpProjectId)
                .build());

        // The stats exporter
        StackdriverStatsExporter.createAndRegister(
                StackdriverStatsConfiguration.builder()
                .setProjectId(gcpProjectId)
                .build());
    }

    private static String envOrAlternative(String key, String ...alternatives) {
        String value = System.getenv().get(key);
        if (value != null && value != "")
            return value;

        // Otherwise now look for the alternatives.
        for (String alternative : alternatives) {
            if (alternative != null && alternative != "") {
                value = alternative;
                break;
            }
        }

        return value;
    }
}
{{</highlight>}}

#### Viewing your metrics
Please visit [https://console.cloud.google.com/monitoring](https://console.cloud.google.com/monitoring)

#### Viewing your traces
Please visit [https://console.cloud.google.com/traces/traces](https://console.cloud.google.com/traces/traces)
