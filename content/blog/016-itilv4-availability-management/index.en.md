+++
title = 'Understanding Availability Management in ITIL v4'
date = 2024-01-30T20:34:58+09:00
draft = true
categories = ['Management']
tags = ['ITIL']
+++

## Overview
This article explains availability management as defined in ITIL v4. I will also relate my own experiences to explain the processes involved in availability management.

## What is Availability Management?
Availability management involves activities to ensure the availability of a service. The objective of availability management is to ensure that the service delivers an agreed level of availability to meet the needs of customers and users.

## Processes in Availability Management

There are two main processes in availability management:

* Establishing Service Availability Control
* Analyzing and Improving Service Availability

### Establishing Service Availability Control
Establishing service availability control ensures the availability of a service. It is realized through the following steps:

1. Identifying service availability requirements
2. Agreeing on service availability requirements
3. Deciding on availability measurement requirements
4. Designing availability metrics and reporting

Applying my own experiences to these processes, I understood them as follows:

* Identifying service availability requirements
    * Identifying the types of users and the business risks of service downtime.
    * For my service, which is an internal platform, I identified the impacts on various services using the platform.
* Agreeing on service availability requirements
    * Agreeing on the service's availability (e.g., 99% uptime) in the form of an SLA.
    * We also clarified downtime criteria and exceptions for uptime calculations.
* Deciding on availability measurement requirements
    * As the availability requirements were agreed upon earlier, there were no specific measurement requirements.
* Designing availability metrics and reporting
    * Primarily designed around the 'downtime/uptime' formula.
    * For reporting, we created a dashboard to visualize the availability metrics.

### Analyzing and Improving Service Availability
This process, as the name suggests, involves analyzing and improving service availability. It is realized through the following steps:

1. Analyzing service availability
2. Reporting on service availability
3. Planning and designing for service availability

Applying my own experiences to these processes, I understood them as follows:

* Analyzing service availability
    * Confirming that service availability is being achieved and compiling the data.
* Reporting on service availability
    * Reflecting availability on a dashboard that is accessible to everyone.
* Planning and designing for service availability
    * In case of incidents that affect availability, we formulated plans for prevention of recurrence.

## Reference
* [Availability management: ITIL 4 Practice Guide](https://www.axelos.com/resource-hub/practice/availability-management-itil-4-practice-guide)