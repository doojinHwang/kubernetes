# windows powerShell(cmd) docker ps 등 명령어 실행시 에러발생시.. 아래 2개명령 실행

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

### * powerShell.exe
> minikube docker-env | Invoke-Expression 

### cmd.exe
> @FOR /f "tokens=*" %i IN ('minikube docker-env') DO @%i <==cmd


(참고 cmd 명령어) : @FOR /f "tokens=*" %i IN ('minikube docker-env') DO @%i

## 4) docker 확인
> docker ps
