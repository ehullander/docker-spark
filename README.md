# Docker-Spark-Tutorial

You will need to set up multiple machines with a cloud provider such as AWS or Azure, or local.

***
## Apache Spark

***
## Examples
The examples/ directory contains some example python scripts and jupyter notebooks for demonstrating various aspects of 
of Spark.

## passwordless ssh on master and workers  
```
$ ssh-keygen -t rsa  
$ ssh <user>@<worker_ip.local> mkdir -p .ssh  
$ cat .ssh/id_rsa.pub | <user>@<worker_ip.local> 'cat >> .ssh/authorized_keys'  
$ ssh <user>@<worker_ip.local> "chmod 700 .ssh; chmod 640 .ssh/authorized_keys"  
```

## Getting Started

To get started, pull the following three docker images
```
# On workers and manager
git pull ehullander/docker-spark 
masterPC: docker build -f Dockerfile_master --tag spark-master:1.0 .
masterPC: docker build -f Dockerfile_submit --tag spark-submit:1.0 .
workerPC: docker build -f Dockerfile_worker --tag spark-worker:1.0 .
```
Create a docker swarm using  

try ifconfig and the last ip address is the host, not br or docker

``` 
docker swarm init --advertise-addr <your-host-ip, e.g. 10.0.0.115>  
```
then attach the other machines you wish to be in the cluster to the docker swarm by copying and
pasting the output from the above command.

Create an overlay network by running the following on one of the machines
``` 
docker network create -d overlay --attachable spark-net
```
On the machine you wish to be the master node of the Spark cluster run
``` 
docker run -it --name spark-master --network spark-net -p 8080:8080 spark-master:1.0
```
On the machines you wish to be workers run
``` 
docker run -it --name spark-worker1 --network spark-net -p 8081:8081 -e MEMORY=6G -e CORES=3 spark-worker:1.0
```
substituting the values for CORES and MEMORY to be cores of the machine - 1 and RAM of machine
- 1GB.

http://localhost:8080/  

Start a driver node by running
``` 
docker run -it --name spark-submit --network spark-net -p 4040:4040 -p 8888:8888 spark-submit:1.0 jupyter notebook --ip 0.0.0.0 --allow-root --NotebookApp.token=''
```

You can now either submit files to spark using 
``` 
$SPARK_HOME/bin/spark-submit [flags] file 
```

or run a pyspark shell
``` 
$SPARK_HOME/bin/pyspark
```

