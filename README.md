# docker-centos-slurm

This is my personal development Docker Slurm development containers based on CentOS 7. Forked off the great [bikerdanny/docker-centos-slurm](https://github.com/bikerdanny/docker-centos-slurm) repo.

## Pre-requisites

* Install j2

```
pip3 install j2cli
```

## Start the containers 

* Generate the image and the Docker compose

```
cd docker-centos-slurm
j2 Dockerfile.j2 config.ini > Dockerfile
docker build -t miguelgila/slurm:20.02.2 .
j2 aio.docker-compose.yml.j2 config.ini > docker-compose.yml
```

* Create needed dependencies

```
mkdir var_lib_mysql
mkdir etc_munge
mkdir etc_slurm
mkdir shared
```

* Start up the docker containers and get stuff going

```
docker-compose up -d
docker-compose exec c10 bash
supervisorctl stop slurmctld
echo "SwitchName=s2 Nodes=c10" > /etc/slurm/topology.conf #FIXME this is ugly, needs to be improved
echo "#
# TOPOLOGY
TopologyPlugin=topology/tree" >> /etc/slurm/slurm.conf #FIXME this is ugly
sacctmgr show cluster # if the command returns successfully, you can start slurmctld again
supervisorctl start slurmctld
sinfo
```

## How to stop the containers

```
docker-compose down
```


