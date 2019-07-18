## 애플리케이션 사용자화
서비스 설정파일, 환경파일 민감한 비밀번호 등 설정

### (1) 컨테이너 애플리케이션 사용자화

### (2) 인자 및 인자 사용자화
  kubectl create -f cccr-arg-pod.yml
pod/cccr-arg-pod created

  kubectl port-forward cccr-arg-pod 8080:8080
Forwarding from 127.0.0.1:8080 -> 8080

(별도 터미널에서 실행)  
  curl http://localhost:8080

(위 port-forward 서비스 모니터링) 아래 내정보 나타남  
Forwarding from [::1]:8080 -> 8080  
Handling connection for 8080  

### (3) 환경변수를 이용한 사용자화

### (4) 컨피그 맴

### (5) 시크릿
