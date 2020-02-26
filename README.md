# kubernetes_configmap

```

Created a simple configmap :-
---------------------------------------------------
kmaster@kmaster:~/Linux_class/configmap$ cat myconfigmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myconfigmap
data:
  fname: upinder
  lname: sujlana
  favos: ubuntu
  snack: momos
kmaster@kmaster:~/Linux_class/configmap$
kmaster@kmaster:~/Linux_class/configmap$ kubectl create -f myconfigmap.yaml
configmap/myconfigmap created
kmaster@kmaster:~/Linux_class/configmap$
kmaster@kmaster:~/Linux_class/configmap$ kubectl describe configmap/myconfigmap
Name:         myconfigmap
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
fname:
----
upinder
lname:
----
sujlana
snack:
----
momos
favos:
----
ubuntu
Events:  <none>
kmaster@kmaster:~/Linux_class/configmap$

Going to create a testpod and use the configmap using the configMapKeyRef
---------------------------------------------------------------------------
kmaster@kmaster:~/Linux_class/configmap$ cat locationConfigMapdemo.yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: null
  generation: 1
  labels:
    type: location
  name: locationdeployment
  selfLink: /apis/extensions/v1beta1/namespaces/default/deployments/locationdeployment
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      type: location
  strategy:
    type: Recreate
  template:
    metadata:
      creationTimestamp: null
      labels:
        type: location
    spec:
      containers:
      - image: upigrad82/location
        imagePullPolicy: Always
        name: location
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        env:
          - name: FNAME_ENV_VAR
            valueFrom:
              configMapKeyRef:
                name: myconfigmap
                key: fname
          - name: SNACK_ENV_VAR
            valueFrom:
              configMapKeyRef:
                name: myconfigmap
                key: snack
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status: {}
kmaster@kmaster:~/Linux_class/configmap$
kmaster@kmaster:~/Linux_class/configmap$ kubectl create -f locationConfigMapdemo.yaml
deployment.extensions/locationdeployment created
kmaster@kmaster:~/Linux_class/configmap$ 
kmaster@kmaster:~/Linux_class/configmap$ kubectl get pods
NAME                                  READY   STATUS    RESTARTS   AGE
locationdeployment-5f5b6db444-qkdcj   1/1     Running   0          66s
kmaster@kmaster:~/Linux_class/configmap$

Investigating the enviromental variables inside the pod to confirm
------------------------------------------------------------------------------------------------------
kmaster@kmaster:~/Linux_class/configmap$ kubectl exec -it locationdeployment-5f5b6db444-qkdcj bash
root@locationdeployment-5f5b6db444-qkdcj:/app#
root@locationdeployment-5f5b6db444-qkdcj:/app#
root@locationdeployment-5f5b6db444-qkdcj:/app# env | grep ENV
SNACK_ENV_VAR=momos
FNAME_ENV_VAR=upinder
root@locationdeployment-5f5b6db444-qkdcj:/app#

```
