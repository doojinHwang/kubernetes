
### 쿠번네티스의 클러스터 정보 확인
    kubectl cluster-info
dump상세정보확인
    kubectl cluster-info dump
  
### 쿠버네티스의 노드 정보 확인
    kubectl get nodes
자세한 정보
    kubectl get nodes -o wide
  
### 애드온
#### 클러스터 DNS:
  kube-dns: 쿠버테니스 내부에서만 사용(필수)
  
#### 컨테이너 리소스 모니터링:  
프로메테우스 외부 프로젝트 많이 사용
  
### 클러스터에서 지원하는 API 버전확인(alpha정보제외)
    kubectl api-versions
    
### API 리소스 확인
    kubectl api-resources
    
    
    
<hr/>

    
## YAML 및 오브젝트
### YAML
YAML Ain't Markup Language(YAML은 마크업 언어가 아니다)라는 재귀적인 이름에서 유래...
(중요) YAML에서는 tab키를 인식하질 않는다.(사용x)
들여쓰기는 보통 2칸으로 사용  
[YAML(https://yaml.org)](https://yaml.org/)

스칼라/스트링, 리스트/어레이, 해시/딕셔너리 3가지 많이 사용.

#### (1) 스칼라/스트링
-단순 간단한 문자 또는 문자열
-쌍/홑 따옴표를 사용하여 둘러쌀 수 있으나 일반적으로 사용하질 않는다.
-한줄에 하나의 문자열이다.
-순서가 없다.  

#### (2) 리스트/어레이
-스칼라 앞에 '-'을 붙이고 '-' 와 문자 사이는 공백이 있어야 한다.
 ==> - abc  
-리스트는 순서가 있다.
-한 줄에 하나의 요소를 표현  

#### (3) 해시/딕셔너리
- key: value 형태, 이때 주의 할 점은 value 앞은 공백으로 띄워쓰기 필수
 ==> 예) name: 홍길동  

#### (4) 해시의 리스트

#### (5) 리스트의 해시

### YAML 문법
-YAML 문자열은 UTF-8 또는 UTF-16의 유니코드 문자집합 사용  
(참고) 유닉스에서 줄바꿈(개행)을 CRLF를 인식못하고 LF만 인식함  
-공백문자를 이용하여 들어쓰기(tab들어쓰기 사용하질 않음)  
-하나의 스트림에 있는 여러 개의 YAML 문서를 구분하기 위해  
 시작은 하이픈 3개(---), 끝은 마침표 3개(...)를 사용  
-주석은 #으로 표시, 한 줄이 끝날 때까지 유효
-YAML 파일릐 확장자는 yml 또는 yaml  

    apiVersion: v1
    kind: pod
    metadata:
      name: myapp-pod
      lables:
        app: myapp
        type: frontend
    spec:
      containers:
        - name: nginx-container
          image: nginx
필수 필드:  
-apiVersion: 오브젝트를 생성하기 위한 API버전(2019.97월 기준 현재 v1만 존재)
==> kubectl api-versions 명령결과에서 앞에 그룹이 없는 것만 사용가능
-kind: 오브젝트 종류(pod, services, replcaset, deployment 등등)  
-metadata: name, UID, namespace 등을 포함하는 기본적인 정보  
-spec: 오브젝트의 상태 정의, 정의할 요소는 정의하고자 하는 오브젝트의 종류에 따라 다름  

<hr/>

### Visual Studio Code 설정
setting: eol검색..에서 \n으로 설정
Extenstion 설치: yaml, kubernets

<hr/>

### 오브젝트(Pod) 생성 
    kubectl run cccr-app --image=ons/cccr:latest --port=8080 --generator=run/v1
--generator=run commond옵션은 pod를 생성하겠다는 의미

### Pod 보기
    kubectl get pods

'결과화면 예시---------------------------------------------------------------------------  
NAME             READY     STATUS    RESTARTS   AGE  
cccr-app-ddjxr   1/1       Running   0          57s  
'---------------------------------------------------------------------------------------  
==> ready 에서 1/1의 의미는 1개 준비 상태에 / 1개가 실행됨


### Pod 상세보기
    kubectl get pods -o wide
 
'결과화면 예시----------------------------------------------------------------------------  
NAME             READY     STATUS    RESTARTS   AGE       IP           NODE  
cccr-app-ddjxr   1/1       Running   0          2m        172.17.0.6   minikube  
'---------------------------------------------------------------------------------------  

https://kubernetes.io/docs/concepts/workloads/controllers/replicationcontroller/

    kubectl get rc
rc: replicationcontroller
    kubectl get replicationcontroller

'결과화면 예시----------------------------------------------------------------------------  
NAME       DESIRED   CURRENT   READY     AGE  
cccr-app   1         1         1         3m  
'---------------------------------------------------------------------------------------    

#### replicationcontroller 상세보기
    kubectl get rc -o wide
'------------------------------------------------------------------------------------------------------  
NAME       DESIRED   CURRENT   READY     AGE       CONTAINERS   IMAGES                 SELECTOR  
cccr-app   1         1         1         3m        cccr-app     ons/cccr:latest   run=cccr-app  
'------------------------------------------------------------------------------------------------------  

### 오브젝트(Pod) 삭제
    kubectl delete pod cccr-app-ddjxr
결과메시지: pod "cccr-app-ddjxr" deleted

    kubectl get po
'-------------------------------------------------------  
NAME             READY     STATUS    RESTARTS   AGE  
cccr-app-956n6   1/1       Running   0          38s  
'-------------------------------------------------------  
kubectl delete pod cccr-app-ddjxr로 삭제해도 pod는 무조건 1개를 생성함.

### --watch command
1초마다 실시가 보기

#### replicationcontroller 상태보기
    kubectl get rc --watch
    
#### Pod 상태보기
    kubectl get ps --watch

### 서비스 생성
    kubectl expose rc cccr-app --type=LoadBalancer --name=cccr-svc
결과화면: service "cccr-svc" exposed


### 서비스 확인
    kubectl get services
'------------------------------------------------------------------------------------  
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE  
cccr-svc     LoadBalancer   10.100.20.190   <pending>     8080:32559/TCP   27s  
kubernetes   ClusterIP      10.96.0.1       <none>        443/TCP          1d  
'------------------------------------------------------------------------------------  
#### (1) minikube tunnel    
-EXTERNAL-IP pending 상태인데 이는 외부 loadblancer 없어서 외부 IP를 얻지 못해서... minikube에서는 아래 명령으로 사용하면 됨.
    minikube.exe tunnel
    
#### (2) minikube service 서비스명
위 명령 error발생이 아래 명령으로 실행후 브라우져로 확인(ie에서 error발생시 크롬으로..)  
    minikube.exe service list  

|-------------|------------|-----------------------------|  
|  NAMESPACE  |    NAME    |             URL             |  
|-------------|------------|-----------------------------|  
| default     | cccr-svc   | http://192.168.99.100:32559 |  
| default     | kubernetes | No node port                |  
| kube-system | kube-dns   | No node port                |  
|-------------|------------|-----------------------------|  

위 서비스 목록에서 Name확인 후 지정
    minikube.exe service cccr-svc
결과메시지: Opening kubernetes service default/cccr-svc in default browser...



### Pod 스케일링
    kubectl scale rc cccr-app --replicas=3
결과메시지: replicationcontroller "cccr-app" scaled

위 Pod 스케일링시 실시간 모니터링 상태를 띄운상태로 확인 가능
-아래 watch로 보면 pod 스케일링 상태 보임.
    kubectl get rc --watch <== replicationcontroller 상태보기
    kubectl get ps --watch <== pod 상태보기 

### 생성된 오브젝트(pod) 삭제
    kubectl delete service cccr-svc
결과메시지: service "cccr-svc" deleted

위 pod를 삭제해도 계속 생성됨 그래서 아래 리플리케이션컨트롤을 삭제함.
    kubectl delete rc cccr-app

### replicationcontroller(rc) 삭제확인 
    kubectl get rc
위 Pod cccr-app 삭제 확이됨.

### 오브젝트 확인
    kubectl get pods
 
