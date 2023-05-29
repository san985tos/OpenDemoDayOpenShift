# OpenShift API for Data Protection (OADP)

The OpenShift API for Data Protection (OADP) is an Operator that helps you define and configure installation of Velero and its required resources to run on OpenShift.

You can download and install the Velero CLI tool by following the instructions on the [Velero documentation page](https://velero.io/docs/v1.9/basic-install/#install-the-cli).

You must have the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) installed.

Install OADP operator through OpenShift's Operator Hub.

> **Note: Better use the OpenShift APIs For Data Protection lab**

## Prapare
We have also setup credentials for aws cli to be used with openshift storage. We will be using alias awsocs to interact with the openshift storage endpoint. See what the awsocs alias is set to.
```
alias awsocs='aws --endpoint-url http://s3.openshift-storage.svc'
```

