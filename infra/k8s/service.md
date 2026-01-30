## Service

- Service는 애플리케이션 내외부 다양한 구성 요소 간의 통신을 지원한다.
- Pod는 IP가 바뀌거나 제거될 수 있기에, Service의 Cluster IP로 느슨한 결합(Loose Coupling)을 제공한다.

### 서비스 타입

```text
1. NodePort는 Service가 Node의 포트에서 내부 Pod에 접근하도록 한다.
    - 웹 서버가 실행되는 Pod의 포트는 Target Port고, Service와 연결된다.
    - Service는 Cluster IP, Port를 가지고, NodePort와 연결된다.
    - 외부에서 웹 서버에 접근하는 포트는 NodePort다. (범위: 30000-32767)
    - Service는 selector로 대상 Node를 찾는다.
    - Node와 Pod 관계가 1:1, 1:N, N:M에 상관없이 동일한 NodePort를 사용할 수 있다.
    
2. ClusterIP는 서비스가 Cluster 내부에 가상IP를 생성하여 프론트엔드와 백엔드 등 서로 다른 서비스 간의 통신을 가능하게 한다.
    - ClusterIP(고정된 가상 IP)를 할당 받으며, 이 주소는 클러스터 외부에서 접근이 불가능하다.
    - 클러스터 내부 통신 및 서비스 간의 연결을 담당하고, Pod의 IP가 바뀌어도 Cluster IP는 변하지 않는다.

3. LoadBalancer는 여러 웹 서버에 부하를 분산하는 로드밸런싱을 수행한다.
    - ClusterIP와 NodePort 기능을 모두 포함하며, 클라우드 환경(AWS, Azure, GCP 등 )의 로드밸런서와 연동하여 외부 트래픽을 처리하는 데 사용한다.
    - LoadBalancer 타입으로 Service를 생성하면, ClusterIP와 NodePort가 설정된다.
    - Service 정의에 따라 클라우드 제공업체의 외부 로드밸런서를 생성한다. 그리고 클라이언트로부터 트래픽을 받기 위한 공인 IP주소를 가진다.
    - 외부 로드밸런서의 IP로 요청이 들어오면, 클러스터 특정 NodePort로 전달한다. 전달받은 트래픽을 NodePort에 연결된 Pod로 분배한다.
```

```yaml
# pod-definition.yaml에 아래 설정을 추가
ports:
  - containerPort: 80
    name: http-web # 별칭, targetPort: http-web

# service-definition.yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30000
  selector:
    app: myapp
    type: front-end
```
