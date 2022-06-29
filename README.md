## training plan 

<img src="cna.png">

### Revision 

<img src="rev.png">

### intro to Ingress controller in k8s 

<img src="ingress.png">

### cleaning up namespace 

```
 kubectl  delete all --all
pod "ashuwebapp-57c64f7f8-bsqxh" deleted
pod "ashuwebapp-57c64f7f8-gg8nb" deleted
service "ashulb1" deleted
service "ashulb2" deleted
deployment.apps "ashuwebapp" deleted
[root@client ~]# kubectl  get deploy 
No resources found in ashu-project namespace.
```

### understanding ingress controller 

<img src="ingressc.png">

## Testing Ingress -- 

### creating sample deployment 

```
 kubectl  create deployment  ashuapp --image=dockerashu/cat:v1 --port 80  --dry-run=client  -o yaml  >ashucat.yaml
```

### some changes 

```
[root@client ashuapp]# cat  ashucat.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashuapp
  name: ashuapp
  namespace: ashu-project # namespace info 
spec:
  replicas: 2 # number of pods 
  selector:
    matchLabels:
      app: ashuapp
  strategy: {}
  template: # for pod creation 
    metadata:
      creationTimestamp: null
      labels:
        app: ashuapp
    spec:
      containers:
      - image: dockerashu/cat:v1
        name: cat
        ports:
        - containerPort: 80
        resources: # resource restrication of each pod 
          requests:
            cpu: 100m # CNA -- 1vcpu == 1000 milicore 
            memory: 200M
          limits:
            cpu: 200m
            memory: 400M 
status: {}

```

