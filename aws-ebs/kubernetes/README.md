# Amazon Elastic Block Store CSI driver

Project page: [aws-ebs-csi-driver](https://github.com/kubernetes-sigs/aws-ebs-csi-driver)

## Prerequisites

- A Kubernetes v1.13+ cluster
- Access to `kubectl` connected to your Kubernetes cluster(s)
- `wget` utility installed in your environment

## Kubernetes Version Compability Matrix

| AWS EBS CSI Driver \ Kubernetes Version| v1.13 | v1.14 |
|----------------------------------------|-------|-------|
| latest                                 | no    | yes   |
| v0.5.0                                 | no    | yes   |
| v0.3.0                                 | yes   | no    |
| v0.2.0                                 | yes   | no    |

## Setup

1) Download this repository to a machine that has `kubectl` access to your Kubernetes cluster(s):

```
wget https://github.com/mesosphere/csi-driver-deployments/archive/master.zip -O csi-driver-deployments.zip
unzip csi-driver-deployments.zip && rm csi-driver-deployments.zip
cd csi-driver-deployments-master/aws-ebs/kubernetes
```

2) The CSI driver requires access to the AWS API, below is a sample IAM policy that can be used to grant the driver required permissions.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:AttachVolume",
        "ec2:CreateSnapshot",
        "ec2:CreateTags",
        "ec2:CreateVolume",
        "ec2:DeleteSnapshot",
        "ec2:DeleteTags",
        "ec2:DeleteVolume",
        "ec2:DescribeInstances",
        "ec2:DescribeSnapshots",
        "ec2:DescribeTags",
        "ec2:DescribeVolumes",
        "ec2:DetachVolume"
      ],
      "Resource": "*"
    }
  ]
}
```

The recommended approach is to add the above policy to the EC2 instance roles. If that cannot be done, modify the `secrets.yaml` with `key_id`, `access_key` and optionally `session_token` credentials for a IAM user that does have this policy.

3) Deploy the Kubernetes manifests in your cluster (replace `$VERSION` with the desired version, with `latest/` always containing the `amazon/aws-ebs-csi-driver:latest` image):

```
kubectl apply -f $VERSION
```

## Using the Driver

### An example with a dynamically provisioned volume

1) Deploy Kubernetes manifests from `example-dynamic/`:

```
kubectl apply -f example-dynamic/
```

### An example with a pre-provisioned volume

1) Create a new EBS volume or use an existing one in the same AZ where a Kubelet node is running.

2) Substitute `__REPLACE_ME__` in `example-pre-provisioned/pv.yaml` with the volume ID from above:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pre-provisioned
  annotations:
    pv.kubernetes.io/provisioned-by: ebs.csi.aws.com
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 1Gi
  csi:
    driver: ebs.csi.aws.com
    fsType: ext4
    volumeHandle: __REPLACE_ME__
  claimRef:
    namespace: default
    name: pre-provisioned
  persistentVolumeReclaimPolicy: Retain
```

3) Deploy Kubernetes manifests from `example-pre-provisioned/`:

```
kubectl apply -f example-pre-provisioned/
```