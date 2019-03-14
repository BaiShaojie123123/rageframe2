## 伪静态

目录

- Nginx
- Apache
- IIS

### Nginx

推荐配置

```
location / {
    try_files $uri $uri/ /index.php$is_args$args;
}
location /backend {
    try_files $uri $uri/ /backend/index.php$is_args$args;
}
location /api {
    try_files $uri $uri/ /api/index.php$is_args$args;
}
location /wechat {
    try_files $uri $uri/ /wechat/index.php$is_args$args;
}
```

类似Apache的配置

```
location / 
{
     index  index.html index.htm index.php;

     if (!-e $request_filename) {
           rewrite ^/backend(.*)$ /backend/index.php?s=$1 last;
           rewrite ^/wechat(.*)$ /wechat/index.php?s=$1 last;
           rewrite ^/api(.*)$ /api/index.php?s=$1 last;
           rewrite ^/(.*)$ /index.php?s=$1 last;
           break;
     }
         
     #autoindex  on;
}
```
包含其他参数
```
server {
	listen       80;
            server_name   www.pt.com; #这里是站点hosts自己修改
            root   "/Users/baishaojie/rageframe2/web";#这里是指向自己项目的web目录 修改为自己的绝对路径
			location / {
				index index.html index.htm index.php;
				if (!-e $request_filename) {
					rewrite ^/backend(.*)$ /backend/index.php?s=$1 last;
								rewrite ^/wechat(.*)$ /wechat/index.php?s=$1 last;
								rewrite ^/api(.*)$ /api/index.php?s=$1 last;
								rewrite ^/(.*)$ /index.php?s=$1 last;
						break;
				}
				#autoindex on;
			}
			location ~ \.php(.*)$ {
				fastcgi_pass 127.0.0.1:9000;
					fastcgi_index index.php;
					fastcgi_split_path_info ^((?U).+\.php)(/?.+)$;
					fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
					fastcgi_param PATH_INFO $fastcgi_path_info;
					fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
					include fastcgi_params;
			}
}
```
### Apache

> 注意系统默认自带了.htaccess，所以环境如果是apache可以不用再配置

```
Options +FollowSymLinks
IndexIgnore */*
RewriteEngine on

# if a directory or a file exists, use it directly
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d

# otherwise forward it to index.php
RewriteRule . index.php
```

### IIS

> rule 部分配置

```
<rule name="backend" stopProcessing="true">
    <match url="^backend/(.*)" />
    <conditions logicalGrouping="MatchAll">
        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
    </conditions>
    <action type="Rewrite" url="backend/index.php/{R:1}" />
</rule>
<rule name="wechat" stopProcessing="true">
    <match url="^wechat/(.*)" />
    <conditions logicalGrouping="MatchAll">
        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
    </conditions>
    <action type="Rewrite" url="wechat/index.php/{R:1}" />
</rule>
<rule name="api" stopProcessing="true">
    <match url="^api/(.*)" />
    <conditions logicalGrouping="MatchAll">
        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
    </conditions>
    <action type="Rewrite" url="api/index.php/{R:1}" />
</rule>
<rule name="frontend" stopProcessing="true">
    <match url="^(.*)$" />
    <conditions logicalGrouping="MatchAll">
        <add input="{REQUEST_FILENAME}" matchType="IsFile" ignoreCase="false" negate="true" />
        <add input="{REQUEST_FILENAME}" matchType="IsDirectory" ignoreCase="false" negate="true" />
    </conditions>
    <action type="Rewrite" url="index.php/{R:1}" />
</rule>
```
