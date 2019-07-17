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
      
)

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


### 서비스 접근 테스트
  kubectl run nettool -it --image=praqma/network-multitool --generator=run-pod/v1 --rm=true bash
If you don't see a command prompt, try pressing enter.
bash-5.0#

1초마다 서비스 혹인
  bash-5.0# watch -n1 -d http://10.96.138.178
 
 ### 서비스 세션 친화성 구성
 yml 작성시 sessionAffinity: [None | ClientIP] 설정으로 동일 파드로 연결제어 가능
 
 
 
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


 
 
 
