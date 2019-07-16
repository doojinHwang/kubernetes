
### 쿠번네티스의 클러스터 정보 확인
    kubectl cluster-info
dump상세정보확인
    kubectl cluster-info dump
  
### 쿠버네티스의 노드 정보 확인
    kubectl get nodes
자세한 정보
    kubectl get nodes -o wide
  
 ### 애드온
 #### 클러스터 DNS
  kube-dns: 쿠버테니스 내부에서만 사용(필수)
  
  #### 컨테이너 리소스 모니터링  
  프로메테우스 외부 프로젝트 많이 사용
