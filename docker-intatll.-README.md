# Windows docker 환경변수 설정 

## windows powerShell(cmd) docker ps 등 명령어 실행시 에러발생시 아래 환결설정 필요
Windows에서 powerShell(cmd) 접속시 마다 아래 환경변수 설정이 필요.

## 1) docker 실행확인

> docker ps

powerShell, cmd에서 docker 실행시 error발생시 권한설정필요.

  error during connect: Get http://%2F%2F.%2Fpipe%2Fdocker_engine/v1.39/containers/json: open //./pipe/docker_engine: The system cannot find the file specified. In the default daemon configuration on Windows, the docker client must be run elevated to connect. This error may also indicate that the docker daemon is not running.


## 2) docker 실행권한 확인  

minikube.exe docker-env 명령으로 확인 후 아래 Run 명령 구문으로 확인후 powerShell, cmd 명령에 맞게 실행

> minikube.exe docker-env

$Env:DOCKER_TLS_VERIFY = "1"  
$Env:DOCKER_HOST = "tcp://192.168.99.100:2376"  
$Env:DOCKER_CERT_PATH = "C:\Users\ktds\.minikube\certs"  
'# Run this command to configure your shell:  
'# & minikube docker-env | Invoke-Expression  

## 3) docker 권한설정

### powerShell.exe
> minikube docker-env | Invoke-Expression 

### cmd.exe
> @FOR /f "tokens=*" %i IN ('minikube docker-env') DO @%i <==cmd


(참고 cmd 명령어) : @FOR /f "tokens=*" %i IN ('minikube docker-env') DO @%i


## 4) docker 확인
> docker --version

### docker image 확인
> docker images

### docker image 삭제
> docker rmi <image>

### docker image 다운로드
> docker pull <image>
 
### docker image 자세한 정보 확인
> docker image inspect < image | id >

### docker container 확인
> docker ps
> docker ps -a

### Container 생성
> docker create <image>
> docker create --name web1 httpd
  
### Conatiner 시작
> docker start < name | id >

### Container 생성 및 시작
> docker run <image>
run: create + start 합친 형태
  
### Contatiner 중지
> docker stop < name | id > 

### Container 재시작
> docker restart < name | id >

### Container 리소스 사용량 통계
> docker stats < name : id >

### Container 프로세스(애플리케이션) 목록 확인
> docker top < name | id >

### Container 로그 화긴
> docker logs < name | id >

### Container 명령 실행하기
> docker exec < name | id > <command>

### Container 표준 입력/출력/에러 붙이기
> docker attach < name | id >

### Container 자세한 정보 확인
> docker inspect < name | id >

### Container 삭제
> docker rm < name | id >
### 실행중인 Container 삭제
> docker rm -f < name | id >
image삭제: docker rmi <image>
  

#### docker 컨테이너 중  ubuntu 찾기
> $ docker ps -a | findstr ubuntu
