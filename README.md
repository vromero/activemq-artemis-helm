
## 1. What is ActiveMQ Artemis?

[Apache ActiveMQ Artemis](https://activemq.apache.org/artemis) is an open source project to build a multi-protocol, embeddable, very high performance, clustered, asynchronous messaging system. Apache ActiveMQ Artemis is an example of Message Oriented Middleware (MoM).

![logo](https://activemq.apache.org/artemis/images/activemq-logo.png)

## 2. What is the ActiveMQ Artemis Helm Chart

A chart is package composed of files that describe a related set of [Kubernetes](http://kubernetes.io) resources. The packages
stand in a [Helm](https://helm.sh) repository. This Git repository is a Helm repository too. 

Helm packages can have dependencies much like Debian packages can have dependencies, and they greatly 
simplify the management of complex distributed systems in Kubernetes.

This chart bootstraps a cluster of ActiveMQ Artemis nodes with a configurable size. The topology is a
[symmetric cluster](https://activemq.apache.org/artemis/docs/latest/clusters.html#cluster-topologies) with only one
active master.

## 3. Prerequisites

- Kubernetes 19+ with Beta APIs enabled
- PV provisioner support in the underlying infrastructure

## 4. Installing the Chart

Just like in Debian, Fedora or similar. First we need to add a repository to our list:

```bash
helm repo add activemq-artemis https://vromero.github.io/activemq-artemis-helm/
```
At this point when the package is searched for it should be found:

```bash
CHART VERSION	APP VERSION	DESCRIPTION                                       
activemq-artemis/activemq-artemis	0.0.1        	           	a multi-protocol, embeddable, very high perform...
```

If the package could be found in the previous step, to install the chart with the release name `my-release` just:

```bash
helm install --name my-release activemq-artemis/activemq-artemis
```

The command deploys ActiveMQ Artemis on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

By default a random password will be generated for the `artemis` user. If you'd like to set your own password change the artemisPassword
in the values.yaml.

You can retrieve your password by running the following command. Make sure to replace [YOUR_RELEASE_NAME]:

```bash
kubectl get secret my-release-activemq-artemis -o jsonpath="{.data.artemis-password}" | base64 --decode; echo
```

> **Tip**: List all releases using `helm list`

After the install some information should have appear in the console. If you missed it and want to check it out again try:

```bash
helm status my-release
```

## 5. Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
helm delete my-release
```
The command removes all the Kubernetes components associated with the chart and deletes the release.

## 6. Configuration

The following tables lists the configurable parameters of the MySQL chart and their default values.

| Parameter                            | Description                           | Default                                                    |
| ------------------------------------ | ------------------------------------- | ---------------------------------------------------------- |
| `imageTag`                           | `vromero/activemq-artemis` image tag. | Most recent release                                        |
| `imagePullPolicy`                    | Image pull policy                     | `IfNotPresent`                                             |
| `artemisUser`                        | Username of new user to create.       | `artemis`                                                  |
| `artemisPassword`                    | Password for the new user.            | `simetraehcapa`                                            |
| `replicas`                           | Number of nodes in the cluster.       | 2                                                          |
| `persistence.enabled`                | Create a volume to store data         | true                                                       |
| `persistence.size`                   | Size of persistent volume claim       | 8Gi RW                                                     |
| `persistence.storageClass`           | Type of persistent volume claim       | nil  (uses alpha storage class annotation)                 |
| `persistence.accessMode`             | ReadWriteOnce or ReadOnly             | ReadWriteOnce                                              |
| `persistence.testJournalPerformance` | See docker image docs                 | `AUTO`                                                     |
| `resources.request.memory`           | Memory resource requests/limits       | `256Mi`                                                    |
| `resources.request.cpu`              | CPU/Memory resource requests/limits   | `100m`                                                     |

Some of the parameters above map to the env variables defined in the [vromero's ActiveMQ Artemis image](https://hub.docker.com/r/vromero/activemq-artemis/) refer to it for values, meaning, etc.

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install --name my-release \
  --set artemisUser=my-user,artemisPassword=my-password \
  activemq-artemis
```

The above command creates a user named `my-user`, with the password `my-password`.

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```bash
$ helm install --name my-release -f values.yaml activemq-artemis
```
> **Tip**: You can use the default [values.yaml](activemq-artemis/values.yaml) as template.

## 7. Persistence

The ActiveMQ Artemis image stores the broker data in `/var/lib/artemis/data` and configurations at the `/var/lib/artemis/etc` path of the container.

By default a PersistentVolumeClaim is created and mounted into the `/var/lib/artemis/data`. In order to disable this functionality
you can change the values.yaml to disable persistence and use an emptyDir instead.

> *"An emptyDir volume is first created when a Pod is assigned to a Node, and exists as long as that Pod is running on that node. When a Pod is removed from a node for any reason, the data in the emptyDir is deleted forever."*
