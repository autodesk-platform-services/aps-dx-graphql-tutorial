---
layout: page
title: About
permalink: /about/
nav_order: 6
---

# About this tutorial

The purpose of this tutorial is to showcase the new Data Exchange GraphQL API. It’s OK if you are not familiar with the overall Autodesk Platform Services APIs as the Data Models are bringing a new approach to accessing granular data. But to be familiar with other aspects, especially when you are ready to use the Data Exchange API into your web app, please make sure you are familiar with APS. If you are new to the platform, you can start at the [APS Tutorials page](https://tutorials.autodesk.io).

The Data Exchange is a new approach for getting granular data. If you are already familiar with Model Derivative data from a Revit file, this data will look very familiar to you. The Data Model approach to accessing data brings a “real-time” aspect to the data, and also direct access to the data you need. Model Derivative is basically a file conversion, and as a result, all the data is produced at once, putting the burden of accessing it granularly on you – the developer. The Data Exchange API, instead, will allow you to access specific types of data directly, bringing the promise of granular data to life. Model Derivative data is a “snapshot” of data at a given time or version, while an exchange once initially created, will stay synchronized by the Revit design changes, and also includes design versioning.

In this tutorial, we'll go through a set of queries you can make using the new Data Exchange API (currently in public beta).
We'll walk you through the prerequisites, initial workflows, advanced queries, and some interesting cases that can be covered using this API.

If you're interested in any of the following cases, then this tutorial is suitable to you:

1. Querying metadata from subset of your Revit designs \*
2. Take advantage of the benefits of working with a GraphQL interface
3. Experiment with the queries without the need of building any sample.

\* Limited to Revit 2024 files hosted on ACC allow-listed hubs
