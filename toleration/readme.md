## 隔离/恢复

### 隔离节点，任何pod不能调度，已在节点上的pod不会被驱逐
kubectl cordon k8s-node-1

### 恢复节点，取消隔离
kubectl uncordon k8s-node-1


## 污点

### 设置节点污点(不能调度)，只有容忍该污点的pod才能调度
kubectl taint node k8s-node-1 key=value:NoSchedule

### 设置节点污点(不允许运行)，只有容忍该污点的pod才能继续运行，否则pod将被驱逐出该节点
####. tolerationSeconds: 设置pod将在多少秒后将被驱逐
1. 不设置该字段，将永久不被驱逐; 
2. 0或负数，将立即被驱逐;
3. 正整数，将在指定秒数后被驱逐）
kubectl taint node k8s-node-1 key2=value2:NoExecute


### 删除节点的污点(指定了key2的)
kubectl taint node k8s-node-1 key2-
kubectl taint node k8s-node-1 key:NoSchedule-



