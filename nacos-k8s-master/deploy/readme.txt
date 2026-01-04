https://github.com/nacos-group/nacos-k8s  #使用该方式在k8s内部署启动后报错。

修改配置：
sudo vi nacos-k8s/deploy/mysql/mysql-nfs.yaml
#将原先的nfs类型修改为StorageClass模式，与服务器地址解耦
  storageClassName: "nfs-client" # 指定要使用的 StorageClass 名称
        image: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/nacos_nacos-mysql:5.7
#mysql默认用户密码后续修改

sudo vi nacos-k8s/deploy/nacos/nacos-pvc-nfs.yaml
      #serviceAccountName: nfs-client-provisioner
          image: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/nacos_nacos-peer-finder-plugin:1.1
          imagePullPolicy: IfNotPresent
          imagePullPolicy: IfNotPresent
          image: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/nacos_nacos-server:latest
#开启tocken
          volume.beta.kubernetes.io/storage-class: "nfs-client"
            - name: NACOS_AUTH_TOKEN
              valueFrom:
                secretKeyRef:
                  name: nacos-secret
                  key: auth-token
            - name: NACOS_AUTH_IDENTITY_KEY
              value: "serverIdentity"
            - name: NACOS_AUTH_IDENTITY_VALUE
              value: "security"
#添加service并暴露端口
---
apiVersion: v1
kind: Service
metadata:
  name: nacos
  namespace: nacos
  labels:
    app: nacos
spec:
  type: NodePort
  ports:
  - port: 8848
    targetPort: 8848
    nodePort: 30000  # 自定义NodePort端口，范围30000-32767
    name: server
  - port: 9848
    targetPort: 9848
    nodePort: 30001
    name: client-rpc
  - port: 9849
    targetPort: 9849
    nodePort: 30002
    name: raft-rpc
  selector:
    app: nacos

crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/nacos_nacos-mysql:5.7
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/nacos_nacos-peer-finder-plugin:1.1
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/nacos_nacos-server:latest
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/nacos_nacos-server:v2.5.2

nacos/nacos-mysql:5.7
nacos/nacos-peer-finder-plugin:1.1
nacos/nacos-server:latest
nacos/nacos-server:v2.5.2


#生成tocken并创建secret
openssl rand -base64 32
apiVersion: v1
kind: Secret
metadata:
  name: nacos-secret
type: Opaque
data:
  auth-token: CtUvqQMS09BqgaHUuPjNcW0F9wZj5UI9+XqQzvFXehQ=  # 替换为你生成的 token

#生成tocken并创建secret
kubectl create secret generic nacos-secret \
  --namespace nacos \
  --from-literal=auth-token=$(openssl rand -base64 32)

#部署naocs
cd nacos-k8s
kubectl create ns nacos
kubectl apply -f deploy/mysql/mysql-nfs.yaml -n nacos  #mysql用于nacos数据共享
kubectl apply -f deploy/nacos/nacos-pvc-nfs.yaml -n nacos


报错1:
create Pod nacos-0 in StatefulSet nacos failed error: pods "nacos-0" is forbidden: error looking up service account nacos/nfs-client-provisioner: serviceaccount "nfs-client-provisioner" not found
#需创建tocken并添加变量
报错2：
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'configChangeClusterSyncRequestHandler' defined in URL [jar:nested:/home/nacos/target/nacos-server.jar/!BOOT-INF/lib/nacos-config-3.1.1.jar!/com/alibaba/nacos/config/server/remote/ConfigChangeClusterSyncRequestHandler.class]: Unsatisfied dependency expressed through constructor parameter 0: Error creating bean with name 'configMigrateService': Invocation of init method failed
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'configMigrateService': Invocation of init method failed
Caused by: java.lang.Exception: [migrate] config_gray namespace migrate pre check failed
Nacos v3在启动时尝试进行配置迁移。

换成v2版本后报错：
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'configOpsController' defined in URL [jar:file:/home/nacos/target/nacos-server.jar!/BOOT-INF/lib/nacos-config-2.5.2.jar!/com/alibaba/nacos/config/server/controller/ConfigOpsController.class]: Unsatisfied dependency expressed through constructor parameter 0; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'externalDumpService': Invocation of init method failed; nested exception is ErrCode:500, ErrMsg:Nacos Server did not start because dumpservice bean construction failure :
StatementCallback; bad SQL grammar [SELECT COUNT(*) FROM config_info_gray]; nested exception is java.sql.SQLSyntaxErrorException: Table 'nacos_devtest.config_info_gray' doesn't exist
#最后使用了官网推荐的 Nacos Operator在Kubernetes部署Nacos Server.成功部署。


