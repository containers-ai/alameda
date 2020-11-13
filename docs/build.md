# Build Alameda docker images from source code

## Prerequisites
To build Alameda images from source code, [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) and [Docker](https://docs.docker.com/install/#supported-platforms) environment are required.
> **Note**: It is recommended to use Docker engine 18.09 or greater

As showed in the [Alameda architecture design](https://github.com/containers-ai/alameda/blob/master/design/architecture.md), Running Alameda requires several components. Some of the components leverage existing open-source solutions such as InfluxDB and Grafana. Some of them are implemented in Alameda. The following sections show how to build those components implemented in Alameda from source code.

## Build alameda-operator
```
$ git clone https://github.com/containers-ai/alameda.git
$ cd alameda
$ docker build . -t alameda-operator:latest -f operator/Dockerfile
```

## Build alameda-datahub
```
$ git clone https://github.com/containers-ai/alameda.git
$ cd alameda
$ docker build . -t alameda-datahub:latest -f datahub/Dockerfile
```

## Build alameda-evictioner
```
$ git clone https://github.com/containers-ai/alameda.git
$ cd alameda
$ docker build . -t alameda-evictioner:latest -f evictioner/Dockerfile
```

## Build admission-controller
```
$ git clone https://github.com/containers-ai/alameda.git
$ cd alameda
$ docker build . -t admission-controller:latest -f admission-controller/Dockerfile
```

