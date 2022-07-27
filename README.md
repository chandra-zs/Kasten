# Kasten Backup On Same On Same Cluster

# Kasten


# create k3s cluster
```

curl -sfL https://get.k3s.io | VolumeSnapshotDataSource=true sh -s
```
# Install the VolumeSnapshot CRDS and the Snapshot Controller

```
SNAPSHOTTER_VERSION=master
```
# Apply VolumeSnapshot CRDs

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/${SNAPSHOTTER_VERSION}/client/config/crd/snapshot.storage.k8s.io_volumesnapshotclasses.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/${SNAPSHOTTER_VERSION}/client/config/crd/snapshot.storage.k8s.io_volumesnapshotcontents.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/${SNAPSHOTTER_VERSION}/client/config/crd/snapshot.storage.k8s.io_volumesnapshots.yaml
```
# Create Snapshot Controller
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/${SNAPSHOTTER_VERSION}/deploy/kubernetes/snapshot-controller/rbac-snapshot-controller.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/external-snapshotter/${SNAPSHOTTER_VERSION}/deploy/kubernetes/snapshot-controller/setup-snapshot-controller.yaml
```
# Install the CSI Hostpath Driver

```
git clone https://github.com/kubernetes-csi/csi-driver-host-path.git
cd csi-driver-host-path
./deploy/kubernetes-1.18/deploy.sh
```
# After the install is complete, add the CSI Hostpath Driver StorageClass and make it the default

```
kubectl apply -f ./examples/csi-storageclass.yaml
kubectl patch storageclass standard \
    -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}' { if u have local sc execute this other wise ignore]
kubectl patch storageclass csi-hostpath-sc \
    -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```
# Annotate the CSI Hostpath VolumeSnapshotClass for use with K10

```
kubectl annotate volumesnapshotclass csi-hostpath-snapclass \
    k10.kasten.io/is-snapshot-class=true  [if command not working, open the volumesnapshotclass object and add the annotation]
```
# Create namespace
```
k create ns kasten-io
```
# Install k10
```
helm install k10 kasten/k10 --namespace=kasten-io
```
# Deploy todo statefull app

```
https://github.com/debianmaster/go-to-do-app.git
cd go-to-do-app
cd deploy 
k apply -f k8s.yaml [ in this file replace storage class with your storage class]
```

# Backup Policy Screenshot

![image](https://user-images.githubusercontent.com/82602775/181231543-0a9da28c-c55b-4b40-8de0-2a21dd4ff386.png)

![image](https://user-images.githubusercontent.com/82602775/181232443-324c3094-8675-4d90-a5e5-70c890780788.png)

![image](https://user-images.githubusercontent.com/82602775/181232637-c484e168-1d10-4955-a67e-c49c9b190dc4.png)

![image](https://user-images.githubusercontent.com/82602775/181232671-96098a4f-c6cb-4e09-a0bb-cd18fa532edd.png)

# Restore Application
```
Go to application section and click on it. There You can find todo application click on restore. While restoring select namespace on which you want to restore.
```
![image](https://user-images.githubusercontent.com/82602775/181234513-a99a2244-3685-4729-bed2-06a26f8c4258.png)

![image](https://user-images.githubusercontent.com/82602775/181234647-37339d4c-288f-4f6c-b93b-d7fc38142a0e.png)

