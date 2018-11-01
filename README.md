# docker_demo
一步步构建多版本PHP开发环境

操作步骤：

拉取镜像
docker pull nginx
docker pull php:5.6-fpm
docker pull php:7.2-fpm

准备文件
git clone https://github.com/FineYi/docker_demo.git
cd ../docker_demo

创建NGINX容器
docker run -d -p 8088:80 -v $PWD/www/default/:/usr/share/nginx/html/ --name local_nginx nginx

创建php7.2容器
docker run -d -p 9000:9000 -v $PWD/www/default/:/usr/share/nginx/html/ --name local_fpm72 php:7.2-fpm

查看php7.2容器的IP地址
docker inspect local_fpm72 | grep 'IPAddress'

进入NGINX容器
docker exec -it local_nginx bash

cat /etc/nginx/conf.d/default.conf

修改NGINX配置文件
vi conf/conf.d/docker-default.conf
修改如下部分

    location ~ \.php$ {
        fastcgi_pass   172.17.0.3:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/html$fastcgi_script_name;
        include        fastcgi_params;
    }

复制文件到容器内
docker cp conf/conf.d/docker-default.conf local_nginx:/etc/nginx/conf.d/default.conf

重启local_nginx容器NGINX服务
/etc/init.d/nginx reload

测试
http://localhost:8088/p.php
显示PHP版本7.2


停止local_fpm72容器
docker stop local_fpm72

创建php5.6容器
docker run -d -p 9000:9000 -v $PWD/www/default/:/usr/share/nginx/html/ --name local_fpm56 php:5.6-fpm

查看php5.6容器的IP地址
docker inspect local_fpm56 | grep 'IPAddress'

修改NGINX配置文件,并复制到容器中
vi conf/conf.d/docker-default.conf
修改如下部分

    location ~ \.php$ {
        fastcgi_pass   172.17.0.3:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /usr/share/nginx/html$fastcgi_script_name;
        include        fastcgi_params;
    }

docker cp conf/conf.d/docker-default.conf local_nginx:/etc/nginx/conf.d/default.conf

重启local_nginx容器NGINX服务
/etc/init.d/nginx reload

测试
http://localhost:8088/p.php