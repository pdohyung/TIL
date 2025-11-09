## NameSpace

### 목적

```text
1. 격리
    - 하나의 물리적 클러스터 내에 가상 클러스터를 여러 개 생성하는 것과 같다.
    - 서로 다른 ns에선 동일한 이름의 리소스를 가질 수 있다.

2. 접근 제어 및 권한 관리
    - ns별로 RBAC(Role-Based Access Control) 정책을 적용할 수 있다.
    - 예를 들어, A팀은 team-a ns에만 리소스를 생성하고 수정할 권리를 가진다.

3. 리소스 할당 제한
    - ns별로 리소스 사용량 상한선을 설정할 수 있다. (예: dev ns는 최대 10개의 Pod, prod ns는 최대 100개의 Pod)
```

### 개요

```text
1. 스코프 - 대부분의 리소스(Pod, Deployment, Service 등)에 적용되지만, Cluster-scoped 리소스에는 적용되지 않는다.
    - Cluster-scoped: 특정 ns에 종속되지 않고 클러스터 전체에 걸쳐 존재하는 리소스다. 특히 기반 인프라나 전역 정책과 관련이 있다.
 
2. k8s 클러스터에는 기본적으로 default, kube-system, kube-public, kube-node-lease ns가 존재한다.

3. ns는 논리적으로 격리되지만, 네트워크는 공유한다. 그래서 서로 다른 ns의 Pod끼리 통신할 수 있다. (FQDN)
    - 동일 ns: http://my-backend
    - 다른 ns: http://my-backend.prod.svc.cluster.local

```

### 사용법

```text
1. 새로운 ns 생성: `kubectl create namespace dev`

2. 특정 ns에 접근: '-n dev'와 같이 ns 옵션을 추가,  `-A`(--all-namespaces)로 모든 ns에 접근 가능

3. 리소스를 특정 ns에 생성: metadata에 `namespace: dev`를 추가

4. 기본 ns 변경: `kubectl config set-context --current --namespace=dev`
    - k8s context는 kubectl 명령어가 어떤 클러스터에 어떤 사용자의 자격으로 접속할 지 정의해놓은 설정 그룹이다.

5. ns에 리소스 제한 적용: `kubectl apply -f dev-quota.yml`
```

```yaml
# dev-quota.yml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "1"
    requests.memory: "2Gi"
    limits.cpu: "2"
    limits.memory: "4Gi"
```
