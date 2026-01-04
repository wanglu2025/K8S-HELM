#初始redis修改项，202511月底最新版本8.2.1，模式哨兵模式三节点
vi charts-main/bitnami/redis/values.yaml
global:
  defaultStorageClass: "nfs-client"
  storageClass: "nfs-client"
  security:
    allowInsecureImages: true
  redis:
    password: "Alaedu_2025_r41w"
image:
  registry: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com
  repository: my_public_base/bitnami_redis
  tag: 8.2.1-debian-12-r0
replica:
  automountServiceAccountToken: true
sentinel:
  enabled: true
  image:
    registry: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com
    repository: my_public_base/bitnami_redis-sentinel
    tag: 8.2.1-debian-12-r0
  persistence:
    enabled: true
    size: 1Gi
  masterService:
    enabled: true
    type: NodePort
    nodePorts:
      redis: "30310"
rbac:
  create: true
volumePermissions:
  enabled: true
  image:
    registry: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com
    repository: my_public_base/bitnami_os-shell
    tag: 12-debian-12-r50  #原r51
#需要监控时开启（可选）
metrics:
  enabled: true
  image:
    registry: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com
    repository: my_public_base/bitnami_redis-exporter
    tag: 1.76.0-debian-12-r0