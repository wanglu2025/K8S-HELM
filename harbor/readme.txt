expose:
  tls:
    certSource: secret #选择sercret
    secret:
      secretName: "tls-secret"  #需提前创建
  ingress:
    hosts:
      core: dev-harbor.aladdinedu.com  #注意使用该方式必须使用域名登录才行，使用nodeport登录会报错“用户名密码错误”
    className: "nginx"  #必须选择已有的ingress类，否则
externalURL: https://dev-harbor.aladdinedu.com
persistence:
  imageChartStorage:
    disableredirect: true  #使用s3建议禁用重定向，否则上传镜像会有403报错
    type: s3
    s3:
      #existingSecret: ""    #需提前创建，本次直接使用密钥减少步骤
      region: default    #region可在客户端工具，bucket右键-->属性-->Location中查看
      bucket: harbor-bucket
      accesskey: YQDHLQZV9G7SMCTEKODE
      secretkey: 7rwejLtN4gJopUHmcljhjtKaeCHT52RkYbbRoQEK
      regionendpoint: http://10.220.73.135:80
      secure: false
      v4auth: true  #使用 S3 v4 认证
      rootdirectory: /   #存储路径修改
#existingSecretAdminPassword:    #需提前创建，本次使用密码，登录后修改
existingSecretAdminPasswordKey: HARBOR_ADMIN_PASSWORD
harborAdminPassword: "Harbor12345"  
trivy:
  enabled: true
#以及所有镜像

以及以下镜像：
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/goharbor_harbor-core:v2.13.0
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/goharbor_harbor-db:v2.13.0
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/goharbor_harbor-jobservice:v2.13.0
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/goharbor_harbor-portal:v2.13.0
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/goharbor_redis-photon:v2.13.0
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/goharbor_registry-photon:v2.13.0
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/goharbor_registryctl:v2.13.0
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/goharbor_harbor-registryctl:v2.13.0
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/goharbor_trivy-adapter-photon:v2.13.0
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/goharbor_harbor-exporter:v2.13.0  #暂未使用
goharbor/harbor-core:v2.13.0
goharbor/harbor-db:v2.13.0
goharbor/harbor-jobservice:v2.13.0
goharbor/harbor-portal:v2.13.0
goharbor/redis-photon:v2.13.0
goharbor/registry-photon:v2.13.0
goharbor/registryctl:v2.13.0
goharbor/harbor-registryctl:v2.13.0
goharbor/trivy-adapter-photon:v2.13.0
goharbor/harbor-exporter:v2.13.0


#服务介绍
外部请求 → Nginx → 分发到相应服务
                    ├── 管理操作 → Harbor-Core
                    ├── 镜像操作 → Registry
                    └── 界面访问 → Portal
                    
Harbor-Core 协调所有服务：
    ├── 元数据存储 → PostgreSQL
    ├── 缓存/队列 → Redis
    ├── 后台任务 → JobService
    ├── 安全扫描 → Trivy Adapter
    ├── 镜像管理 → Registry/Registryctl
    └── 监控指标 → Exporter
