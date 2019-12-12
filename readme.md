
## 简介
提供一个连接读者与图书馆的图书资源共享平台。

如今人们阅读纸质书的频率越来越低，主要原因是购买纸质书需要成本，而从图书馆借书步骤繁琐：每个图书馆都需要办卡、缴纳押金；图书无法在线预约；不同的图书馆馆藏资源不共享，用户往往需要去多个图书馆才能找到自己想要的图书。希望通过为所有图书馆和读者提供服务，实现图书借阅线上化，最大化的提高资源利用率。



## 环境配置
本项目服务器环境：Linux + Nginx + MySQL + PHP 7.0 / PHP 5.6。

### 配置 HTTPS
小程序要求域名必须采用HTTPS协议。

1. 在[腾讯云](https://qcloud.com/)购买域名与服务器，完成备案
2. 在腾讯云为域名[申请SSL证书](https://console.cloud.tencent.com/ssl)
3. [下载](https://console.cloud.tencent.com/ssl)申请成功的证书，下载好的证书包含两个文件：.key与.crt
4. 在服务器的 Nginx 配置目录下新建一个目录cert（Ubuntu：/etc/nginx/sites-available/default），将这两个文件复制进去
5. 配置文件site.default相关内容如下，具体值需要从腾讯云获取：

server {

    listen 443; # HTTPS 监听 443端口

    ssl on;

    ssl_certificate cert/xxxxxx.crt; # 这里填写你下载的 .crt 文件路径

    ssl_certificate_key cert/xxxxxx.key; # 这里填写你下载的 .key 文件路径

    ssl_session_timeout 5m;

    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

    ssl_prefer_server_ciphers on;

    # ... 完整配置文件见下方

}

### URL 重写
ThinkPHP 或 Slim 框架支持 RESTful API，但是必须通过它们的入口文件访问API。例如，获取id为 1 的图书信息时，URL如下：

[https://www.my-api-server.cn/api/public/index.php/api/v1/books/1](https://www.my-api-server.cn/api/public/index.php/api/v1/books/1)

配置文件相关内容如下：

server {

    # ... 完整配置文件见下方

    location / {

        root   $root;

        index  index.html index.php;

        # 如果访问内容是文件，返回文件内容

        if ( -f $request_filename) {

            break; 

        }

        # 如果访问内容不是文件，则重定向至/api/index.php/$1，$1是所访问的URL

        # 如/api/v1/books/1会被重定向至/api/index.php/api/v1/books/1

        if ( !-e $request_filename) {

            rewrite ^(.*)$ /api/index.php/$1 last;

            break;

        }

    }

    # ... 完整配置文件见下方

}



## 安装运行
打开微信开发者工具→新建项目→选择源码目录→填写appid

在project.config.json中添加下列字段以忽略无用文件，否则会报错“代码包过大”。

"packOptions": {

  "ignore": [{

      "type": "file",

      "value": "./ui.png"

  }]

}


