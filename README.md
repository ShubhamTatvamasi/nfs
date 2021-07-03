# nfs

Install the following package on node:
```bash
dpkg -l nfs-common || sudo apt install -y nfs-common
```

Add helm stable repo:
```bash
helm repo add stable https://charts.helm.sh/stable/
helm repo update
```

Setup a Persistent Volume:
```bash
kubectl apply -f - << EOF
apiVersion: v1
kind: PersistentVolume
metadata:
  name: data-nfs-server-provisioner-0
spec:
  storageClassName: manual
  capacity:
    storage: 80Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /var/data/nfs
EOF
```


Install NFS Server Provisioner:
```bash
helm install nfs-server-provisioner stable/nfs-server-provisioner \
  --create-namespace \
  --namespace nfs-server-provisioner \
  --set image.tag=v2.3.0 \
  --set persistence.size=80Gi \
  --set persistence.enabled=true \
  --set persistence.storageClass=manual \
  --set storageClass.defaultClass=true
```
---

### Test PVC Setup

setup pvc and pod:
```bash
curl -sL https://k8s.io/examples/pods/storage/pv-claim.yaml | sed 's/manual/nfs/' | kubectl apply -f -

kubectl apply -f https://k8s.io/examples/pods/storage/pv-pod.yaml
```

test pvc with pod:
```bash
kubectl exec task-pv-pod -- bash -c "echo 'Hello World' > /usr/share/nginx/html/index.html"

kubectl exec task-pv-pod -- cat /usr/share/nginx/html/index.html
```

Delete the pod and recreate it again: 
```bash
kubectl delete po/task-pv-pod
```

Once checked that file still exist delete everything:
```bash
kubectl delete po/task-pv-pod pvc/task-pv-claim
```
