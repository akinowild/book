# SSL证书申请、颁发说明

下载压缩包 openssl-1.0.0e
解压

```
# 生成一个RSA密钥
openssl genrsa -des3 -out 33iq.key 1024
 
# 拷贝一个不需要输入密码的密钥文件
openssl rsa -in 33iq.key -out 33iq_nopass.key
 
# 生成一个证书请求
# 会提示输入省份、城市、域名信息等，重要的是email一定要是你的域名后缀的；
# 这样就有一个 csr 文件了，提交给 ssl 提供商的时候就是这个 csr 文件。
# CA会给你一个新的文件cacert.pem，那才是你的数字证书。
openssl req -new -key 33iq.key -out 33iq.csr
 
# 自己签发证书
openssl x509 -req -days 365 -in 33iq.csr -signkey 33iq.key -out 33iq.crt
 
# 直接生成自签发证书
# 如果生成签署请求时加上-x509参数，那么就直接生成一个self-signed的证书，即自己充当CA认证自己
openssl req -x509 -newkey rsa:1024 -nodes -days 999 -keyout 33iq.key -out 33iq.crt
```
生成证书请求时，会提示输入省份、城市、域名信息等， 重要的是，email一定要是你的域名后缀的 。

在要求输入Common Name(eg, YOUR name)时，输入你的主机名。Common Name必须和httpd.conf中server name必须一致，否则apache不能启动。启动apache时错误提示为：RSA server certificate CommonName (CN) `Koda’ does NOT match server name!? 。

这样就有一个 csr 文件了，提交给 ssl 提供商的时候就是这个 csr 文件。当然我这里并没有向证书提供商申请，而是在第4步自己签发了证书。

```
apt-get install ssl-cert
 
# make-ssl-cert生成证书的方法有两种，一种是根据生成按工具默认的方式生成，一种是按模板文件生成。
 
#默认的方式生成,生成的公钥(证书)在/etc/ssl/certs/ssl-cert-snakeoil.pem，私钥在/etc/ssl/private/ssl-cert-snakeoil.key。
make-ssl-cert generate-default-snakeoil
# 按模板文件生成
# 这里生成的证书采用pem格式，这个pem格式档案中包含了私钥和公钥(证书)两部分内容。
# make-ssl-cert是只能由root执行的命令。
make-ssl-cert /usr/share/ssl-cert/ssleay.cnf /etc/ssl/private/apache2.pem
```

###nginx配置ssl
编辑配置文件nginx.conf，给站点加上HTTPS协议
```
server {
    listen 443;
    server_name YOUR_DOMAINNAME_HERE;
 
    ssl on;
    ssl_certificate /usr/local/nginx/conf/33iq.crt;
    ssl_certificate_key /usr/local/nginx/conf/33iq_nopass.key;
    # 若ssl_certificate_key使用33iq.key，则每次启动Nginx服务器都要求输入key的密码。
 
    ssl_session_cache    shared:SSL:10m;
    ssl_session_timeout  10m;
 
    ssl_protocols SSLv3 TLSv1 TLSv1.1 TLSv1.2;
    #ssl_ciphers  ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
    ssl_ciphers   RC4:HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;
 
    location / {
            root /usr/local/nginx/html;
            index index.html;
        }
    }
```

###apache配置ssl
```
a2enmod ssl             # 开启SSL模块
a2ensite default-ssl    # 启用SSL站点
vi /etc/apache2/ports.conf
#编辑Apache端口配置，加入443端口
Listen 443
# 编辑default-ssl文件，加入证书对应的主机头。
vi /etc/apache2/sites-enabled/default-ssl
ServerName www.mike.me
# 编辑配置文件，修改如下几行
 
# 如果SSLCertificateFile中指定的证书已包含相应私钥，SSLCertificateKeyFile这一行就可以注释掉。
# 前面用make-ssl-cert生成的证书就是同时包含公钥和私钥的，所以这里注释掉了。
vi /etc/apache2/sites-enabled/default-ssl
 
# 如果是自签名证书，按如下配置：
SSLEngine on
SSLCertificateFile    /etc/ssl/private/apache2.pem
#SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
 
# 如果是第三方签署的CA证书，按如下配置：
SSLEngine on
SSLCertificateFile    /etc/ssl/certs/ssl-cert-snakeoil.pem
SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key
SSLCertificateChainFile /etc/ssl/certs/server-ca.crt

