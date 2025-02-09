<img alt="Topolvm-Operator" src="./docs/logo.svg" width="250"/>  

Topolvm-Operator
========

Topolvm-Operator is an open source **cloud-native local storage** orchestrator for Kubernetes, which bases on [topolvm](https://github.com/topolvm/topolvm).

Supported environments
----------------------

- Kubernetes: 1.20, 1.19
- Node OS: Linux with LVM2
- Filesystems: ext4, xfs

The [CSIStorageCapacity](https://kubernetes.io/docs/concepts/storage/storage-capacity/) feature gate should be turned on

Features
--------

- Orchestrate topolvm
- Prepare volume group
- Volume group dynamic expand
- Perception of storage topology
- Volume capacity limit
- PVC snapshot
- Prometheus metric and alarm
- Auto discover available devices

OperatorHub.io
--------

[Topolvm Operator](https://operatorhub.io/operator/topolvm-operator) had been shared in operatorhub.io home.  


### Planned features

- Raid of volume group
- Manage volume group that user created


Components
-------
- `operator`: orchestrate topolvm include `TopolvmCluster controller` and `ConfigMap controller`
- `preparevg`: prepare volume group on each node


### Diagram

A diagram of components and the how they work see below:
![component diagram](./diagram.svg)

### How components work

1. `Topolvm-operator` watch the `TopolvmCluster`(CRD) 
2. `Topolvm-operator` watch the `operator-setting ConfigMap`
3. `Topolvm-operator` start `discover devices Daemonset`
4. `Topolvm-operator` start  `ConfigMap controller` to watch `lvmd ConfigMap` if `TopolvmCluster` created
5. `TopolvmCluster controller` create `preparevg` Job,`Topolvm-controller` Deployment depend on `TopolvmCluster`
6. `preparevg` Job on specific node check disk that provided in `TopolvmCluster` and create volume group, if volume group created successfully and then create `lvmd ConfigMap` for the node
7. `ConfigMap controller` finds the new `lvmd ConfigMap` then create `Topolvm-node` Deployment
8. `TopolvmCluster controller` update `TopolvmCluster` status

Getting started and Documentation
---------------
[docs](docs/) directory contains documents about installation and specifications


Topolvm-operator vs Other local storage Implement
-------------


|            |         nfs                          |     rook ceph              |           longhorn   |  host path       |  topolvm
| ---------- | ------------------------------|-----------------|-----------------------------------------|----|------------------------|
| filesystem        | yes            | yes                                        | yes                           | yes         | yes
| filesystem type   | nfs       | ext4/xfs                                      | driver specific                 | ext4/xfs        | ext4/xfs                                      |
| block             | no                | yes (rbd)                             | yes                              | no      | yes                                                   |
| bandwidth         | standard    | high                                       | high                               | high      |high                                                     |
| IOPS              |   standard       | standard                             | standard                            | high        | high                                                   |
| latency       | standard      | standard                                       | standard                           | low      | low                                                  |
| snapshot       | no               | yes                              | yes                                       | no          | yes                                      |
| clone       | no                   | yes                       | no                                               | no         | no                                           |
| quota       | no                | yes                                      | yes                                    | no      | yes                                                 |
| access mod | ReadWriteOnce ReadOnlyMany ReadWriteMany| ReadWriteOnce ReadOnlyMany ReadWriteMany|  ReadWriteOnce ReadOnlyMany |  ReadWriteOnce|  ReadWriteOnce ReadWriteOncePod
| resizing       | yes            | yes                                       | yes                            | yes          |yes                                               |
|data redundancy |Hardware RAID  | yes | yes| Hardware RAID| Hardware RAID
|protocol type | nfs | rados| iscsi | fs | lvm
|ease of maintainess| driver specific|  high maintainess effort| medium|  medium | ops-free
|usage scenarios| general storage| extremly scalability| container attach storage|     temporary data       |   high performance block device for cloudnative applications


Topolvm
-------------

topolvm-operator is based on topolvm, we fork [topolvm/topolvm](https://github.com/topolvm/topolvm)  and do some enhancements. 

see [alauda/topolvm](https://github.com/alauda/topolvm)

the enhancements are below:

- remove topolvm-scheduler 
- lvmd containerized
- add new feature snapshot 

Docker images
------------

- topolvm-operator [alaudapublic/topolvm-operator](https://hub.docker.com/r/alaudapublic/topolvm-operator)
- topolvm [alaudapublic/topolvm](https://hub.docker.com/r/alaudapublic/topolvm-operator)



Report a Bug
----------
For filing bugs, suggesting improvements, or requesting new features, please open an [issue](https://github.com/alauda/topolvm-operator/issues).





