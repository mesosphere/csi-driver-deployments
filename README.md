# CSI Driver Deployments

This repository contains manifest files for CSI drivers that have been tested on the [Mesosphere's DC/OS Kubernetes package](https://docs.mesosphere.com/services/kubernetes/).

## Prerequisites

- A Kubernetes v1.13+ cluster
- Access to `kubectl` connected to your Kubernetes cluster(s)
- `wget` utility installed in your environment

## Supported Drivers

- [AWS EBS](https://github.com/mesosphere/csi-driver-deployments/tree/master/aws-ebs/kubernetes)