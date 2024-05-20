+++
title = 'Explaining Capacity and Performance Management in ITIL v4'
date = 2024-02-27T08:53:36+09:00
draft = true
categories = ['Management']
tags = ['ITIL', 'Capacity Management', 'Performance Management']
+++

## Overview
This article explains capacity and performance management as outlined in ITIL v4. I will also apply what I have understood to my own experiences, explaining the process of capacity and performance management.

## What are Capacity and Performance Management?
Capacity and Performance Management involves managing the performance of services and the resources supporting them. The goal is to optimize the performance of services and ensure that the capacity of services is appropriately maintained.

## Processes in Capacity and Performance Management
The processes in capacity and performance management include:

* Establishing Capacity and Performance Control
* Analyzing and Improving Service Capacity and Performance

### Establishing Capacity and Performance Control
Establishing capacity and performance control involves agreeing with stakeholders on the usage and performance standards for the IT resources used by the service, and deciding on the timing, baseline, and format for evaluation.

It is realized through the following steps:

1. Identifying service capacity and performance requirements
2. Agreeing on service capacity and performance requirements
3. Deciding on capacity and performance requirements
4. Designing capacity and performance evaluation metrics and reports

Applying my experiences to these processes, I understood them as follows:

* Identifying service capacity and performance requirements
    * As an internal platform provider, I identified latency performance requirements (99%ile in Nms) demanded by internal users.
    * Based on these thresholds, we conducted performance verification and measured throughput per instance.
    * We calculated the cost based on the throughput measurements.
* Agreeing on service capacity and performance requirements
    * We agreed on latency performance and throughput with stakeholders.
* Deciding on capacity and performance requirements
    * This remained unchanged from what was agreed upon.
* Designing capacity and performance evaluation metrics and reports
    * We used a tracing tool called Dynatrace to measure and report performance.

### Analyzing and Improving Service Capacity and Performance

Analyze issues in usage and performance conditions from service output logs and incident information.

It is realized through the following steps:

1. Analyzing capacity and performance
2. Reporting capacity and performance
3. Planning and designing capacity and performance

Applying my experiences to these activities, I understood them as follows:

* Analyzing capacity and performance
    * We used Dynatrace to analyze latency performance and throughput.
    * We identified performance issues from incident information.
* Reporting capacity and performance
    * We visualized latency performance and throughput using Dynatrace's dashboard.
* Planning and designing capacity and performance
    * Anticipating growth in users, we forecasted that the current capacity would be insufficient.
    * We made plans to increase capacity based on demand forecasts and executed them.

## Summary
In this article, I explained capacity and performance management based on what I have learned and applied it to my own experiences. I understood that capacity and performance management involves managing aspects similar to availability management, but from the perspective of capacity and performance.  
In my experience, discussions about performance and availability often go hand in hand, and I rarely deal with them independently.