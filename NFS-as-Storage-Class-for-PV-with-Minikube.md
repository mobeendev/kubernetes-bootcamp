
### Run on MiniKube Machine:


```
sudo apt install rpcbind nfs-common
```
sudo mount Server_IP_ADDRESS:/home/tom/nfs/storage/server/public  path/to/folder/on/minikube
```
sudo mount 172.16.56.148:/home/tom/nfs/storage/server/public /mnt```
```

```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mobeen-nfs-pv
  labels:
    name: mynfs 
spec:
  storageClassName: mobeen-nfs-storage-server # same storage class as pvc
  capacity:
    storage: 200Mi
  accessModes:
    - ReadWriteMany
  nfs:
    server: 172.16.56.148 # ip addres of nfs server
    path: "/home/tom/nfs/storage/server/public" # path to directory
```

```
kubectl apply -f mobeen_nfs_pv.yaml
kubectl get pvc,pv
```
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mobeen-nfs-pvc
spec:
  storageClassName: mobeen-nfs-storage-server
  accessModes:
    - ReadWriteMany #  must be the same as PersistentVolume
  resources:
    requests:
      storage: 50Mi
```

```
kubectl apply -f mobeen_nfs_pvc.yaml
kubectl get pvc,pv
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: mobeen-nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      volumes:
      - name: nfs-test
        persistentVolumeClaim:
          claimName: mobeen-nfs-pvc # same name of pvc that was created
      containers:
      - image: nginx
        name: nginx
        volumeMounts:
        - name: nfs-test # name of volume should match claimName volume
          mountPath: /usr/share/nginx/html # mount inside of contianer
```

```
kubectl apply -f mobeen_nginx_pod.yaml
kubectl get po
```

```
kubectl expose deploy mobeen-nginx --port 80 --type NodePort
kubectl get svc
```

```
minikube service mobeen-nginx --url
```

### Run inside  MiniKube NGINX POD:

```
kubectl exec -it mobeen_nginx-6cb55d48f7-q2bvd bash
sudo nano /usr/share/nginx/html/test.html
```
```
echo "welcome" > index.html
```

### Run on MiniKube Machine:
```
minikube ssh
docker@minikube:/mnt$ echo "welcome" > test.html
```







