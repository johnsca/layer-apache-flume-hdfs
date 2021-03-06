## Overview

Flume is a distributed, reliable, and available service for efficiently
collecting, aggregating, and moving large amounts of log data. It has a simple
and flexible architecture based on streaming data flows. It is robust and fault
tolerant with tunable reliability mechanisms and many failover and recovery
mechanisms. It uses a simple extensible data model that allows for online
analytic application. Learn more at [flume.apache.org](http://flume.apache.org).

This charm provides a Flume agent designed to ingest events into the shared
filesystem (HDFS) of a connected Hadoop cluster. It is meant to relate to
other Flume agents such as `apache-flume-syslog` and `apache-flume-twitter`.


## Usage

This charm is uses the hadoob base layer and the hdfs interface to pull its dependencies
and act as a client to a hadoop namenode:

You may manually deploy the recommended environment as follows:

    juju deploy apache-hadoop-namenode namenode
    juju deploy apache-hadoop-resourcemanager resourcemgr
    juju deploy apache-hadoop-slave slave
    juju deploy apache-hadoop-plugin plugin

    juju add-relation namenode slave
    juju add-relation resourcemgr slave
    juju add-relation resourcemgr namenode
    juju add-relation plugin resourcemgr
    juju add-relation plugin namenode


Deploy Flume HDFS:

    juju deploy apache-flume-hdfs flume-hdfs
    juju add-relation flume-hdfs plugin

The deployment at this stage isn't very exciting, as the `flume-hdfs` service
is waiting for other Flume agents to connect and send data. You'll probably
want to check out
[apache-flume-syslog](https://jujucharms.com/apache-flume-syslog)
or
[apache-flume-twitter](https://jujucharms.com/apache-flume-twitter)
to provide additional functionality for this deployment.

When `flume-hdfs` receives data, it is stored in a `/user/flume/<event_dir>`
HDFS subdirectory (configured by the connected Flume charm). You can quickly
verify the data written to HDFS using the command line. SSH to the `flume-hdfs`
unit, locate an event, and cat it:

    juju ssh flume-hdfs/0
    hdfs dfs -ls /user/flume/<event_dir>               # <-- find a date
    hdfs dfs -ls /user/flume/<event_dir>/<yyyy-mm-dd>  # <-- find an event
    hdfs dfs -cat /user/flume/<event_dir>/<yyyy-mm-dd>/FlumeData.<id>

This process works well for data serialized in `text` format (the default).
For data serialized in `avro` format, you'll need to copy the file locally
and use the `dfs -text` command. For example, replace the `dfs -cat` command
from above with the following to view files stored in `avro` format:

    hdfs dfs -copyToLocal /user/flume/<event_dir>/<yyyy-mm-dd>/FlumeData.<id> /home/ubuntu/myFile.txt
    hdfs dfs -text file:///home/ubuntu/myFile.txt


## Contact Information

- <bigdata@lists.ubuntu.com>


## Help

- [Apache Flume home page](http://flume.apache.org/)
- [Apache Flume bug tracker](https://issues.apache.org/jira/browse/flume)
- [Apache Flume mailing lists](https://flume.apache.org/mailinglists.html)
- `#juju` on `irc.freenode.net`
