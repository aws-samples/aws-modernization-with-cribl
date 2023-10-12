---
title: How it works
chapter: true
weight: 2 
---




## How Cribl Works
Before we go ahead and work on deploying Cribl, let us understand what is Cribl and how it works. Cribl Stream allows customers to send their machine data from any source to any destination. Take Control and Shape Your Data. With extensive experience building and deploying log analytics and observability solutions for the world's largest organizations, Cribl provides unprecedented control over log ingestion. 

Cribl Stream is a vendor-agnostic observability platform that gives you the flexibility to route, shape, optimize, and enrich data from any source to any destination without adding new agents. Cribl Stream helps you process machine data logs, instrumentation data, application data, metrics, etc. in real time, and deliver them to your analysis platform of choice. It allows you to:

* Add context to your data, by enriching it with information from external data sources both on on-prem and cloud.(1,2)
* Help secure your data, by redacting, obfuscating, or encrypting sensitive fields.(3)
* Optimize your data, per your performance and cost requirements.(3).
* Send the data to one or more destinations like S3, Kinesis, SQS to feed Analytics Solution(4) Security Lake* in OCSF format(5)
* Collect or/and write data to another AWS account using a Cross-Account IAM role xallows without the need to generate IAM static keys.

Wherever your data comes from, wherever it needs to go, Cribl gives you the freedom and flexibility to make choices instead of compromises. 

![Cribl Products](https://docs.cribl.io/img/page/ISO_arch_lightmode.svg)

In this section, we will cover the following topics:

* [Integrating Cribl Stream and Search into Amazon Security Lake](/content/2_Security%20Lake%20Integration/_index.md)
* [Converting VPC Flow Logs to Metrics](/content/3_VPC%20Flow%20Logs%20to%20Metrics/_index.md)
* [Optimizing CloudTrail Events](/content/5_CloudTrail%20Optimization/_index.md)