# Amazon Elastic Block Store CSI driver

Project page: [aws-ebs-csi-driver](https://github.com/kubernetes-sigs/aws-ebs-csi-driver)

## Setup

1) Download this repository to a machine that has `kubectl` access to your Kubernetes cluster(s):

```
wget https://github.com/mesosphere/csi-driver-deployments/archive/master.zip -O csi-driver-deployments.zip; unzip csi-driver-deployments.zip; rm csi-driver-deployments.zip
```

2) The CSI driver requires access to the AWS API, below is a sample IAM policy that can be used to grant the driver required permissions.

```
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

3) Deploy the Kubernetes manifests in your cluster (replace `$VERSION` with the desired version):

```
kubectl apply -f csi-driver-deployments/aws-ebs/kubernetes/$VERSION
```