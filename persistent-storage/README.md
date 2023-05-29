# A quick demo to review persistent storage on OpenShift

## Create the project
```
oc new-project install-storage
```

## Create the postgresql-no-persistent deployment
```
oc new-app --name postgresql-no-persistent \
    --image registry.redhat.io/rhel8/postgresql-12:1-43 \
    -e POSTGRESQL_USER=redhat \
    -e POSTGRESQL_PASSWORD=redhat123 \
    -e POSTGRESQL_DATABASE=persistentdb
```

## Take a look of the deployed resources
```
oc get all
```

## Take a look that there is not PV created at this time
```
oc get pv
```






## Take a look on the PV on the system
```
oc get pv
oc new-project install-storage
```



## Create a PVC and volume for the postgresql-persistent deployment
```
oc set volumes deployment/postgresql-persistent \
    --add --name postgresql-storage --type pvc \
    --claim-mode rwo --claim-size 10Gi --mount-path /var/lib/pgsql \
    --claim-name postgresql-storage
```

  in case of storageclass:

```
  oc set volumes deployment/postgresql-persistent \
    --add --name postgresql-storage --type pvc --claim-class nfs-storage \
    --claim-mode rwo --claim-size 10Gi --mount-path /var/lib/pgsql \
    --claim-name postgresql-storage
```

## List persistent volumes with custom columns
```
oc get pv -o custom-columns=NAME:.metadata.name,CLAIM:.spec.claimRef.name
```

## Insert data into DB.
```
./init_data.sh
```

## Check data
```
./check_data.sh
```

## Delete all related application 
```
oc delete all -l app=postgresql-persistent
```

## Create the postgresql-persistent2 deployment
```
oc new-app --name postgresql-persistent2 \
    --docker-image registry.redhat.io/rhel8/postgresql-12:1-43 \
    -e POSTGRESQL_USER=redhat \
    -e POSTGRESQL_PASSWORD=redhat123 \
    -e POSTGRESQL_DATABASE=persistentdb
```

## Attach the existing PVC to the postgresql-persistent2 deployment
```
oc set volumes \
  deployment/postgresql-persistent2 \
  --add --name postgresql-storage --type pvc \
  --claim-name postgresql-storage --mount-path /var/lib/pgsql
``` 

## Check data
```
./check_data.sh
```

## Clean up:
```
oc delete project install-storage
```

