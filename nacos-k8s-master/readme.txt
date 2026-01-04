#初始nacos配置修改项，nacos-server版本为2.0.3，模式为集群模式，三副本。
vi operator/chart/nacos-operator/values.yaml
image:
  repository: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/nacos_nacos-operator
  pullPolicy: IfNotPresent
vi operator/config/samples/nacos_cluster.yaml
  image: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/nacos_nacos-server:2.0.3
#添加数据持久化
  database:
    type: embedded
  # 启动数据卷，不然重启后数据丢失
  volume:
    enabled: true
    requests:
      storage: 1Gi
    storageClass: nfs-client