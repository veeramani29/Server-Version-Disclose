# How to disclose server version in nginx
Extend and override nginx config for Elastic Beanstalk Amazon Linux 2

To protect our server we need disclose our server version/name from the attack/hackers.

There are three ways we can do.

1.Server version discloses (Server : nginx/12)

2.Remove Server property from the response header (Server : nginx/12)

3.Custom Server name (Server: Voot-server)

 

**Amazon Linux 2 Instance (elasticbeanstalk nginx server structure )**
```

~/my-app/
|-- web.jar
|-- Procfile
|-- readme.md
|-- .ebextensions/
|   |-- options.config        # Option settings
|   `-- cloudwatch.config     # Other .ebextensions sections, for example files and container commands
`-- .platform/
    |-- nginx/                # Proxy configuration
    |   |-- nginx.conf (*override nginx conf )
    |   `-- conf.d/ (*extend nginx conf )
    |       `-- custom.conf (*server context)
    |        -- elasticbeanstalk
    |           -- app.conf (*location context)
    |-- hooks/                # Application deployment hooks
    |   |-- prebuild/
    |   |   |-- 01_set_secrets.sh
    |   |   
    |   |-- predeploy/
    |   |   `-- 01_some_service_stop.sh
    |   `-- postdeploy/
    |       |-- 01_set_tmp_file_permissions.sh
    |       
    |       
    `-- confighooks/          # Configuration deployment hooks
        |-- prebuild/
        |   `-- 01_set_secrets.sh
        |-- predeploy/
        |   `-- 01_some_service_stop.sh
        `-- postdeploy/
            |-- 01_run_something_after_config_deployment.sh
 ```           
nginx Config Contexts

```
http{
  include       conf.d/*.conf; (*server context all conf should be conf.d dir)
  server{
    include conf.d/elasticbeanstalk/*.conf; (*location context all conf should be conf.d/elasticbeanstalk dir)
    
  }
}
```
Example location context
```
location /{
}
```
Structure would be
```
http{
  server{
    location /{
    }
  }
}
```
**For Step1:**

.platform/nginx/conf.d/custom.conf

```
server_tokens off;
```
**For Step2:**

.platform/nginx/conf.d/custom.conf

```
proxy_pass_header Server;
```
**For Step3:**

i).ebextensions/ngnix-extra-pack-install.config (Write  configuration rule to download nginc extras moduls (YMAL syntax )

ii) To override exists nginx configuration include 

```
load_module modules/ngx_http_headers_more_filter_module.so;
```
This to files top nginx.conf

.platform/nginx/nginx.conf

iii) Then set custom server name on  conf.d directory 

.platform/nginx/conf.d/01-server-tokens.conf

```
more_set_headers 'Server: voot-server';
```
 

Mac OS ZIP remove MACOSX dir
```
 zip -d filename.zip __MACOSX/\*
```
 


 

**Docs Link:**

[
Customizing software on Linux servers - AWS Elastic Beanstalk ](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/customize-containers-ec2.html)

[
Extending Elastic Beanstalk Linux platforms - AWS Elastic Beanstalk ](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/platforms-linux-extend.html)

[Remove Nginx server header with Nginx Headers More Filter Module - SERVERDIARY  ](https://serverdiary.com/linux/remove-nginx-server-header-with-nginx-headers-more-filter-module/)
