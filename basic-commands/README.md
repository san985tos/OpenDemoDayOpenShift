# Basic OpenShift Commands

### Accessing the OpenShift Console
```
oc login -h
oc whoami
oc whoami --show-console
oc whoami -t 
oc whoami --help
```

### Verifying the Health of OpenShift Nodes
```
oc get nodes
oc adm top nodes
oc describe node [node_name]
```

### Reviewing the Cluster Version Resource
```
oc get clusterversion
oc describe clusteroperators
```

### Displaying the logs of OpenShift Nodes
```
oc adm node-logs -u crio [node_name]
oc adm node-logs -u kubelet [node-name]
oc adm node-logs [node_name] … #Journal logs
```

### Opening a shell prompt on OpenShift Nodes
```
oc debug node/[node_name]
sh-4.2# chroot /host
sh-4.2# systemctl is-active kubelet
sh-4.2# crictl ps
```

### Troubleshooting terminated and running pods
```
oc logs [pod-name]
oc logs [pod-name] -c [container-name]
oc logs --tail 20 [pod-name]
oc get status
oc get events
```

### Troubleshooting OpenShift CLI
```
oc get pod --loglevel 6 
oc get pod --loglevel 10
oc whoami 
```