# MLOps Course

[CourseLink](https://github.com/GokuMohandas/mlops-course?tab=readme-ov-file): Learn how to combine machine learning with software engineering best practices to design, develop, deploy and iterate on production-grade ML applications. 


<div align="center">
<img title="Overview" alt="Alt text" src="./images/mlDevOPs.png " width="500">
</div>

The specific task in the course is to fine-tune an LLM for supervised text classification, but anything learned can be easily extended to any class of algorithms (regression -> LLMs), applications (NLP, CV, tabular), tooling stacks (aws, pytorch, huggingface), stack and scale.

<div align="center">
<img title="First Part Cource" alt="Alt text" src="./images/mlDevOPs2.png" width="600">
</div>

The first half of the course involves implementing our machine learning workloads, using some of the best open source tools like PyTorch and Huggingface and integrating with MLOps platforms for workloads around experiment tracking, data validation etc. The workload will be scaled with Ray -> framework for scaling and productionizing machine learnung applications, widly adopted by companies like OpenAI, Spotify, Instacart, Netflix etc. 

<div align="center">
<img title="Second Part Course" alt="Alt text" src="./images/mlDevOPs3.png" width="500">
</div>

In the latter half of the course we'll be productionizing the machine learning workloads that we've developed and scaled. Our ML workloads have been responsible for consuming data and producing model artifacts. We'll then save this artifacts to a centralized model registri and then serve it through an endpoint. Tically this process of productionization is anything but smooth. Things that worked in our development environment no longer work at scale and the production environment is usually very different from the development one. Finally we'll extend from our manual productionization process here towards a more continuos one with CI/CD pipelines. 

<div align="center">
<img title="Final Part Course" alt="Alt text" src="./images/mlDevOPs4.png" width="800">
</div>

We'll design our system so that whenever we make a change to our code, it'll kick off our machine learning workloads. Once they're done, the results will be automatically commented to our PR for additional discussions and then deployed to update our service. This architecture can be easily be extended to other external triggers such as performance regressions or new data coming in. All of this will be done in a very modular way, making it very easy to take all of it and integrate with my own stack at work

-------------------------------------

# 1. Desgin

## Cluster

Cluster: refers to a group of servers that come together to form one system. Each cluster will have a head node that manages the cluster and it will be connected to a set of worker nodes that will execute workloads. These clusters can be fixed in size or autoscale based on our application's compute needs. We'll create our cluster by defining a compute configuration and an environment.

Using [Ray](https://docs.ray.io/en/latest/cluster/key-concepts.html#head-node):
<div style="font-size:11px;">
- Head Node - Every Ray cluster has one node which is designated as the head node of the cluster. The head node is identical to other worker nodes, except that it also runs singleton processes responsible for cluster management such as the autoscaler*, GCS and the Ray driver processes which run Ray jobs**
- Worker Node - do not run any head node management processes, and serve only to run user code in Ray tasks and actors. They participate in distributed scheduling, as well as the storage and distribution of Ray objects in cluster memory.

* Autoscaling: The Ray autoscaler is a process that runs on the head node (or as a sidecar container in the head pod if using Kubernetes). When the resource demands of the Ray workload exceed the current capacity of the cluster, the autoscaler will try to increase the number of worker nodes. When worker nodes sit idle, the autoscaler will remove worker nodes from the cluster. It is important to understand that the autoscaler only reacts to task and actor resource requests, and not application metrics or physical resource utilization. (see VMs and Kubernetes)

** Ray Jobs: A Ray job is a single application: it is the collection of Ray tasks, objects, and actors that originate from the same script. The worker that runs the Python script is known as the driver of the job. There are two ways to run a Ray job on a Ray cluster:
1. (Recommended) Submit the job using the Ray Jobs API.
2. Run the driver script directly on the Ray cluster, for interactive development.
</div>

Steps for configuration: (choice on using Anyscale, or personal laptop. I chose the latter)

1. Environment: need Python (recommend 3.10), create a virtual environment to install our dependencies
2. Compute: define our compute configuration, which will specify our hardware dependencies (head and worker nodes) that we'll need for our workloads. The personal laptop (single machine) will act as the cluster, where one CPU will be the head node and some of the remaining CPU will be the worker nodes (no GPUs required).
3. Workspace: create our cluster workspace,  where we'll be developing our ML application on top of our compute, environment and storage. (laptop needs IDE installed, like VS Code)
4. Git: create a new repo (Made-With-ML), with README file (very important as this creates a main branch). Clone the Made With ML repository's contents, then install the packages inside the venv using the requirements.txt file (python3 -m pip install -r requirements.txt) 