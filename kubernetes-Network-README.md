## 네트워크
쿠버네티스를 설치하면 Calico 네트워크가 설치 됨(https://docs.projectcalico.org/v2.0/getting-started/kubernetes/)

(1) 서비스  
(2) 인그레스  
(3) 레디니스 프로브  
(4) 헤드리스 서비스  

<hr/>



## (1) 서비스
쿠버네티스의 Pod와 Service의 네트워크 정보
  kubectl cluster-info dump | findstr cluster-ip
  "--service-cluster-ip-range=10.96.0.0/12",
  
### 생성방법
1) command 명령을 이용하는 방법
kubectl expose <CONTROLLER_TYPE> <REOURSE> [--type=SVC_TYPE] --name <NAME>
2) yaml방식이 있음.
cccr-svg.yml
  apiVersion: v1
  kind: Service
  metadata:
    name: cccr
  spec:
    ports:
    - port: 80             #서비스 포트
      targetPort: 8080     #파드 포트
    selector:
      app: cccr            #파드 라벨셀렉터(서비스에 연결할 파드를 라벨셀렉터)
      

#### 서비스 확인
(참고) svc : services
  kubectl get svc 
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   46h

#### 엔드포인트 확인
서비스와 연결된 파드 확인
  kubectl get endpoints
NAME         ENDPOINTS             AGE
kubernetes   192.168.99.100:8443   46h

위 서비스와 엔드포인트확인 내용은 서비스 확인 결과 10.96.0.1 ip 443port로 접속시 192.168.99.100ip의 8443으로 연결된다는 의미
kubectl get all     #endpoints정보는 나오질 않음
kubectl get all .ep #endpoint정보 나옴
kubectl get po.ep -o wide #파드 엔드포인트 정보 확인

### 파드 생성 및 엔트포인트 연결
  kubectl create -f cccr-replicaset.yml
replicaset.apps/cccr created

엔드포인트 다시 확인
  kubectl get endpoints cccr
  
파드의 목록과 비료
kubectl get pods -o wide

### 서비스 접근 테스트
  kubectl run nettool -it --image=praqma/network-multitool --generator=run-pod/v1 --rm=true bash
If you don't see a command prompt, try pressing enter.
bash-5.0#

1초마다 서비스 혹인
  bash-5.0# watch -n1 -d http://10.96.138.178
 
### 서비스 세션 친화성 구성
 yml 작성시 spec sessionAffinity 지시어: [None | ClientIP] 설정으로 동일 파드로 연결제어 가능
 
### 서비스 다중포트 구성
spec 지시어 ports를 리스트로 구성
  spec:
    ports:
    - name: cccr-http
      port: 80
      targetPort: 8080
    ports:
    - name: cccr-https
      port: 443
      targetPort: 8443

### 포트 이름 참조
파드와 파드를 생성하는 컨트롤러의 파드 템플릿의 컨테이너 포트에도 이름을 부여할 수 있다.

#### 포트 이름을 사용한 레플리카셋 및 서비스 생성


spec: 지시어내 list 목록 내용중 - name: 이름지정  
(파드 및 컨트롤러에서 컨테이너의 포트에 이름을 부여하는 구성)  
cccr-named-port-replicaset.yml
  apiVersion: apps/v1beta2
  kind: ReplicaSet
  metadata:
    name: cccr
  spec:
    replicas: 3
      matchLabels:
        app: cccr
    template:
     metadata:
       labels:
         app: cccr
    spec:
      containers:
      - name: cccr
        image: ons/cccr
        ports:
        - name: cccr-http <== 이름지정(기존:8080)
          containerPort: 8080
=>기존 컨테이너의 8080포트에 cccr-http라는 이름을 부여함  

(다름은 포트 이름을 참조하는 서비스 구성)  
ccc-named-port-svc.yml  
  apiVersion: v1
  kind: Service
  metadata:
    name: cccr
  spec:
    ports:
    - name: cccr-http
      port: 80
      targetPort: ccc-http <==port이름으로 참조함
    selector:
      app: cccr
==> targetPort 포트번호 대신 이름을 지정함.

(작성한 레플리카세 컨트롤러와 서비스 생성)
  kubectl create -f ccc-named-port-replicaset.yml -f ccc-named-port-svc.yml
replicaset .apps/cccr created
service/cccr created

#### 오브젝트 확인
(레플리카셋 컨트롤러 확인)  
  kubectl get replicasets.apps
NAME    DESIRED    CURRENT    READY    AGE
cccr    3          3         3        19s

(엔드포인트 확인)  
  kubectl get endpoints cccr
  
(파드 목록 및 파드 IP 확인)  
  kubectl get pods -o wide
 
(실시간 확인)
while (1) {kubectl get po; start-sleep -seconds:1; clear}

#### 포트 변경 및 확인
(포트수정)
  kubectl edit replicaset.apps cccr
==> 8080을 8081로 변경 저장

(포트 변경하고 나 후, 현재 동작중인 파드의 포트는 변경되질 않으므로, 기존의 포트를 삭제)
  kubectl delete pods -l app=cccr

(새로운 파드가 생성된 것을 확인)
  kubectl get pods
  
(엔드포인트 다시 확인)
  kubectl get endpoints cccr


 # web-was-db 연동 구성
 서비스 탐색을 이용하라!!!!
 
 ### DNS을 이용한 서비스 검색
  kubectl get all -n kube-system -l k8s-app=kube-dns
NAME                           READY   STATUS    RESTARTS   AGE
pod/coredns-5c98db65d4-4ntms   1/1     Running   5          47h
pod/coredns-5c98db65d4-7dql7   1/1     Running   5          47h


NAME               TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
service/kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   47h


NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/coredns   2/2     2            2           47h

NAME                                 DESIRED   CURRENT   READY   AGE
replicaset.apps/coredns-5c98db65d4   2         2         2       47h


 
 
 
