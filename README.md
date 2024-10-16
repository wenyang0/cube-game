# 魔法游戏

## Docker部署方法

```
docker run -d -it  -p 80:80  cube-game:1.0

在浏览器中访问nodeIP:80 端口即可
```

## K8S部署方法

##### 创建deployment cube-game.yaml
```
apiVersion: apps/v1 # for versions before 1.8.0 use apps/v1beta1
kind: Deployment
metadata:
  name: cube-game #应用名称。
  labels:
    app: cube-game
spec:
  replicas: 1 #设置副本数量。
  selector:
    matchLabels:
      app: cube-game  #对应服务中Selector的值需要与其一致，才可以通过服务公开此应用。
  template:
    metadata:
      labels:
        app: cube-game
    spec:
      containers:
      - name: cube-game
        image: cube-game:1.00 #替换为您实际的镜像地址，格式为：<image_name:tags>。
        ports:
        - containerPort: 80 #需要在服务中暴露该端口。
        resources:
          limits: #设置资源限制。
            cpu: '1'
            memory: 1Gi
          requests: #设置所需资源
            cpu: 500m
            memory: 512Mi 
```

##### 创建service cube-game.yaml
```
apiVersion: v1
kind: Service
metadata:
  labels:
    app: cube-game
  name: cube-game-svc
  namespace: default
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: cube-game # 需要与Deployment YAML文件中的matchLabels的值一致。
  type:  ClusterIP
```
