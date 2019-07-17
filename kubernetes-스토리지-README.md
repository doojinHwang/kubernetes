## 쿠버네티스 스토리지

### 1. 볼륨
#### 종류  
-emptyDir  
-getRepo  
emptyDir기본 기능을 이용하여 초기에 Git리포지토리의 내용을 채워 제공하는 볼륨, 많이 사용 안함.  
-hostPath  
쿠버네티스 클러스터 노드(호스트)의 파일 시스템을 제공하는 볼륨  
다른 파드에서 접근 안됨, 그래서 테스트용으로 사용하지 실제로 사용안함  
단, pod별 log용으로 활용가능하다.  
-nfs: 공유 스토리지로, NFS볼륨은 많이 사용  
- 네트워크 스토리지 볼륨  
cephfs, cinder, fc, iscsi, flexVolume, focker, glusterfs, quobyte, rbd, vsphereVolume, scaleIO 등  
실습은 glusterfs으로...  

==> pod가 직접연결하는 스토리지 :emptyDir, getRepo, nfs,네트워크 스토리지 볼륨  
-awsElasticBlockStore, gcePersistentDisk, azureDis, azureFile등  
-persistentVolumeClaim  
정적/동적 프로비저님 볼륨  
주로 개발자 연결 스토리지  
(참고) 관리자는 PersistentVolume 리소스로 관리하고 개발자는 PersistentVolume에 연결(요청)하는 형태임.  
-configMap, secret:특수 유형의 볼륨  

### EmptyDir
임시저장 빈 볼륨  
파드를 지우면 같이 사라짐,  
메모리를 스로리지로 사용할 수 있음  
사이드카 패턴 구현  
-데이터 생성 컨테이너 + 데이터 손실 컨테이너 로 구현.  

### hostPath

### PersistentVolume 및 PersistentVolumeClaim


### 정적볼륨


### 동적볼륨





<hr/>


  kubectl get pv
No resources found.

code cccr-nfs-pv.yml 작성

  kubectl create -f cccr-hostpaths-pv.yml
persistentvolume/cccr-nfs-pv created

  kubectl get pv
NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
cccr-nfs-pv   5Gi        RWX            Retain           Available                                   27s

  kubectl get persistentvolume
NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
cccr-nfs-pv   5Gi        RWX            Retain           Available                                   39s

  kubectl create -f cccr-nfs-pvc.yml
persistentvolumeclaim/cccr-nfs-pvc created

   kubectl get pv,pvc
NAME                           CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                  STORAGECLASS   REASON   AGE
persistentvolume/cccr-nfs-pv   5Gi        RWX            Retain           Bound    default/cccr-nfs-pvc                           113s

NAME                                 STATUS   VOLUME        CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/cccr-nfs-pvc   Bound    cccr-nfs-pv   5Gi        RWX                           29s

  kubectl get persistentvolumeclaims
NAME           STATUS   VOLUME        CAPACITY   ACCESS MODES   STORAGECLASS   AGE
cccr-nfs-pvc   Bound    cccr-nfs-pv   5Gi        RWX                           87s
PS C:\education\02_kubernetes\work\03> kubectl get persistentvolume
NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                  STORAGECLASS   REASON   AGE
cccr-nfs-pv   5Gi        RWX            Retain           Bound    default/cccr-nfs-pvc                           2m58s


  ubectl create -f cccr-nfs-rs.yml
replicaset.apps/cccr-nfs created


  kubectl get pods
NAME                  READY   STATUS              RESTARTS   AGE
cccr-44pmh            1/1     Running             0          125m
cccr-77kcv            1/1     Running             0          125m
cccr-hostpath-47wqb   1/1     Running             0          63m
cccr-hostpath-mkd7h   1/1     Running             0          63m
cccr-jmsm2            1/1     Running             0          125m
cccr-liveness-pod     1/1     Running             1          23h
cccr-nfs-7trkh        0/1     ContainerCreating   0          8s
cccr-nfs-tk6vj        0/1     ContainerCreating   0          8s

  kubectl describe pv cccr-nfs
Name:            cccr-nfs-pv
Labels:          <none>
Annotations:     pv.kubernetes.io/bound-by-controller: yes
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:
Status:          Bound
Claim:           default/cccr-nfs-pvc
Reclaim Policy:  Retain
Access Modes:    RWX
VolumeMode:      Filesystem
Capacity:        5Gi
Node Affinity:   <none>
Message:
Source:
    Type:          HostPath (bare host directory volume)
    Path:          /web-contents
    HostPathType:  Directory
Events:            <none>

  kubectl describe persistentvolumeclaims cccr-nfs-pvc
Name:          cccr-nfs-pvc
Namespace:     default
StorageClass:
Status:        Bound
Volume:        cccr-nfs-pv
Labels:        <none>
Annotations:   pv.kubernetes.io/bind-completed: yes
               pv.kubernetes.io/bound-by-controller: yes
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      5Gi
Access Modes:  RWX
VolumeMode:    Filesystem
Mounted By:    cccr-nfs-7trkh
               cccr-nfs-tk6vj
Events:        <none>

  kubectl create -f cccr-nfs-svc.yml
service/cccr-nfs created

(테스트)
  kubectl run nettool -it --image=praqma/network-multitool --generator=run-pod/v1 --rm=true bash
If you don't see a command prompt, try pressing enter.
bash-5.0# curl http://cccr-nfs


  kubectl delete replicasets.apps cccr-nfs

  kubectl delete persistentvolumeclaims cccr-nfs-pvc
persistentvolumeclaim "cccr-nfs-pvc" deleted

  kubectl create -f cccr-nfs-rs.yml
replicaset.apps/cccr-nfs created

  kubectl get sc
NAME                 PROVISIONER                AGE
standard (default)   k8s.io/minikube-hostpath   2d4h

  kubectl describe sc standard
Name:                  standard
IsDefaultClass:        Yes
Annotations:           storageclass.kubernetes.io/is-default-class=true
Provisioner:           k8s.io/minikube-hostpath
Parameters:            <none>
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>

  minikube addons list
- addon-manager: enabled
- dashboard: disabled
- default-storageclass: enabled
- efk: disabled
- freshpod: disabled
- gvisor: disabled
- heapster: disabled
- ingress: enabled
- logviewer: disabled
- metrics-server: disabled
- nvidia-driver-installer: disabled
- nvidia-gpu-device-plugin: disabled
- registry: disabled
- registry-creds: disabled
- storage-provisioner: enabled
- storage-provisioner-gluster: disabled

  kubectl get storageclass
NAME                 PROVISIONER                AGE
standard (default)   k8s.io/minikube-hostpath   2d4h

  minikube addons enable storage-provisioner-gluster
* storage-provisioner-gluster was successfully enabled

  minikube addons list
- addon-manager: enabled
- dashboard: disabled
- default-storageclass: enabled
- efk: disabled
- freshpod: disabled
- gvisor: disabled
- heapster: disabled
- ingress: enabled
- logviewer: disabled
- metrics-server: disabled
- nvidia-driver-installer: disabled
- nvidia-gpu-device-plugin: disabled
- registry: disabled
- registry-creds: disabled
- storage-provisioner: enabled
- storage-provisioner-gluster: enabled

  kubectl get ns
NAME                STATUS   AGE
default             Active   2d4h
development         Active   24h
kube-node-lease     Active   2d4h
kube-public         Active   2d4h
kube-system         Active   2d4h
quality-assurance   Active   24h



  kubectl get sc
NAME                    PROVISIONER                AGE
glusterfile (default)   gluster.org/glusterfile    56s
standard (default)      k8s.io/minikube-hostpath   2d4h

  kubectl delete sc standard
storageclass.storage.k8s.io "standard" deleted

  kubectl get sc
NAME                    PROVISIONER               AGE
glusterfile (default)   gluster.org/glusterfile   72s

  kubectl describe sc glusterfile
Name:                  glusterfile
IsDefaultClass:        Yes
Annotations:           storageclass.beta.kubernetes.io/is-default-class=true
Provisioner:           gluster.org/glusterfile
Parameters:            resturl=http://heketi.storage-gluster.svc.cluster.local:8080,restuser=admin
AllowVolumeExpansion:  <unset>
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>

  kubectl get sc
NAME                    PROVISIONER                AGE
glusterfile (default)   gluster.org/glusterfile    3m27s
standard (default)      k8s.io/minikube-hostpath   87s

  kubectl edit sc standard
storageclass.storage.k8s.io/standard edited

  kubectl get sc
NAME                    PROVISIONER                AGE
glusterfile (default)   gluster.org/glusterfile    5m2s
standard                k8s.io/minikube-hostpath   3m2s


  kubectl delete sc standard
storageclass.storage.k8s.io "standard" deleted

  kubectl get sc
NAME                    PROVISIONER               AGE
glusterfile (default)   gluster.org/glusterfile   5m19s

  kubectl create -f cccr-dynamic-pvc.yml
persistentvolumeclaim/cccr-dynamic-pvc created

  kubectl get pvc,pv
NAME                                     STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/cccr-dynamic-pvc   Bound    pvc-a4606964-0e8f-4b41-933d-3793b50b47bd   100Mi      RWX            glusterfile    15s

NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                      STORAGECLASS   REASON   AGE
persistentvolume/cccr-nfs-pv                                5Gi        RWX            Retain           Released   default/cccr-nfs-pvc                               20m
persistentvolume/pvc-a4606964-0e8f-4b41-933d-3793b50b47bd   100Mi      RWX            Delete           Bound      default/cccr-dynamic-pvc   glusterfile             12s


  kubectl delete pvc cccr-dynamic-pvc
persistentvolumeclaim "cccr-dynamic-pvc" deleted

  kubectl get pvc,pv

NAME                           CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS     CLAIM                  STORAGECLASS   REASON   AGE
persistentvolume/cccr-nfs-pv   5Gi        RWX            Retain           Released   default/cccr-nfs-pvc                           21m


  kubectl get po
NAME                  READY   STATUS    RESTARTS   AGE
cccr-44pmh            1/1     Running   0          148m
cccr-77kcv            1/1     Running   0          148m
cccr-hostpath-47wqb   1/1     Running   0          86m
cccr-hostpath-mkd7h   1/1     Running   0          86m
cccr-jmsm2            1/1     Running   0          148m
cccr-liveness-pod     1/1     Running   1          24h
cccr-nfs-8x2tq        0/1     Pending   0          15m
cccr-nfs-cr9ff        0/1     Pending   0          15m


