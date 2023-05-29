# A quick demo to review persistent storage on OpenShift

## Stateless database

### Create the project
```
oc new-project install-storage
```

### Create the postgresql-no-persistent deployment
```
oc new-app --name postgresql-no-persistent \
    --image registry.redhat.io/rhel8/postgresql-12:1-43 \
    -e POSTGRESQL_USER=redhat \
    -e POSTGRESQL_PASSWORD=redhat123 \
    -e POSTGRESQL_DATABASE=persistentdb
```

### Take a look of the deployed resources
```
oc get all
```

### Take a look that there is not PV created at this time
```
oc get pv
```

### Init shell session inside the deployed pod
```
oc rsh $(oc get pods -o custom-columns=POD:.metadata.name --no-headers) bash
```

### Create and populate a table on the created postgresql POSTGRESQL_DATABASE
```
/usr/bin/psql -U redhat persistentdb

CREATE TABLE characters (
	    id SERIAL PRIMARY KEY,
	    name varchar(50),
	    nationality varchar(50)
);

INSERT INTO characters (name, nationality)
VALUES
    ('Wolfgang Amadeus Mozart', 'Prince-Archbishopric of Salzburg'),
    ('Ludwig van Beethoven', 'Bonn, Germany'),
    ('Johann Sebastian Bach', 'Eisenach, Germany'),
    ('José Pablo Moncayo', 'Guadalajara, México'),
    ('Niccolò Paganini', 'Genoa, Italy');

```

### Validate the table exist and we can query the populated data
```
persistentdb=> \dt
          List of relations
 Schema |    Name    | Type  | Owner  
--------+------------+-------+--------
 public | characters | table | redhat
(1 row)

persistentdb=> select id,name,nationality from characters;
 id |          name           |           nationality            
----+-------------------------+----------------------------------
  1 | Wolfgang Amadeus Mozart | Prince-Archbishopric of Salzburg
  2 | Ludwig van Beethoven    | Bonn, Germany
  3 | Johann Sebastian Bach   | Eisenach, Germany
  4 | José Pablo Moncayo     | Guadalajara, México
  5 | Niccolò Paganini       | Genoa, Italy
(5 rows)

persistentdb=>

```

### Delete the pod
```
oc delete pod $(oc get pods -o custom-columns=POD:.metadata.name --no-headers)
```

### Start shell session in the new pod
```
oc rsh $(oc get pods -o custom-columns=POD:.metadata.name --no-headers) bash
```

### Start session on the postgresql and run the same query
```
/usr/bin/psql -U redhat persistentdb

persistentdb=> \dt
Did not find any relations.
persistentdb=> select id,name,nationality from characters;
ERROR:  relation "characters" does not exist
LINE 1: select id,name,nationality from characters;
                                        ^
persistentdb=>

```

## Statefull database 


### Create the postgresql persistent deployment
```
oc new-app --name postgresql-persistent \
    --image registry.redhat.io/rhel8/postgresql-12:1-43 \
    -e POSTGRESQL_USER=redhat \
    -e POSTGRESQL_PASSWORD=redhat123 \
    -e POSTGRESQL_DATABASE=persistentdb
```

### Take a look on there is no PVs currently
```
oc get pv
```

### Create a PVC and volume for the postgresql-persistent deployment
```
oc set volumes deployment/postgresql-persistent \
    --add --name postgresql-storage --type pvc \
    --claim-mode rwo --claim-size 10Gi --mount-path /var/lib/pgsql \
    --claim-name postgresql-storage
```

**Note**
In case of of using an specific storageclass:
```
  oc set volumes deployment/postgresql-persistent \
    --add --name postgresql-storage --type pvc --claim-class nfs-storage \
    --claim-mode rwo --claim-size 10Gi --mount-path /var/lib/pgsql \
    --claim-name postgresql-storage
```

### List persistent volumes with custom columns
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

