
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
    
    
## YAML 및 오브젝트
### YAML
YAML Ain't Markup Language(YAML은 마크업 언어가 아니다)라는 재귀적인 이름에서 유래...
(중요) YAML에서는 tab키를 인식하질 않는다.(사용x)
들여쓰기는 보통 2칸으로 사용
[YAML](link: https://yaml.org/)

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

### (4) 해시의 리스트

### (5) 리스트의 해시

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
-apiVersion: 오브젝트를 생성하기 위한 API버전  
-kind: 오브젝트 종류(pod, services, replcaset, deployment 등등)  
-metadata: name, UID, namespace 등을 포함하는 기본적인 정보  
-spec: 오브젝트의 상태 정의, 정의할 요소는 정의하고자 하는 오브젝트의 종류에 따라 다름  



 
