# Terraform OpenShift Container Platform Module

Builds OpenShift reference archtecture on AWS.

It supports OCP and Origin.

## Prerequisites

If you are building a OCP cluster,

* you need to know a subscription pool id for OCP.
* you need to get access of Gold Images of Red Hat Atomic Host through the Red Hat Cloud Access program. See also https://access.redhat.com/articles/2962171

## Creates a cluster

You can set-up a cluster using [the example origin platform](/example/origin/) or [the example OCP platform](/example/ocp/) config.

### Selects Origin or OCP

If you are building a Origin cluster, use [the example origin platform](/example/origin/) config.

For this, set `CLUSTER_CONFIG` environment variable to `origin`

```bash
export CLUSTER_CONFIG=origin
```

If you are building a OCP cluster, use [the example OCP platform](/example/ocp/) config.

For this, set `CLUSTER_CONFIG` environment variable to `ocp`

```bash
export CLUSTER_CONFIG=ocp
```

### Builds infrastructure on AWS for installing OpenShift.

Use make command and input values requested in the interaction.

```bash
make init CLUSTER_CONFIG=ocp
make CLUSTER_CONFIG=ocp
```

You will be asked some parameters for configuring the cluster infrastructure. See also [direnv](#direnv).

### Public DNS setting

If you want to use your named domain, you need to set up the domain names for install and access the cluster.
If not, xx.xx.xx.xx.nip.io address is used.

`make dns-nameservers`

```
 ns-xxx.awsdns-xx.org,
 ns-xxx.awsdns-xx.co.uk,
 ns-xxx.awsdns-xx.com,
 ns-xxx.awsdns-xx.net
```

You can set the name servers in a NS record of your name server for delegation.

See also https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/CreatingNewSubdomain.html

### Install OpenShift on the infrastructure

Once the DNS setting is active, you can install OpenShift using the command:

```
make install CLUSTER_CONFIG=ocp
```

After that, you can access the master of the cluster via URL provided as output of the `make install` command.

### Limitations

At plan phase, you will find the below diff, but you can apply them without any changes.

```
~ module.openshift_platform.module.domain.aws_route53_record.master_public
    records.#:                   "" => <computed>

~ module.openshift_platform.module.domain.aws_route53_record.router_public
    records.#:                   "" => <computed>
```

## direnv
Instead of inputing values in interaction, you can use [direnv](https://github.com/direnv/direnv) and .envrc file for providing configuration.

```.envrc
# AWS access
export AWS_DEFAULT_REGION=ap-northeast-1
export AWS_PROFILE=xxxx

# AMI ID(Gold Image ID / CentOS Image ID)
export TF_VAR_image_id=ami-8e8847f1

# Node Spec

export TF_VAR_bastion_instance_type=m4.large
export TF_VAR_master_instance_type=m4.large
export TF_VAR_compute_node_instance_type=m4.large

export TF_VAR_compute_node_count=2

# If you want to use spot instances for saving cost, specify spot price.
export TF_VAR_bastion_spot_price=0.1
export TF_VAR_master_spot_price=0.1
export TF_VAR_compute_node_spot_price=0.1

# The name of the cluster that is used for tagging some resources
export TF_VAR_platform_name=sample-platform

# Red Hat Network credential for registration system of the OpenShift Container Platform cluster
export TF_VAR_rhn_username="xxx@example.com"
export TF_VAR_rhn_password="xxxxxxxxxxx"

# Red Hat subscription pool id for OpenShift Container Platform
export TF_VAR_rh_subscription_pool_id="xxxxxxxx"

# Public DNS subdomain for access to services served in the cluster
# If it isn't set, a nip address is used.
export TF_VAR_platform_domain=sample-platform.example.com
# Email used to register Let's encrypt account.
export TF_VAR_platform_domain_administrator_email=administrator@example.com

```

## Tips

### Open OpenShift Web Console

```bash
open `make master-url`
```

### SSH to Bastion

```
make ssh
```
