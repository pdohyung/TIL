쿠버네티스의 목적: 애플리케이션의 인스턴스를 쉽게 배포하고 여러 서비스 간의 통신을 쉽게 하는 것

### Cluster Architecture

컨테이너를 실행할 수 있는 소프트웨어, 컨테이너 런타임 엔진은 모든 노드에 존재한다. 애플리케이션은 컨테이너 형식이기 때문이다.

- 마스터노드
    - etcd 클러스터: 클러스터에 대한 정보를 저장
    - kube-scheduler: 노드에서 애플리케이션, 컨테이너의 스케줄링을 담당
    - controller-manager: 노드 컨트롤러, 복제 컨드롤러 등 다양한 기능을 처리
    - kube-apiserver: 클러스터 내의 모든 작업을 오케스트레이션한다.
- 워커노드
    - kubelet: kube api 서버의 지시를 수신하고, 컨테이너를 관리한다.
    - kube-proxy: 클러스터 내부 서비스 간의 통신을 지원한다.

### ETCD

- 관계형 데이터베이스는 SQL을 사용하여 성능은 좋지만, 특정 레코드는 빈 칼럼을 가지게 될 수 있기에 유연하지 않다. (정형 데이터)
- 문서형 데이터베이스는 각 문서마다 정의가 유연하고, 독립적으로 업데이트할 수 있다. 다른 테이블과 조인하여 복잡한 쿼리를 수행하는 것은 제한적이다. (반정형 데이터)
- 키-값 저장소는 스키마와 복잡한 쿼리가 없지만, 성능이 매우 빠르고 유연하여 데이터 구조에 대한 걱정없이 모든 것을 저장할 수 있다. (빠른 조회)

```bash
./etcd # etcd 실행
./etcdctl put key1 value1 # 삽입
./etcdctl get key1 # 조회
./etcdctl # etcd 커맨드 조회
```

- etcd 저장소에는 노드, 파드, 구성, 시크릿, 계정, 역할 등 클러스터 관련 정보가 저장된다. 노드 추가, 파드 배포 등 클러스터에 대한 변경 사항은 etcd 서버에 업데이트된다.
- kubectl get 명령은 etcd 서버에서 가져온다. 또한 etcd는 [`https://](https://$IP)${INTERNAL_IP}:2379`로 수신한다.
- kubeadm으로 클러스터를 설정하면, `kubectl get pods -n kube-system`에서 etcd-master를 조회할 수 있다.
- `kubectl exec etcd-master -n kube-system etcdctl get / -prefix -keys-only`로 저장된 모든 키를 가져올 수 있다. 루트 디렉토리가 registry고, 그
  아래에 노드, 파드, 규칙 등의 구성이 있다.
- 고가용성 환경에선 다수의 마스터 노드와 etcd 인스턴스가 분산된다. 이 경우 etcd 서비스 구성에 올바른 매개변수를 설정하여 다른 etcd 서버를 인지하도록 해야 한다. (`initial-cluster`
  옵션)

### Controller

Kube Controller Manager는 시스템 내의 다양한 구성 요소 상태를 지속적으로 모니터링하고, 원하는 작동 상태로 만들기 위해 동작한다.

- 노드 컨트롤러는 노드의 상태를 모니터링하고, 애플리케이션이 중단 없이 실행되도록 하는 책임을 가진다.

```
1. Node Monitor Period: kube-apiserver를 통해 5초마다 노드의 상태를 확인한다. (kubectl get nodes: 노드 모니터링)
2. Node Monitor Grace Period: 문제가 있는 노드인지 판단하기 위해 40초를 기다린다.
3. POD Eviction Timeout: 노드에 문제가 있다면, 5분 동안 다시 연결할 수 있는 시간이 주어진다.
4. 응답이 없으면 파드를 제거하고, ReplicaSet이 있는 경우, 다른 정상 노드에 Pod를 다시 생성한다.
```

- 레플리케이션 컨트롤러는 ReplicaSet을 감시하며 항상 지정된 개수의 Pod가 유지되도록 관리한다.

```
1. 사용자가 특정 Pod를 3개로 유지하라고 설정한다.
2. 컨트롤러는 Pod 개수를 지속적으로 확인한다.
3. 만약 Pod 중 1개가 삭제되면, 자동으로 새로운 Pod를 생성하여 3개를 유지한다.
```

- Kube Controller Manager 서버 옵션 조회 방법

```bash
kubectl get pods -n kube-system # 파드 조회 - kubeadm
cat /etc/kubernetes/manifests/kube-controller-manager.yaml # 설정 파일 - kubeadm
ps aux | grep kube-controller-manager # 실행 옵션 확인 
```

## Kude-Scheduler

kude-scheduler는 어느 Node에 어떤 Pod를 배치할지 결정하는 역할만 수행한다. 실제 Pod를 배치하는 일은 kubelet이 수행한다.

### Scheduler 동작 방식

```shell
1. 필터링 단계: 파드가 실행될 수 있는 후보 노드 목록을 생성한다. 특히 CPU, 메모리 등 리소스가 충분한 Pod를 고른다.
2. 스코어링 단계: 필터링을 통과한 실행 가능한 노드에 대해, 점수를 매겨 가장 적합한 노드를 결정한다. 기본적으로 잔여 리소스가 가장 많은 노드에 높은 점수를 주어 자원을 분산시킨다.
```

### Scheduler 설정 옵션

```bash
cat /etc/kubernetes/manifests/kube-scheduler.yaml # 설정 파일 - kubeadm
ps aux | grep kube-scheduler # 실행 옵션 확인 
```

## Kubelet

### 동작

kubelet은 kube-apiserver와 통신하며 아래와 같은 역할을 수행한다.

```bash
1. API 서버를 감시하며 자신한테 할당된 파드의 정보를 받는다. (kube-scheduler가 할당한 pod)
2. 이 정보를 바탕으로 컨테이너 런타임에서 필요한 이미지를 가져와, 컨테이너를 생성, 실행, 중지하는 등 라이프사이클을 관리한다.
3. 요청이 잘 지켜지는 지 확인하고, 이후에 node와 컨테이너 상태를 모니터링하여 보고한다.
```

### 조회

```bash
# kubeadm은 kubelet을 자동으로 설치해주지 않는다. 따라서 각 work node에 수동으로 설치해야 한다.
ps -aux | grep kubelet # 실행 중인 kubelet 프로세스와 옵션 조회
```

## Kube Proxy

### 동작

```bash
1. kube proxy는 kube-apiserver를 감시하며 새로운 서비스 및 endpoint 정보를 확인한다.
2. 서비스와 endpoint 정보를 바탕으로 node 내부에 IPtables 네트워크 규칙을 설정한다.
3. 규칙에 따라 서비스의 가상 IP(Cluster IP)로 들어오는 트래픽을 해당 서비스에 속한 백엔드 Pod들 중 하나로 로드밸런싱한다.
```

### 조회

```bash
kubectl get pods -n kube-system # 각 node에 kube proxy가 pod로 배포되어 있다.
kubectl get daemonset -n kube-system # 실제로 daemonset으로 배포되므로 각 node에는 항상 단일 pod가 배포된다.
```

## Pod

목표 → 클러스터의 워커 노드에 컨테이너 형태로 애플리케이션을 배포하는 것

- 쿠버네티스가 생성할 수 있는 가장 작은 단위, 컨테이너를 관리하기 위한 논리적인 그룹 역할
- 파드 내의 컨테이너간의 namespace, 스토리지 볼륨, ip주소 등을 공유한다.
- 쿠버네티스 클러스터(노드, 컨트롤 플레인) → Pod → 컨테이너 순으로 관리를 한다.
- nginx 이미지를 pod로 실행하고 외부에서 접속하려면 노드 내부를 설정해야한다.

## Kube-API Server

- kube-api server는 k8s 클러스터의 api를 제공하는 컴포넌트다.
- 클러스터 외부의 통신과 클러스터 내의 리소스를 관리한다.
- k8s 클러스터의 제어 중심 역할을 담당하며, 모든 리소스와 객체에 대한 작업을 처리한다.

**Pod 생성 예**

1. Authenticate User: kube-api server는 요청을 인증하고,
2. Validate Request: 검증한다.
3. Retrieve data: 데이터 응답
4. Update ETCD: node가 없는 pod가 생성하고, 사용자한테 알린다.
5. Scheduler: 생성된 pod의 적절한 node 위치를 api server에 보고한다.
    - 스케줄러는 api server를 지속적으로 모니터링하여, node가 없는 pod를 확인한다.
6. Update ETCD: api 서버가 etcd 클러스터의 정보를 업데이트한다.
7. Kubelet: api 서버가 정보를 해당 kubelet에 전달하고, kubelet은 node에 pod를 생성하고 컨테이너 런타임 엔진에 애플리케이션 이미지를 배포하도록 지시한다.
8. Update ETCD: kubelet이 api server에 전달하고, api server는 etcd 클러스터를 업데이트한다.

**변경이 요청될 때마다 비슷한 패턴으로 동작한다.**
클러스터를 변경하는 모든 작업의 중심에는 kube-api server가 있다.

- 특히 api server는 etcd 클러스터와 유일하게 상호작용하는 컴포넌트다.
- 나머지 컴포넌트는 api server에 전달하여 etcd 클러스터의 업데이트를 수행한다.

### YAML

- 쿠버네티스는 Pod, Replica, Deployment, Service 등과 같은 오브젝트를 생성하기 위한 입력으로 yaml 파일을 사용한다.
- 쿠버네티스 정의 파일에는 항상 apiVersion, kind, metadata, spec이 포함된다.

  | Kind | apiVersion | 
  | --- |------------|
  | Pod | v1         |
  | Service | v1         |
  | ReplicaSet | apps/v1    |
  | Deployment | apps/v1    |

- `metadata`는 이름, 레이블 등과 같은 개체에 대한 데이터다. (사전 형식, key-value 형식)
    - 라벨을 미리 지정하면, 프론트엔드, 백엔드, 데이터베이스 등 라벨을 기준으로 필터링할 수 있다.
- `spec`은 오브젝트의 상태, 구체적인 설정을 정의한다. `kind`에 따라 달라진다.
    - Pod의 경우는 컨테이너 이미지, 포트 등을 정의한다. 여러 컨테이너를 `-` 로 구분하여 정의할 수 있다.

```shell
kubectl create -f pod-definition.yml # pod 생성
kubeclt get pods
kubectl describe pod myapp-pod
```
