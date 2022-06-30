## training plan 

<img src="cna.png">

## Stateless vs statefulsets 

<img src="state.png">

### strategy 

<img src="stra.png">

### getting password from argocd 

```
[root@client ~]# kubectl  get secret  -n argocd 
NAME                                        TYPE     DATA   AGE
argocd-initial-admin-secret                 Opaque   1      26h
argocd-notifications-secret                 Opaque   0      26h
argocd-secret                               Opaque   5      26h
cluster-kubernetes.default.svc-3396314289   Opaque   3      17h
repo-1629727592                             Opaque   3      17h
[root@client ~]# kubectl  get secret argocd-initial-admin-secret -o yaml  -n argocd 
apiVersion: v1
data:
  password: dTNhMTRpMXd1anJraVlJQw==
kind: Secret
metadata:
  creationTimestamp: "2022-06-29T02:20:04Z"
  name: argocd-initial-admin-secret
  namespace: argocd
  resourceVersion: "34657"
  uid: a4e2dd13-0556-4992-ab8f-47faf535c8ea
type: Opaque
[root@client ~]# 
[root@client ~]# 
[root@client ~]# echo "dTNhMTRpMXd1anJraVlJQw=="   |  base64 -d
u3a14i1wujrkiYIC[root@client ~]# 


```


## ARgoCD for wordpress application 

### db 
```
kubectl  create  deployment  ashudb --image=mysql:5.7  --port 3306 --namespace=ashu-project    --dry-run=client -o yaml >db_deploy.yaml
```

### configMAP 

```
kubectl  create  configmap  ashudb-config  --from-literal  MYSQL_DATABASE=ashudb  --namespace=ashu-project  --dry-run=client -o yaml  >db_config.yaml
```

###

```
[root@client webapp_db]# cat db_config.yaml 
apiVersion: v1
data:
  MYSQL_DATABASE: ashudb
  MYSQL_USER: ashuuser
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: ashudb-config
  namespace: ashu-project
```

### password stored in secret 

```
kubectl  create  secret  generic  ashudb-sec  --from-literal db_pass="Cisco123456#" --from-literal      root_pass="Root@123456#"  --namespace=ashu-project  --dry-run=client -o yaml  >db_secret.yaml
```

### final YAML of deployment 

```
[root@client webapp_db]# cat db_secret.yaml 
apiVersion: v1
data:
  db_pass: Q2lzY28xMjM0NTYj
  root_pass: Um9vdEAxMjM0NTYj
kind: Secret
metadata:
  creationTimestamp: null
  name: ashudb-sec
  namespace: ashu-project
[root@client webapp_db]# vim db_deploy.yaml 
[root@client webapp_db]# cat db_deploy.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: ashudb
  name: ashudb
  namespace: ashu-project
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ashudb
  strategy: {}
  template:  # pod template 
    metadata:
      creationTimestamp: null
      labels:
        app: ashudb
    spec:
      volumes: # to create volume
      - name: ashudbvol
        hostPath: # pod will take space from Minion node 
         path: /data/ashudb
         type: DirectoryOrCreate 
      containers:
      - image: mysql:5.7
        name: mysql
        ports:
        - containerPort: 3306
        envFrom: # to call env variable from configMAP 
        - configMapRef:
           name: ashudb-config # name of configMAP 
        env: # calling other env with secret 
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: ashudb-sec
              key: db_pass
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: ashudb-sec
              key: root_pass
        resources: {}
        volumeMounts: # to mount the volume created above
        - name: ashudbvol
          mountPath: /var/lib/mysql/
status: {}

```




