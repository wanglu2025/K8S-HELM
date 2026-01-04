#1、禁用node-exporter安装，因为信息化已安装。
vi values.yaml
nodeExporter:
  enabled: false

#2、修改镜像地址
vi charts/kube-state-metrics/values.yaml
image:
  registry: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com
  repository: my_public_base/kube-state-metrics
  tag: ""

vi charts/grafana/values.yaml 
image:
  registry: crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com
  repository: my_public_base/grafana
  tag: ""

##镜像
registry.k8s.io/kube-state-metrics/kube-state-metrics:v2.17.0
docker.io/grafana/grafana:12.3.0
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/kube-state-metrics:v2.17.0
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/grafana:12.3.0
