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

### (4) configMap

### (5) 시크릿
configMap과 같은 key/value 값 저장, base64인코딩 저장  
Database image에서 활용가능  
  kubectl get secret
NAME                  TYPE                                  DATA   AGE    
default-token-fx2tj   kubernetes.io/service-account-token   3      2d22h    
ingress-tls-secret    kubernetes.io/tls                     2      20h    

  kubectl describe secret default-token-fx2tj
Name:         default-token-fx2tj
Namespace:    default
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: default
              kubernetes.io/service-account.uid: 9af3ac41-f622-4264-a2e4-a468ccc88f4a

Type:  kubernetes.io/service-account-token

Data
====
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tZngydGoiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjlhZjNhYzQxLWY2MjItNDI2NC1hMmU0LWE0NjhjY2M4OGY0YSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.LiCKB3V_S1R2gpqTvBhKsKuUu4lJ8PnGz6f7HRqbgDR7bze_5zbH2M2MiMvkUHJIisgxHI5Gg78pCg6i4x5dGoUAxIG6F51wfhcVxxZBK7XBmVyGt4lpUcnXs3mhoP4L1GgPkRjKreWLTPTS56uUZjmUd_RrCuFZzGaV2rmR6w3LQEzUxeMOwSHxd6ovGG-ML93e8JaAYV_sVEmGgXMNN9ScUTZpOobNHB6lxsLJJfEsBctxfNM4iU-nq3nysS1ti1_wflvecAPUShkuldM8MDaguurbO9ED4q4wZYLHIxWrdX0LshulLuUjJZR9BIylPMzMjpuNphtVG1K77gFJmQ
ca.crt:     1066 bytes
namespace:  7 bytes

  kubectl describe pod coredns-5c98db65d4-4ntms
내용중에 Mounts: 
     /var/run/secrets/kube~~~  확인  
 
