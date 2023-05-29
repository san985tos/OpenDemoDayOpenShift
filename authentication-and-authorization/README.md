# A quick demo to review Authentication and Authorization on OpenShift

### Check OAUTH Operator
```
oc get oauth cluster -o yaml > oauth.yaml
```

### Extraer los usuarios del secreto:
```
oc extract secret/htpasswd-secret -n openshift-config —to .  -confirm 
```

### Take a look of the file
```
cat htpasswd
```

### Add a new user and password with htpasswd tool
```
htpasswd -b htpasswd user_name user_password
```

#### Optional if many users to be created
```
for i in {1..100}; do htpasswd -b htpasswd user_name$i user_password; done
```

### Update the secret on OpenShift
```
oc set data secret/htpasswd-secret --from-file htpasswd=./htpasswd -n openshift-config
```

### Check Auth pods
```
watch oc get pods -n openshift-authentication 
```

### Optional Create user at OS level
```
useradd user_name -d /home/user_name
echo "password" | passwd user_name --stdin
chmod -R 777 /home
```

#### Optional if many users need to be created at OS
```
for i in $(cat htpasswd | awk -F':' '{print $1}')
> do
> useradd $i -d /home/$i; echo user_password | passwd $i  --stdin

chmod -R 777 /home
```

### Create users in OpenShift
```
oc create user user_name
```

#### Optional create many users in OpenShift
```
for i in $(cat htpasswd | awk -F':' '{print $1}'); do oc create user $i ; done
```

### Create user groups 
```
oc adm groups new workshop
```

### Cluster-admin permissions to one of the groups
```
oc adm policy add-cluster-role-to-group cluster-admin workshop
```

### Remove permissions to the group
```
oc adm policy remove-cluster-role-from-group cluster-admin workshop
```

### Add users to the group
```
oc adm groups add-users workshop user_name
```

#### Optional if many users to be added into a group 
```
for i in $(cat htpasswd | awk -F':' '{print $1}'); do oc adm groups add-users workshop $i ; done
```
