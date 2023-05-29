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

Login as cluster-mgr.
```
 oc login -u cluster-mgr https://api.cluster.xxxxx.sandbox2051.opentlc.com:6443
```

Verify OpenShift version
```
oc version
```

The velero CLI we have setup for you is an alias to a binary within the velero deployment on the cluster.
```
alias velero='oc -n openshift-adp exec deployment/velero -c velero -it -- ./velero'
```

Verify Velero Version
```
velero version
```

Verify OADP resources are ready
```
oc get deployments -n openshift-adp
```

Verify OADP Version
```
oc get clusterserviceversion -n openshift-adp
```

Verify DPA (DataProtectionApplication)
```
oc get dpa -n openshift-adp
oc describe dpa/example-dpa -n openshift-adp
```

Verify OADP secret
```
oc get secret/cloud-credentials  -n openshift-adp 
```

## Backing up single namespace

Firts Deploy a statefull application.
> **Note: Can use persistent-storage in this repo**

Let’s go ahead and create a backup of your application namespace.
```
velero backup create backup01 --include-namespaces install-storage --default-volumes-to-restic 
```

You can check on the backup progress by running the following
```
velero backup describe backup01
```

Viewing backup content in S3 storage
```
awsocs s3 ls migstorage/velero/backups/mssql-backup/
```

Simulate a disaster.
```
oc delete project install-storage
```

Restoring deleted application.
```
velero restore create restore01 --from-backup backup01
```

Check when the restoe is complete.
```
velero restore describe restore01
```

