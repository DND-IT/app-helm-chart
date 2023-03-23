# app-helm-chart
Contains the generic helm chart for an application. It is used by Prometheus & Disco project

## Usage

Reference the release of the chart you want to deploy in terraform

```hcl
resource "helm_release" "app" {
  chart     = "https://github.com/DND-IT/app-helm-chart/archive/3.0.0.tar.gz"
  
  values = [
    templatefile("values.yaml")
  ]
  set {
    name  = "service.name"
    value = "myname-myenv"
  }
}
```

## AWS Role

If you specify a IAM role in `aws_iam_role_arn` a service account will be created to take advantage of [fine grained permission for pods](https://aws.amazon.com/blogs/opensource/introducing-fine-grained-iam-roles-service-accounts/)

## Pod Disruption Budget

By default this chart include a sort of fail-safe mechanism to prevent huge application disruptions. [K8s documentation](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) explains very well what are the benefits and the limits of settings PDBs in case of voluntary disruptions.

For highly-available deployments, the chart allows a minimum availability of 50%. For production-critical applications we recommend evaluating whether 50% is enough.

You can configure it by overriding the `scale.minAvailable` parameter. (e.g. `90%`)

**Note:** PDBs are **only** deployed together with deployments that have more than one replica.
