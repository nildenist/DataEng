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

```console
docker build -f Dockerfile -t $IMAGE_URI ./
docker push $IMAGE_URI
```

7) Finally, you can launch your Dataflow job by referencing the regular parameters and the location of the custom container image.

```console
python my-pipeline.py \
   --input=INPUT_FILE \
   --output=OUTPUT_FILE \
   --project=PROJECT_ID \
   --region=REGION \
   --temp_location=TEMP_LOCATION \
   --runner=DataflowRunner \
   --worker_harness_container_image=$IMAGE_URI
```


## Cross-language transforms

With a language agnostic representation of pipelines and the possibility to specify the environment of each operation, you are no longer limited to a single language in a given pipeline.

Portability makes it possible for you to run multi-language pipelines that leverage the respective benefits of the individualized case.

For example, you can now write a Python pipeline while using IO connectors that were only available in Java, or if you want to use a TensorFlow extended block of code for a machine learning model in your Java pipeline, you can now use a cross-language transform.

```console
from apache_beam.io.kafka import ReadFromKafka

with beam.Pipeline(options=<Your Beam PipelineOptions object>) as p:
      p
      | ReadFromKafka(
         consumer_config={'bootstrap.servers':'<Kafka bootstrap servers list>'},
            topics=[<List of Kafka topics>])

```

This code represents a part of a Python streaming pipeline. The ```ReadFromKafka``` transform imported from the ```apache_beam.io.kafka``` module is a cross-language transform implemented using the Beam Java SDK.

Under the hood, to make Java transforms available to a dataflow Python pipeline, The Apache Beam Python SDK starts up a ***local Java service*** on your computer to create and inject the appropriate Java pipeline fragments into your Python pipeline.

Then the SDK downloads in stages the necessary Java dependencies needed to execute these transforms, and at run time, the dataflow workers will execute the Python and Java code simultaneously to run your pipeline.

Let's have a refresh our knowledge for **Beam Portability**

<h3> Question 1. What is the Beam Portability Framework? </h3>

- [ ] A hermetic worker environment <br/>
- [ ] A language-agnostic way to represent pipelines <br/>
- [ ] A set of cross-language transforms <br/>
- [ ] A set of protocols for executing pipelines <br/>
      
</div>

<details>
  <summary><b>Click here for the answer</b></summary>
<br>
<div id="q79" class="collapse">
   
- [ ] A hermetic worker environment <br/>
- [x] A language-agnostic way to represent pipelines <br/>
- [ ] A set of cross-language transforms <br/>
- [x] A set of protocols for executing pipelines <br/>

 </b>
</div>
</details>


<h3> Question 2. Which of the following are benefits of Beam Portability (Select ALL that apply) ? </h3>

- [ ] Cross-language transforms <br/>
- [ ] Running pipelines authored in any SDK on any runner <br/>
- [ ] Implement new Beam transforms using a language of choice and utilize these transforms from other languages <br/>
      
</div>

<details>
  <summary><b>Click here for the answer</b></summary>
<br>
<div id="q79" class="collapse">
   
- [x] Cross-language transforms <br/>
- [x] Running pipelines authored in any SDK on any runner <br/>
- [x] Implement new Beam transforms using a language of choice and utilize these transforms from other languages <br/>

 </b>
</div>
</details>


# Separating Compute ans Storage with Dataflow


## Dataflow

Dataflow allows you to execute your VM pipelines on Google Cloud. There are several reasons why customers love Dataflow so much.

First, it's because it is fully managed and autoconfigured.

Second, Dataflow optimizes the graph execution by fusing operations efficiently and by not waiting for previous steps to finish before starting a new one unless there is a dependency involved.

Third, autoscaling happens step by step in the middle of a pipeline job. As a job needs more resources, it receives them automatically.

You don't have to manually scale resources to match job needs, and you don't pay for VM resources **that aren't being used.*** Dataflow will turn down the workers as the job demand decreases. 

All of this happens while maintaining strong streaming semantics Aggregations like sums and counts are correct, even if the input source sends duplicate records.
![chrome-capture-2023-9-19 (8)](https://github.com/nildenist/DataEng/assets/28653377/911a7ae2-4463-49bf-986a-15dd07ca5790)

## Dataflow Shuffle Service

A shuffle is a Dataflow-based operation behind transforms such as GroupByKey, CoGroupByKey, and Combine.
![Screenshot from 2023-10-23 16-42-46](https://github.com/nildenist/DataEng/assets/28653377/f3098cbd-0c29-4b39-818d-3423db5ff74c)

The Dataflow Shuffle operation partitions and groups data by key in a scalable, efficient, fault-tolerant manner.

Currently, Dataflow uses a shuffle implementation that runs entirely on worker virtual machines and consumes worker CPU, memory, and persistent disk storage.

The service-based Dataflow Shuffle feature available for batch pipelines only moves the shuffle operations out of the worker VMs and into the Dataflow service backend.

![shuffle_batch](https://github.com/nildenist/DataEng/assets/28653377/dc87de23-628b-40ab-972b-73050db8273c)

With the Dataflow Shuffle service, you will have **faster execution time** of batch pipelines for the majority of the job types.

The worker nodes will benefit from a **reduction** in consumed **CPU**, **memory**, and persistent **disk storage** resources, and your pipelines will have better **autoscaling** because the worker nodes VMs no longer hold any shuffle data, and can therefore be scaled down earlier.

Also, because of the service, you will get better **fault tolerance**.

An unhealthy VM holding Dataflow Shuffle data will not cause the entire job to fail, which would happen without the feature.

## Dataflow Streaming Engine

Just like shuffle component in batch, the streaming engine offloads the window state storage from the persistent disks attached to worker VMs to a back-end service.

It also implements an efficient shuffle for streaming cases. No code changes are required.

Worker nodes continue running your user code and implements data transforms and transparently communicate with a streaming engine to source state.

![dataflow_streaming_engine](https://github.com/nildenist/DataEng/assets/28653377/2cbbfcb0-cb19-402c-ac73-0744f4433ca9)

With the dataflow streaming engine, you will have a reduction in consumed **CPU**, **memory**, and persistent disk **storage** resources on the worker VMs.

Streaming engine works best with smaller worker machine types like n1-standard-2, and does not require persistent disks beyond a smaller worker boot disk. This leads to a lower resource and quota consumption.

With streaming engine, your pipeline will be more **responsive** to variations to incoming data volume.

You will have improved **supportability**, since you don't need to redeploy your pipelines to applied service updates.

## Flexible Resource Scheduling

Flexible Resource Scheduling, or in short, ***FlexRS***.

![dataflow_FlexRS](https://github.com/nildenist/DataEng/assets/28653377/2f6fec83-e135-4179-aa02-4afb1b47ddd6)

FlexRS helps you **reduce** the cost of your batch processing pipelines because you can use **advanced scheduling** techniques in the **Dataflow Shuffle Service** and leverage a mix of **preemptible** and **normal virtual machines**.

When you submit a FlexRS job, the Dataflow service places the job into a queue and submits it for **execution** within **6 hours** from job creation. This makes FlexRS suitable for workloads that are not **time-critical**, such as ***daily*** or ***weekly*** jobs that can be completed within a certain time window.

As soon as you submit your FlexRS job, Dataflow records a job ID and performs an **early validation** run to verify execution parameters, configurations, quota and permissions.

In case of failure, the **error** is reported immediately, and you don't have to wait for a delayed execution.










