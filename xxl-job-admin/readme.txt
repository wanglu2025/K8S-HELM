说明：
https://github.com/xuxueli/xxl-job
https://www.xuxueli.com/xxl-job/  #中文文档

#1、下载较新镜像（需在aws上）
sudo docker pull xuxueli/xxl-job-admin:3.2.0 
sudo docker tag xuxueli/xxl-job-admin:3.2.0 crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/xuxueli_xxl-job-admin:3.2.0
sudo docker push crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/xuxueli_xxl-job-admin:3.2.0

#2、导入初始化sql
新建"xxl_job"库，注意是下划线。
sql位置：xxl-job-master/doc/db/tables_xxl_job.sql，导入以上xxl_job数据库。

#3.1、Docker 镜像方式搭建调度中心
#创建容器并运行
sudo nerdctl run -d \
-e PARAMS='--spring.datasource.url=jdbc:mysql://10.220.73.134:30306/xxl_job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai --spring.datasource.username=root --spring.datasource.password=R00t!Aladedu#2025' \
-p 8080:8080 \
-v /tmp:/data/applogs \
--name xxl-job-admin \
crpi-rlo3wv5p9a1d385b.cn-beijing.personal.cr.aliyuncs.com/my_public_base/xuxueli_xxl-job-admin:3.2.0
#报错权限拒绝，连接不上mysql，PARAMS内指定用户名密码。
#报错连接拒绝，验证容器内访问mysql，修改为一行。

#3.2、将docker形式修改为deployment形式
见"xxl-job-admin.yaml"

#4、验证运行情况
sudo nerdctl logs -f xxl-job-admin
https://test.aladdinedu.com/xxl-job-admin/
admin/123456（默认,登录后修改）
admin/Asje287642

