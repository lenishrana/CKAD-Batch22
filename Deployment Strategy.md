## Deployment Strategy


### Task 1: Rolling Update in Kubernetes 

```
vi dep.yaml 
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: dep1
  name: dep1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: dep1
  strategy: {}
  template:
    metadata:
      labels:
        app: dep1
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
```

Note the strategy.Since nothing is given the default strategy comes into place which is the rolling update.

Apply the yaml file
```
kubectl apply -f dep.yaml
```
```
kubectl get deployments.apps,pod,rs
```
Note the Statgey and events by describing the deployment
```
kubectl describe deployments.apps dep1
```
To increase/decrease the number of replicas manually, either edit the yaml file, or edit the running deployment or execute the below command
```
kubectl scale deployment dep1 --replicas 5
```
For Autoscaling
```
kubectl get hpa
```
```
kubectl autoscale deployment dep1 --min 4 --max 12 --cpu-percent 70 --name hpa-dep1
```
```
kubectl get hpa
```
To upgrade/migrate the version of image, either edit the yaml file, or edit the deployment or execute the below command
Add a watch on the pods in a new tab
```
kubectl get po -w
```
Set a new image for the deployment
```
kubectl set image deploy dep1 nginx=nginx:latest --record
```
Check how the pods are getting deleted and recreated. 

Cross check if the image has been updated by executing the below command
```
kubectl describe deployments.apps dep1
```
To check the rollout history. The below command shows history of 10 versions.
```
kubectl rollout history deploy dep1
```
To Rollback
```
kubectl rollout undo deploy dep1 --to-revision 1
```
To check the history of a particular revision
```
kubectl rollout history deploy dep1 --revision=<revision-number>
```

### Task 2: Recreate Strategy in Kubernetes 
```
vi recreate.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: dep2
  name: dep2
spec:
  replicas: 2
  selector:
    matchLabels:
      app: dep2
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: dep2
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
```
```
kubectl apply -f recreate.yaml
```
Add a watch on the pods in a new tab
```
kubectl get po -w
```
Set a new image for the deployment
```
kubectl set image deploy dep2 nginx=nginx:latest --record
```
Check how the pods are getting deleted and recreated. 

Cross check if the image has been updated by executing the below command
```
kubectl describe deployments.apps dep2
```

