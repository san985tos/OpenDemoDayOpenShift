# A quick demo to review Authentication and Authorization on OpenShift

## Check OAUTH Operator
```
oc get oauth cluster -o yaml > oauth.yaml
```

## Extraer los usuarios del secreto:
```

```
# oc extract secret/htpasswd-secret -n openshift-config —to .  -confirm 





Crear users en el secreto

$ for i in {1..100}; do htpasswd -b htpasswd user$i redhat1234; done



Actualizar el secreto

$ oc set data secret/htpasswd-secret --from-file htpasswd=./htpasswd -n openshift-config





Check los pods

$ watch oc get pods -n openshift-authentication 





Crear usuarios a nivel sistema operativo:



# for i in $(cat htpasswd | awk -F':' '{print $1}')



> do

> useradd $i -d /home/$i; echo openshift | passwd $i  --stdin





# sudo chmod -R 777 /home







Crear usuarios en openshift:



# for i in $(cat htpasswd | awk -F':' '{print $1}'); do oc create user $i ; done





Crear grupo:



# oc adm groups new workshop









Cluster-admin permisos al grupo



# oc adm policy add-cluster-role-to-group cluster-admin workshop

# oc adm policy remove-cluster-role-from-group cluster-admin workshop



Agregar usuarios al grupo:



# for i in $(cat htpasswd | awk -F':' '{print $1}'); do oc adm groups add-users workshop $i ; done











Verify the OpenShift cluster network configuration.

$ oc get network.config/cluster -o yaml





Verify the etcd storage performance.

$ oc get pods -n openshift-etcd | grep etcd-ip



$ oc rsh -n openshift-etcd  etcd-ip-10-0-131-99.ec2.internal

# etcdctl check perf --load="s"







When installing OpenShift on AWS using the full-stack automation method, the OpenShift installer creates and configures a MachineSet for each availability zone in the selected region.

$ oc get machines -n openshift-machine-api





$  oc get machinesets -n openshift-machine-api



$ oc get nodes --label-columns failure-domain.beta.kubernetes.io/region,failure-domain.beta.kubernetes.io/zone









OpenShift Node Data

In some scenarios, Red Hat Support will ask you to collect a sosreport file from a specific OpenShift cluster node.

The sosreport command is a tool that collects configuration details, system information, and diagnostic data from Red Hat Enterprise Linux (RHEL) and Red Hat Enterprise  Linux CoreOS (RHCOS) systems.

Red Hat recommends using a debug pod to generate a sosreport from an OpenShift cluster n







$ oc debug node/ip-10-0-151-177.us-east-2.compute.internal



	# sosreport -k crio.all=on -k crio.logs=on



	# exit

sh-4.4# exit

sh-4.4# ls -lrt \

> /host/var/tmp/sosreport-ip-10-0-151-177-1234-2021-01-11-crlsmlr.tar.xz

-rw-------. 1 root root 32578896 Jan 11 13:42 /host/var/tmp/sosreport-ip-10-0-151-177-1234-2021-01-11-crlsmlr.tar.xz









Para registrar un cluster a You can access your cluster information using the Red Hat OpenShift Cluster Manager Console.





$ oc get clusterversion  -o jsonpath='{.items[].spec.clusterID}{"\n"}'









OCP 4.6 workshop Telcel - sesión 2

Thursday, April 29 · 9:00 – 11:00am

Google Meet joining info

Video call link: https://meet.google.com/wyx-jsiu-qqe

Or dial: (MX) +52 222 382 6000 PIN: 973 086 455 5920#

More phone numbers: https://tel.meet/wyx-jsiu-qqe?pin=9730864555920

Or join via SIP: sip:9730864555920@gmeet.redhat.com





























Demo Pipeline



monolith/src/main/webapp/app/css/coolstore.css



.navbar-header {

    background: blue

}



mvn clean package -Popenshift -DskipTests -f $CHE_PROJECTS_ROOT/cloud-native-workshop-v2m2-labs/monolith



oc start-build -n user1-coolstore-dev coolstore --from-file=$CHE_PROJECTS_ROOT/cloud-native-workshop-v2m2-labs/monolith/deployments/ROOT.war --follow


