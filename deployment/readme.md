## 扩容/缩容
### 手动扩缩容
kubectl scale deployment/deployment-test --replicas 4
### 自动扩容(需集群支持horizontal pod autoscaling)
kubectl autoscale deployment/deployment-test --min=10 --max=15 --cpu-percent=80

## 升级操作
### 1.更新镜像版本后执行
kubectl apply -f deployment.yaml --record
### 2.直接设置容器镜像
kubectl set image deployment/deployment-test buxybox=busybox:1.26 --record
### 3.直接编辑deployment -> spec.containers.image
kubectl edit deployment/deployment-test --record

## 升级历史
### 查看所有历史
kubectl rollout history deployment/deployment-test
### 查看指定版本Pod内容
kubectl rollout history deployment/deployment-test --revision=2

## 版本回退
### 回退到上一版本
kubectl rollout undo deployment/deployment-test
### 回退到指定版本
kubectl rollout undo deployment/deployment-test --to-revision=1

## 升级暂停/恢复
### 暂停升级，直到恢复为止
kubectl rollout pause deployment/deployment-test
### 多次更新部署资源内容，不会触发升级操作
kubectl set image deployment/deployment-test busybox=busybox:1.31.1
kubectl set resources deployment deployment-test -c=nginx --limits=cpu=200m,memory=512Mi
### 恢复升级，此时触发升级操作
kubectl rollout resume deployment/deployment-test
