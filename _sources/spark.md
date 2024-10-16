# Introduction to Apache Spark

Apache Spark is a distributed data processing framework, working across multiple processing nodes in a cluster.

Spark applications are run independent on a cluster, and are coordinated by a _SparkContext_ object (driver program).
The cluster nodes read and write data from and to the file system, caching them in memory as _Resilient Distributed
Datasets_ (RDDs).

## Spark in Azure

In Azure Synapse Analytics, a cluster is implemented as a _Spark pool_.
These can be configured with size of virtual machine (VM), number of nodes (static or auto-scaled), and the version of
the Spark Runtime to be used.
Spark pools are _serverless_.

## Running Spark on Azure

Azure Synapse Studio includes notebooks for working with Spark – providing an intuitive way to combine code with
Markdown notes.
Code cells in notebooks comes with the following features:

- Syntax highlighting and error support
- Code auto-completion
- Interactive data visualizations
- The ability to export results

