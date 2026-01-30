## ReplicaController, ReplicaSet

### 특징

```text
k8s에서 Replica는 지정된 수의 Pod를 항상 유지하는 역할을 한다.

1. 장애 복구 (self-healing): 특정 Pod에 문제가 생기면, Replica가 자동으로 새로운 Pod를 생성해 유지한다.
2. 고가용성 (high availability): 동일한 Pod를 여러 개 띄워두면, 일부 Pod가 죽더라도 서비스는 중단되지 않는다.
3. 부하 분산 (load balancing): 여러 개의 Pod가 실행되므로, 트래픽을 분산시켜 안정적으로 대응할 수 있다.
4. 확장성 (Scalability): 'replicas' 값으로 Pod의 개수를 늘리거나 줄일 수 있다.
```

### 차이점

```text
ReplicaSet과 ReplicaController는 차이점이 존재한다.

1. apiVersion이 다르다. ReplicaSet은 v1이 아닌 apps/v1으로 정의한다.
2. ReplicaSet은 selector 정의가 필요하다. template으로 생성하지 않은 Pod도 관리할 수 있다.
    - 특히 label 필터링을 통해 replicas 수만큼 Pod가 확인된다면, 새로운 Pod를 배포하지 않는다.
```

```yaml
# pod-definition.yml
metadata:
  name: myapp-pod
  labels:
    tier: front-end

# replicaset-definition.yml, pod와 동일한 라벨 설정

selector:
  matchLabels:
    tier: front-end
```

### scale out

```text
ReplicaSet의 Pod 개수를 조절하는 법

1. yaml 파일의 replicas를 수정, kubectl replace -f replicaset-definition.yml
2. kubectl scale --replicas=5 -f replicaset-definition.yml
3. kubectl scale --replicas=5 rs my-replicaset
```
