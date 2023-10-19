# Beam and Dataflow Relationship

## What is Beam?

   - Apache Beam is an open source unified programming model to define both batch and streaming processing pipelines.
   - To create a pipeline, you can use the Beam SDK of the language of your choice to build a program that defines your data-processing pipeline.
   - Beam SDKs use the same classes to represent both batch and streaming data sources, and the same run forms to operate on that data.
   - A pipeline can be run locally on your computer, remotely on a **virtual machine** in a data center, or by using the **services** of a cloud provider.

![what_is_Beam](https://github.com/nildenist/DataEng/assets/28653377/a4d85d81-925c-43cd-9673-8941894f260b)



### Runner: Why we need it?

To decide which will be the engine powering your pipeline, you need to specify a runner.Each runner has its own configuration, and it is associated with a **backend service**.

***Dataflow is one of the runners available in Apache Beam.***


## What is Dataflow?

Fully-managed data processing service with automated provisioning and management of processing resources. Dataflow includes resource autoscaling and dynamic work rebalancing to maximize resource usage and automatically optimize your pipeline execution. It is part of the Google Cloud ecosystem and uses horizontal service like logging and monitoring.

![chrome-capture-2023-9-19 (5)](https://github.com/nildenist/DataEng/assets/28653377/a15b3842-132f-402b-869d-eb3972afc9ee)

***Dataflow allows you to separate computing storage resources.***


## Beam Portability

The Beam vision is to provide a comprehensive portability framework for data processing pipelines, one that allows you to write your pipeline once in the programming language of your choice and run it with minimal effort on the execution engine of your choice.

With Apache Beam, you can define your pipeline in popular languages like Java, Python, Go, SQL.

![chrome-capture-2023-9-19 (7)](https://github.com/nildenist/DataEng/assets/28653377/bb4ebb85-e87c-40c0-bb6d-d824e1a45d70)

With Beam, you also have the flexibility to move your data processing pipeline from your own premise environment to Dataflow on Google Cloud or any other clouds.

***There is no vendor lock-in.***

Dataflow offers the most compelling experience across all of the runners.

### Portability Framework

The portability framework is a language-agnostic way of representing and executing Beam pipelines. It introduces well-defined, language neutral data structures and protocols between the SDKs and the runners. This interoperability layer is called **Portability API** and enables you to use the language of your choice 
with the runner of your choice, thus ensuring that SDKs and runners can work with each other uniformly.

Moreover, because docker containerization is used, you can **customize** the execution environment running on the worker nodes of the back end service.

Portability brings several additional benefits.
   - With portability, every runner can work with every supported language.
   - Containerization allows us a configurable, hermetic worker environment.
   - You can have multi-language pipelines and cross-language transforms because of the language-agnostic representation of pipelines and the isolated environment of each operation.

And all of this will bring you a faster delivery of new features available in the SDKs because, with portability, every time a new functionality is added to a supported language, it will automatically be available to all DRs.

## Runner v2

To use the portability features mentioned earlier, you must use the Dataflow Runner v2. 
This new version of the Dataflow Runner uses a more efficient and portable work architecture, based on the Apache Beam portability framework. It supports custom containers, multi-language pipelines and cross-language transforms. 
This runner is packaged together with the **Dataflow Shuffle** service and **Streaming Engine**.

To enable Runner v2, refer to Dataflow official documentation.
https://cloud.google.com/dataflow/docs

## Container Environments

The Beam SDK runtime environment can be containerized with Docker to isolate it from other runtime systems. Each user operation has an associated environment in which to execute. 
Typically, supported SDKs provide a default environment that you can further customize. Because of containerization, you can benefit from ahead-of-time installation. You can include arbitrary dependencies, and even further customization is possible.

### How a pipeline with custom container could run?

To use this feature, 
1) you need to have the Apache Beam SDK version 2.25.0 or later installed.
2) If you want to test your pipeline locally, you will also need to have Docker installed.
3) To create a custom container image, create a Docker file in which you specify the Apache Beam image as the parent image.

```console
# Specifying the base image with FROM instruction
FROM apache.beam_python3.8_sdk:2.25.0
# Adding an environment variable with ENV instruction
ENV MY_FILE_NAME=my_file.txt
# Copying files to add to the custom image with COPY instruction
COPY path/to/myfile/$MY_FILE_NAME ./
```


4) Then add your own customizations.After creating your custom Docker file, you need to build the image and push it to a container registry.
5) To do so, you need to specify your project, the name of the image repository, the tag that you want to associate with your image, and the image registry host name.

```console
export PROJECT=my-project-id
export REPO=my-repository
export TAG=my-image-tag 
export REGISTRY_HOST=gcr.io
export IMAGE_URI=$REGISTRY_HOST/$PROJECT/$REPO:$TAG
```
6) Then you can use either Cloud Build or Docker to build the image and push it to a container registry like GCR.IO.

```console
gcloud builds submit --tag $IMAGE_URI
```

7) Finally, you can launch your Dataflow job by referencing the regular parameters and the location of the custom container image.

```console
docker build -f Dockerfile -t $IMAGE_URI ./
docker push $IMAGE_URI
```


## Cross-language transforms
