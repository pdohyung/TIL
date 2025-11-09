### Deployments

```bash
운영 환경에 배포되는 웹 서버는 여러 개의 인스턴스가 필요하다.
각 인스턴스 컨테이너는 Pod로 캡슐화 되었다고 가정한다.

1. Deployments는 Pod를 직접 관리하지 않는다. 여러 인스턴스는 ReplicaSet을 통해 배포된다.
  - deployment를 생성하면, 정의된 내용으로 replicaset, pod가 생성된다.
2. 더 높은 계층에 위치한 Deployments는 롤링 업데이트를 통해 인스턴스를 업그레이드한다.
  - 사용자에게 직접적인 영향을 미치므로, 애플리케이션을 차례로 업데이트 한다.
  - 예기치 않은 오류가 발생하면, 롤백을 통해 변경사항을 취소한다.
```

```yaml
# kind를 제외하면 ReplicaSet과 비슷하다
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    type: front-end
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: front-end
```

```shell
kubectl create -f deployments-definition.yml

kubectl get deployments

kubectl get replicaset

kubectl get pods
```
