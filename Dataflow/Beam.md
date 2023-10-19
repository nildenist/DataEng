# Beam and Dataflow Relationship

## What is Beam?

   - Apache Beam is an open source unified programming model to define both batch and streaming processing pipelines.
   - To create a pipeline, you can use the Beam SDK of the language of your choice to build a program that defines your data-processing pipeline.
   - Beam SDKs use the same classes to represent both batch and streaming data sources, and the same run forms to operate on that data.
   - A pipeline can be run locally on your computer, remotely on a **virtual machine** in a data center, or by using the **services** of a cloud provider.

### Runner: Why we need it?

To decide which will be the engine powering your pipeline, you need to specify a runner.Each runner has its own configuration, and it is associated with a **backend service**.

***Dataflow is one of the runners available in Apache Beam.***


## What is Dataflow?

Fully-managed data processing service with automated provisioning and management of processing resources. Dataflow includes resource autoscaling and dynamic work rebalancing to maximize resource usage and automatically optimize your pipeline execution. It is part of the Google Cloud ecosystem and uses horizontal service like logging and monitoring.

***Dataflow allows you to separate computing storage resources.***
