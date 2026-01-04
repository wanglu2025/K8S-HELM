#初始mysql修改项,当前版本为202511月底最新版本9.4.0，模式为一主一从。
vi charts-main/bitnami/mysql/values.yaml

global:
  defaultStorageClass: "nfs-client"
  storageClass: "nfs-client"
  security:
    allowInsecureImages: true

image:
  registry: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com
  repository: my_public_base/bitnami_mysql
  tag: 9.4.0-debian-12-r1
architecture: replication
auth:
  createDatabase: false
  database: ""
  username: "mysqluser" 
  existingSecret: "mysql-core-secret"
primary:
#  persistence:
#    storageClass: "nfs-client"
#    accessModes:
#      - ReadWriteMany
  service:
    type: Nodeport
    nodePorts:
      mysql: "30306"
secondary:  #同上，端口为30307
volumePermissions:
  enabled: true   #首次部署启用，防止权限问题，后续更新可禁用提高启动速度。
  image:
    registry: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com
    repository: my_public_base/bitnami_os-shell
    tag: 12-debian-12-r50

#需要监控时开启（可选）
metrics: 
  enabled: true
  image:
    registry: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com
    repository: my_public_base/bitnami_mysqld-exporter
    tag: 0.17.2-debian-12-r16