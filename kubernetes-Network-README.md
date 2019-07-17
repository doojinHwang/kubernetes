## 네트워크
쿠버네티스를 설치하면 Calico 네트워크가 설치 됨(https://docs.projectcalico.org/v2.0/getting-started/kubernetes/)


## 서비스
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

서비스 확인
  kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   46h
