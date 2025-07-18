## spring platform deployment/CICD via k8s
### monitered by argoCD

<img width="1888" height="868" alt="image" src="https://github.com/user-attachments/assets/d1f03afa-8b30-4ac3-a68e-e5dfcc6af3c4" />

## <File structing>  
mkdir -p aws-eks/base/istory-app       # istory 애플리케이션 공통(base) 설정 디렉토리  
mkdir -p aws-eks/base/istory-db        # DB(PostgreSQL, MySQL 등) 공통 설정 디렉토리  
mkdir -p aws-eks/base/istory-tools     # 툴(디버깅용 등) 공통 설정 디렉토리  
mkdir -p aws-eks/overlay/aws-dev       # AWS 개발 환경용 overlay 설정  
mkdir -p aws-eks/overlay/aws-prod      # AWS 운영 환경용 overlay 설정  
mkdir -p aws-eks/overlay/local-dev     # 로컬 개발 환경용 overlay 설정  
  
### [istory-app 애플리케이션 설정]
touch aws-eks/base/istory-app/istory-app-config.yml        # ConfigMap, 환경 변수 같은 앱 설정 정의  
touch aws-eks/base/istory-app/istory-app-deploy.yml        # Deployment (파드 정의 및 스케일링 등)  
touch aws-eks/base/istory-app/istory-app-lb.yml            # LoadBalancer Service 정의 (외부 트래픽 연결용)  
touch aws-eks/base/istory-app/kustomization.yml            # 위의 리소스들을 하나로 합치는 kustomize entrypoint  
  
### [istory-db DB 설정]  
touch aws-eks/base/istory-db/istory-db-pod.yml             # DB 파드 직접 정의 (StatefulSet이 아닐 수도 있음)  
touch aws-eks/base/istory-db/istory-db-lb.yml              # DB 접근용 LoadBalancer Service 정의  
touch aws-eks/base/istory-db/istory-db-pvc.yml             # DB 데이터 저장용 PersistentVolumeClaim 정의  
touch aws-eks/base/istory-db/istory-db-sc.yml              # StorageClass 정의 (PVC가 사용할 스토리지 종류 정의)  
touch aws-eks/base/istory-db/kustomization.yml             # DB 관련 리소스를 조합하는 kustomization entrypoint  

### [istory-tools 유틸리티 툴]  
touch aws-eks/base/istory-tools/busybox.yml                # busybox 테스트용 Pod (curl, ping 등 디버깅에 사용)  
touch aws-eks/base/istory-tools/kustomization.yml          # tools 리소스를 모은 kustomization entrypoint  

# [AWS 개발 환경 오버레이 설정]
touch aws-eks/overlay/aws-dev/kustomization.yml            # aws-dev 환경에 대한 오버레이 설정 진입점
touch aws-eks/overlay/aws-dev/patch-deploy.yml             # base의 Deployment를 수정(patch)하기 위한 파일 (replica 수, 이미지 tag 등)
touch aws-eks/overlay/aws-dev/patch-lb-annotations.yml     # Service에 AWS ELB annotation 추가용 patch
touch aws-eks/overlay/aws-dev/.env.secret                  # Kustomize에서 참조할 비밀 환경 변수들 (예: DB 비번 등)
