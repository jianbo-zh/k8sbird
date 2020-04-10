## 升级/回滚
### 两种升级策略(.spec.updateStrategy): 
OnDelete - 更新模板后，只有手动删除旧的Pod后才会创建新的Pod
RollingUpdate - 更新DaemonSet模板后，自动删除旧的Pod并创建新的Pod

### 升级相关操作
同Deployment升级一样

## 调度
### 默认可调度到所有可以调度的Node上
通过设置污点容忍，也可以调度到设置了污点不能调度的Node节点
```
spec:
  tolerations:
    - key: node-role.kubernetes.io/master
      operator: Exists
      effect: NoSchedule
```

### 可以选择指定的Node节点部署

**注意**
* DaemonSet部署Pod数量：通过 spec.nodeSelector 和 spec.affinity.nodeAffinity.requiredDuringSchedulingIgnoredDuringExecution 共同匹配的数量来确定
* 设置优选条件不会改变部署Pod的数量，所以spec.affinity.nodeAffinity.preferredDuringSchedulingIgnoredDuringExecution 配置对DaemonSet无效
* Pod亲和性(podAffinity) 和 Pod反亲和性(podAntiAffinity) 会影响到Schedule是否成功(spec.affinity.podAffinity.requiredDuringSchedulingIgnoredDuringExecution,spec.affinity.podAntiAffinity.requiredDuringSchedulingIgnoredDuringExecution)

**affinity参考链接**
[k8s高级调度,亲和性和反亲和性](https://www.jianshu.com/p/61725f179223)
[Kubernetes 亲和性调度](https://segmentfault.com/a/1190000018446833)

#### 1, 通过NodeSelector选择Node节点
```
spec:
  nodeSelector:
    disktype: ssd
```
#### 2, 通过NodeAffinity节点亲和性选择Node节点
```
spec:
  affinity:
    nodeAffinity:
      # 必须满足条件，schedule调度时在预选阶段执行
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
          - matchExpressions:
              - key: kubernetes.io/hostname
                operator: In
                values:
                  - k8s-node-2
                  - k8s-node-3
      # 优选条件，schedule调度时在优选阶段执行
      preferredDuringSchedulingIgnoredDuringExecution:
        - weight: 1
          preference:
            matchExpressions:
              - key: kubernetes.io/hostname
                operator: In
                values:
                  - k8s-node-2

```

#### 3, 通过PodAffinity或PodAntiAffinity来选择节点
```
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
	- labelSelector:
	    matchExpressions:
	      - key: app
		operator: In
		values:
		  - busybox
	  # 指定labelSelector所属命名空间，不设置或为空数组表示同当前Pod命名空间
	  #namespaces: []
	  topologyKey: kubernetes.io/hostname
      preferredDuringSchedulingIgnoredDuringExecution:
	- weight: 100
	  podAffinityTerm:
	    labelSelector:
	      matchExpressions:
		- key: app
		  operator: In
		  values:
		    - other-pod
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
	- labelSelector:
	    matchExpressions:
	      - key: app
		operator: In
		values:
		  - other-busybox
	  topologyKey: kubernetes.io/hostname
```

