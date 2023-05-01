[Nginx](https://nginx.org/en/) is a performance-oriented HTTP server that can reverse proxy HTTP, HTTPS and mail-related (SMTP, POP3, IMAP) protocol links. It also provides load balancing and HTTP caching. Its design fully uses the asynchronous event model to reduce the overhead of context scheduling and improve server concurrency. It adopts a modular design and provides third-party modules with rich modules.

So about Nginx, there are these labels: "asynchronous", "event", "modular", "high performance", "high concurrency", "reverse proxy", "load balancing"

Linux system: `Centos 7 x64`  
Nginx version: `1.11.5`

<!--idoc:ignore:start-->

Table of contents
===
- [installation](#install)	
  - [Install dependencies](#install-dependencies)	
  - [download](#download)	
  - [compile and install](#compile-and-install)	
  - [nginx-test](#nginx-test)	
  - [set global nginx command](#set-global-nginx-commands)	
- [Mac install](#mac-installation)	
  - [Install nginx](#install-nginx)	
  - [Start service](#start-the-service)	
- [Boot Autostart](#automatically-start-at-boot)	
- [Operation and Maintenance](#operation-and-maintenance)	
  - [Service Management](#service-management)	
  - [Restart service firewall error solution](#restart-service-firewall-error-resolution)	
- [nginx uninstall](#nginx-uninstall)	
- [parameter description](#parameter-description)	
- [configuration](#configuration)	
  - [Common Regularity](#commonly-used-regular)	
  - [global variable](#global-variables)	
  - [Symbol Reference](#symbol-reference)	
  - [config file](#configuration-file)	
  - [built-in predefined variables](#built-in-predefined-variables)	
  - [reverse proxy](#reverse-proxy)	
  - [Load Balancing](#load-balancing)	
    - [RR](#rr)	
    - [weight](#weight)	
    - [ip_hash](#ip_hash)	
    - [fair](#fair)	
    - [url_hash](#url_hash)	
  - [shield ip](#shield-ip)	
- [Third-party module installation method](#third-party-module-installation-method)	
- [redirect](#redirect)	
  - [redirect entire site](#redirect-the-entire-site)	
  - [redirect single page](#redirect-single-page)	
  - [redirect entire subpath](#redirect-the-entire-subpath)	
- [performance](#performance)	
  - [content cache](#content-caching)	
  - [Gzip compression](#gzip-compression)	
  - [open file cache](#open-file-cache)	
  - [SSL cache](#ssl-caching)	
  - [upstream keepalive](#upstream-keepalives)	
  - [monitoring](#monitoring)	
- [Common usage scenarios](#common-usage-scenarios)	
  - [Cross-domain problem](#cross-domain-issues)	
  - [jump to domain with www](#jump-to-the-domain-with-www)	
  - [proxy forwarding](#proxy-forwarding)	
  - [Monitoring Status Information](#monitor-status-information)	
  - [proxy forwarding connection override](#proxy-forwarding-connection-replacement)	
  - [ssl configuration](#ssl-configuration)	
  - [Force redirect http to https](#forcibly-redirect-http-to-https)	
  - [two virtual hosts](#two-virtual-hosts)	
  - [Virtual host standard configuration](#virtual-host-standard-configuration)	
  - [Crawler Filter](#crawler-filtering)	
  - [Anti-leech](#anti-leech)	
  - [Virtual directory configuration](#virtual-directory-configuration)	
  - [Anti-theft map configuration](#anti-theft-map-configuration)	
  - [Shield .git and other files](#shield-git-and-other-files)	
  - [The domain name path can be accessed normally with or without the need](#whether-the-domain-name-path-is-added-or-not-it-can-be-accessed-normally)	
  - [cockpit](#cockpit)	
- [error question](#error-questions)	
- [Excellent article reference](#excellent-article-reference)

<!--idoc:ignore:end-->

## Install

### Install dependencies

> prce (redirection support) and openssl (https support, if you don't need https, you don't need to install it.)

```bash
yum install -y pcre-devel 
yum -y install gcc make gcc-c++ wget
yum -y install openssl openssl-devel 
```

When I installed CentOS 6.5, I chose "Basic Server". By default, neither of these two packages is fully installed, so just run and install both of them.

### download

[All versions of nginx are here](http://nginx.org/download/)

```bash
wget http://nginx.org/download/nginx-1.13.3.tar.gz
wget http://nginx.org/download/nginx-1.13.7.tar.gz

# If wget is not installed
# Download the compiled version
$ yum install wget

# Unzip the compressed package
tar zxf nginx-1.13.3.tar.gz
```

### Compile and install

Then enter the directory to compile and install, [configure parameter description] (#parameter description)

```bash
cd nginx-1.11.5
./configure

....
Configuration summary
  + using system PCRE library
  + OpenSSL library is not used
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
```

If the installation reports an error, such as: "C compiler cc is not found", this is the lack of a compilation environment, just install it **yum -y install gcc make gcc-c++ openssl-devel**

If there is no error message, you can perform the following installation:

```bash
make
make install
```

### nginx test

Running the following command will produce two results. Generally, nginx will be installed in the `/usr/local/nginx` directory

```bash
cd /usr/local/nginx/sbin/
./nginx -t

# nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
# nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```

### Set global nginx commands

```bash
vi ~/.bash_profile
```

Add the following content to the `~/.bash_profile` file

```bash
PATH=$PATH:$HOME/bin:/usr/local/nginx/sbin/
export PATH
```

Run the command **`source ~/.bash_profile`** to make the configuration take effect immediately. You can then run `nginx` commands globally.

## Mac installation

Mac OSX installation is very simple, first you need to install [Brew](https://brew.sh/), and quickly install `nginx` through `brew`.

### install nginx

```bash
brew install nginx
# Updating Homebrew...
# ==> Auto-updated Homebrew!
# Updated 2 taps (homebrew/core, homebrew/cask).
# ==> Updated Formulae
# ==> Installing dependencies for nginx: openssl, pcre
# ==> Installing nginx dependency: openssl
# ==> Downloading https://homebrew.bintray.com/bottles/openssl-1.0.2o_1.high_sierra.bottle.tar.gz
# ######################################################################## 100.0%
# ==> Pouring openssl-1.0.2o_1.high_sierra.bottle.tar.gz
# ==> Caveats
# A CA file has been bootstrapped using certificates from the SystemRoots
# keychain. To add additional certificates (e.g. the certificates added in
# the System keychain), place .pem files in
#   /usr/local/etc/openssl/certs
# 
# and run
#   /usr/local/opt/openssl/bin/c_rehash
#
# This formula is keg-only, which means it was not symlinked into /usr/local,
# because Apple has deprecated use of OpenSSL in favor of its own TLS and crypto libraries.
#
# If you need to have this software first in your PATH run:
#   echo 'export PATH="/usr/local/opt/openssl/bin:$PATH"' >> ~/.zshrc
# 
# For compilers to find this software you may need to set:
#     LDFLAGS:  -L/usr/local/opt/openssl/lib
#     CPPFLAGS: -I/usr/local/opt/openssl/include
# For pkg-config to find this software you may need to set:
#     PKG_CONFIG_PATH: /usr/local/opt/openssl/lib/pkgconfig
#
# ==> Summary
# 🍺  /usr/local/Cellar/openssl/1.0.2o_1: 1,791 files, 12.3MB
# ==> Installing nginx dependency: pcre
# ==> Downloading https://homebrew.bintray.com/bottles/pcre-8.42.high_sierra.bottle.tar.gz
# ######################################################################## 100.0%
# ==> Pouring pcre-8.42.high_sierra.bottle.tar.gz
# 🍺  /usr/local/Cellar/pcre/8.42: 204 files, 5.3MB
# ==> Installing nginx
# ==> Downloading https://homebrew.bintray.com/bottles/nginx-1.13.12.high_sierra.bottle.tar.gz
# ######################################################################## 100.0%
# ==> Pouring nginx-1.13.12.high_sierra.bottle.tar.gz
# ==> Caveats
# Docroot is: /usr/local/var/www
# 
# The default port has been set in /usr/local/etc/nginx/nginx.conf to 8080 so that
# nginx can run without sudo.
#
# nginx will load all files in /usr/local/etc/nginx/servers/.
#
# To have launchd start nginx now and restart at login:
#   brew services start nginx
# Or, if you don't wacd /usr/local/Cellar/nginx/1.13.12/n just run:
# cd /usr/local/Cellar/nginx/1.13.12/
```

### Start the service

Note that the default port is not `8080` Check to see if the port is occupied.

```bash
brew services start nginx
# http://localhost:8080/
```

## Automatically start at boot

**Boot self-starting method one:**

Edit the **vi /lib/systemd/system/nginx.service** file without creating a **touch nginx.service** Then modify the following content according to the specific situation and add it to the nginx.service file:

```bash
[Unit]
Description=nginx
After=network.target remote-fs.target nss-lookup.target

[Service]

Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t -c /usr/local/nginx/conf/nginx.conf
ExecStart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

- `[Unit]`: description of the service  
- `Description`: describe the service  
- `After`: describes the service category  
- Setting of `[Service]`service running parameters  
- `Type=forking` is the form of running in the background  
- `ExecStart` is the specific running command of the service  
- `ExecReload` is the restart command  
- `ExecStop` is the stop command  
- `PrivateTmp=True` indicates that a separate temporary space is allocated to the service  

Note: The start, restart, and stop commands of `[Service]` all require the use of absolute paths.

The relevant settings of service installation under the `[Install]` run level can be set to multi-user, that is, the system run level is `3`.

Save and exit.

Set the boot to make the configuration take effect:

```bash
# Start the nginx service
systemctl start nginx.service
# Stop autostart at boot
systemctl disable nginx.service
# View the current status of the service
systemctl status nginx.service
# View all started services
systemctl list-units --type=service
# restart the service
systemctl restart nginx.service
# Set boot autostart
systemctl enable nginx.service
# Output the following content to indicate success
Created symlink from /etc/systemd/system/multi-user.target.wants/nginx.service to /usr/lib/systemd/system/nginx.service.
```

```bash
systemctl is-enabled servicename.service # Query whether the service is started
systemctl enable *.service # start the service
systemctl disable *.service # cancel boot operation
systemctl start *.service # start the service
systemctl stop *.service # stop service
systemctl restart *.service # Restart the service
systemctl reload *.service # Reload the service configuration file
systemctl status *.service # Query service running status
systemctl --failed # Display services that failed to start
```

Note: * represents the name of a certain service, such as the service name of http is httpd

**Boot self-start method two:**

```bash
vi /etc/rc.local

# In the rc.local file, add the following command
/usr/local/nginx/sbin/nginx start
```

If you find that the self-starting script is not executed after booting, you need to confirm whether the access permission of the rc.local file is executable, because rc.local is not executable by default. Modify rc.local access rights and increase executable rights:

```bash
# /etc/rc.local is a soft connection of /etc/rc.d/rc.local,
chmod +x /etc/rc.d/rc.local
```

Open version [ed Hat NGINX Init Script](https://www.nginx.com/resources/wiki/start/topics/examples/redhatnginxinit/).

## Operation and maintenance

### Service Management

```bash
# start up
/usr/local/nginx/sbin/nginx

# restart
/usr/local/nginx/sbin/nginx -s reload

# close the process
/usr/local/nginx/sbin/nginx -s stop

# Shut down nginx smoothly
/usr/local/nginx/sbin/nginx -s quit

# Check the installation status of nginx,
/usr/local/nginx/sbin/nginx -V 
```

**Close the firewall, or add firewall rules to test**

```bash
service iptables stop
```

Or edit the configuration file:

```bash
vi /etc/sysconfig/iptables
```

Save after adding such a rule to open port 80:

```bash
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
```

Just restart the service:

```bash
service iptables restart
# command to view the current nat
iptables -t nat -L
```

### Restart service firewall error resolution


```bash
service iptables restart
# Redirecting to /bin/systemctl restart  iptables.service
# Failed to restart iptables.service: Unit iptables.service failed to load: No such file or directory.
```

In CentOS 7 or RHEL 7 or Fedora, the firewall is managed by **firewalld**, of course you can restore the traditional management method. Or use the new commands to manage.
If you use traditional, please execute the following command:

```bash
# Legacy commands
systemctl stop firewalld
systemctl mask firewalld
```

```bash
# install command
yum install iptables-services

systemctl enable iptables 
service iptables restart
```


## nginx uninstall

If installing via yum, use the following command to install.

```bash
yum remove nginx
```

Compile and install, delete the /usr/local/nginx directory
If a self-starting script is configured, it also needs to be deleted.


## Parameter Description

| Parameters | Description |
| ---- | ---- |
| --prefix=`<path>` | Nginx installation path. If not specified, defaults to /usr/local/nginx. |
| --sbin-path=`<path>` | Nginx executable installation path. It can only be specified during installation, if not specified, the default is `<prefix>`/sbin/nginx. |
| --conf-path=`<path>` | Path to the default nginx.conf when no -c option is given. If not specified, defaults to `<prefix>`/conf/nginx.conf. |
| --pid-path=`<path>` | The path of the default nginx.pid if no pid directive is specified in nginx.conf. If not specified, defaults to `<prefix>`/logs/nginx.pid. |
| --lock-path=`<path>` | Path to nginx.lock file. |
| --error-log-path=`<path>` | The default error log path when no error_log directive is specified in nginx.conf. If not specified, defaults to `<prefix>`/-logs/error.log. |
| --http-log-path=`<path>` | The default access log path when no access_log directive is specified in nginx.conf. If not specified, defaults to `<prefix>`/-logs/access.log. |
| --user=`<user>` | The default user used by nginx when no user directive is specified in nginx.conf. If not specified, defaults to nobody. |
| --group=`<group>` | The default group used by nginx when no user directive is specified in nginx.conf. If not specified, defaults to nobody. |
| --builddir=DIR | Specify the compiled directory |
| --with-rtsig_module | enable rtsig module |
| --with-select_module --without-select_module | Allow or not enable SELECT mode, if configure does not find a more suitable mode, such as: kqueue (sun os), epoll (linux kenel 2.6+), rtsig (- real-time signal ) or /dev/poll (a mode similar to select, the underlying implementation is basically the same as SELECT, and both use the rotation training method) SELECT mode will be the default installation mode|
| --with-poll_module --without-poll_module | Whether or not to enable the poll module. This module is enabled by, default if a more suitable method such as kqueue, epoll, rtsig or /dev/poll is not discovered by configure. |
| --with-http_ssl_module | Enable ngx_http_ssl_module. Enables SSL support and the ability to handle HTTPS requests. Requires OpenSSL. On Debian, this is libssl-dev. Enable HTTP SSL module, so that NGINX can support HTTPS requests. This module requires OPENSSL to have been installed, on DEBIAN it is libssl |
| --with-http_realip_module | 启用 ngx_http_realip_module |
| --with-http_addition_module | 启用 ngx_http_addition_module |
| --with-http_sub_module | 启用 ngx_http_sub_module |
| --with-http_dav_module | 启用 ngx_http_dav_module |
| --with-http_flv_module | 启用 ngx_http_flv_module |
| --with-http_stub_status_module | Enable "server status" page |
| --without-http_charset_module | 禁用 ngx_http_charset_module |
| --without-http_gzip_module | Disable ngx_http_gzip_module. If enabled, zlib is required. |
| --without-http_ssi_module | 禁用 ngx_http_ssi_module |
| --without-http_userid_module | 禁用 ngx_http_userid_module |
| --without-http_access_module | 禁用 ngx_http_access_module |
| --without-http_auth_basic_module | 禁用 ngx_http_auth_basic_module |
| --without-http_autoindex_module | 禁用 ngx_http_autoindex_module |
| --without-http_geo_module | 禁用 ngx_http_geo_module |
| --without-http_map_module | 禁用 ngx_http_map_module |
| --without-http_referer_module | 禁用 ngx_http_referer_module |
| --without-http_rewrite_module | Disable ngx_http_rewrite_module. PCRE is required if enabled. |
| --without-http_proxy_module | 禁用 ngx_http_proxy_module |
| --without-http_fastcgi_module | 禁用 ngx_http_fastcgi_module |
| --without-http_memcached_module | 禁用 ngx_http_memcached_module |
| --without-http_limit_zone_module | 禁用 ngx_http_limit_zone_module |
| --without-http_empty_gif_module | 禁用 ngx_http_empty_gif_module |
| --without-http_browser_module | 禁用 ngx_http_browser_module |
| --without-http_upstream_ip_hash_module | 禁用 ngx_http_upstream_ip_hash_module |
| --with-http_perl_module | 启用 ngx_http_perl_module |
| --with-perl_modules_path=PATH | specify the path of perl modules |
| --with-perl=PATH | Specify the path of the perl execution file |
| --http-log-path=PATH | Set path to the http access log |
| --http-client-body-temp-path=PATH | Set path to the http client request body temporary files |
| --http-proxy-temp-path=PATH | Set path to the http proxy temporary files |
| --http-fastcgi-temp-path=PATH | Set path to the http fastcgi temporary files |
| --without-http | disable HTTP server |
| --with-mail | Enable IMAP4/POP3/SMTP proxy module |
| --with-mail_ssl_module | enable ngx_mail_ssl_module |
| --with-cc=PATH | Specify path to C compiler |
| --with-cpp=PATH | specify path to C preprocessor |
| --with-cc-opt=OPTIONS | Additional parameters which will be added to the variable CFLAGS. With the use of the system library PCRE in FreeBSD, it is necessary to indicate --with-cc-opt="-I /usr/local/include". If we are using select() and it is necessary to increase the number of file descriptors, then this also can be assigned here: --with-cc-opt="-D FD_SETSIZE=2048". |
| --with-ld-opt=OPTIONS | Additional parameters passed to the linker. With the use of the system library PCRE in - FreeBSD, it is necessary to indicate --with-ld-opt="-L /usr/local/lib". |
| --with-cpu-opt=CPU | Compile for specific CPU, valid values ​​include: pentium, pentiumpro, pentium3, pentium4, athlon, opteron, amd64, sparc32, sparc64, ppc64 |
| --without-pcre | Disable the use of the PCRE library. It also disables the HTTP rewrite module. Regular expressions in the "location" configuration directive also require PCRE. |
| --with-pcre=DIR | Specifies the path to the source code for the PCRE library. |
| --with-pcre-opt=OPTIONS | Set additional options for PCRE building. |
| --with-md5=DIR | Set path to md5 library sources. |
| --with-md5-opt=OPTIONS | Set additional options for md5 building. |
| --with-md5-asm | Use md5 assembler sources. |
| --with-sha1=DIR | Set path to sha1 library sources. |
| --with-sha1-opt=OPTIONS | Set additional options for sha1 building. |
| --with-sha1-asm | Use sha1 assembler sources. |
| --with-zlib=DIR | Set path to zlib library sources. |
| --with-zlib-opt=OPTIONS | Set additional options for zlib building. |
| --with-zlib-asm=CPU | Use zlib assembler sources optimized for specified CPU, valid values are: pentium, pentiumpro |
| --with-openssl=DIR | Set path to OpenSSL library sources |
| --with-openssl-opt=OPTIONS | Set additional options for OpenSSL building |
| --with-debug | Enable debug logging |
| --add-module=PATH | Add in a third-party module found in directory PATH |


## configuration

The default configuration file in Centos is **/usr/local/nginx-1.5.1/conf/nginx.conf** We need to configure some files here. nginx.conf is the main configuration file, which consists of several parts, and each brace `{}` represents a part. Each line of instructions is terminated by a semicolon `;`, which marks the end of a line.

### Commonly used regular

| Regular | Description | Regular | Description |
| ---- | ---- | ---- | ---- | 
| `. ` | matches any character except newline | `$ ` | matches end of string |
| `? ` | Repeat 0 or 1 times | `{n} ` | Repeat n times |
| `+ ` | repeat 1 or more times | `{n,} ` | repeat n or more times |
| `*` | Repeat 0 or more times | `[c] ` | Match a single character c |
| `\d ` | Match digits | `[az]` | Match any of the lowercase letters of az |
| `^ ` | matches start of string | - | - |

### Global variables

| variable | description | variable | description |
| ---- | ---- | ---- | ---- | 
| $args | This variable is equal to the parameters in the request line, same as $query_string | $remote_port | The client's port. |
| $content_length | The Content-length field in the request header. | $remote_user | Username that has been authenticated by the Auth Basic Module. |
| $content_type | The Content-Type field in the request header. | $request_filename | The file path of the current request, generated by the root or alias command and the URI request. |
| $document_root | The value specified in the root directive for the current request. | $scheme | HTTP scheme (eg http, https). |
| $host | The request host header field, otherwise the server name. | $server_protocol | The protocol used by the request, usually HTTP/1.0 or HTTP/1.1. |
| $http_user_agent | client agent information | $server_addr | server address, this value can be determined after completing a system call. |
| $http_cookie | Client cookie information | $server_name | Server name. |
| $limit_rate | This variable can limit the connection rate. | $server_port | The port number on which requests arrive at the server. |
| $request_method | The action requested by the client, usually GET or POST. | $request_uri | The original URI containing the request parameters, without the hostname, eg: /foo/bar.php?arg=baz. |
| $remote_addr | IP address of the client. | $uri | The current URI without request parameters, $uri does not contain the hostname, such as /foo/bar.html. |
| $document_uri | given $uri homology. | - | - |

For example request: `http://localhost:3000/test1/test2/test.php`

$host：localhost  
$server_port：3000  
$request_uri：/test1/test2/test.php  
$document_uri：/test1/test2/test.php  
$document_root：/var/www/html  
$request_filename：/var/www/html/test1/test2/test.php  

### Symbol Reference

| Symbol | Description | Symbol | Description | Symbol | Description |
| ---- | ---- | ---- | ---- | ---- | ---- |
| k,K | kilobytes | m,M | megabytes | ms | milliseconds |
|s|second|m|minute|h|hour|
| d | day | w | week | M | month, 30 days |

For example, "8k", "1m" stands for byte count.  
For example, "1h 30m", "1y 6M". Represents "1 hour and 30 minutes", "1 year and 6 months".

### configuration file

The nginx configuration system consists of a main configuration file and other auxiliary configuration files. These configuration files are all plain text files, all located in the conf directory under the nginx installation directory.

Instructions are provided by each module of nginx, and different modules provide different instructions to implement configuration.
In addition to the form of Key-Value, directives also have scope directives.

The configuration information in nginx.conf is classified according to its logical meaning, that is, it is divided into multiple scopes, or called configuration instruction contexts. Different scopes contain one or more configuration items.

The following contextual directives are used more often:

| Directive |  Description | Contains Directive |
| ---- | ---- | ---- |
| main | Some parameters that have nothing to do with specific business functions (such as http service or email service proxy) when nginx is running, such as the number of working processes, running identity, etc. | user, worker_processes, error_log, events, http, mail |
| http | Some configuration parameters related to providing http services. For example: whether to use keepalive, whether to use gzip for compression, etc. | server |
Several virtual hosts are supported on the | server | http service. Each virtual host has a corresponding server configuration item, which contains the configuration related to the virtual host. When providing a proxy for mail service, several servers can also be established. Each server is distinguished by the address it listens to. | listen, server_name, access_log, location, protocol, proxy, smtp_auth, xclient |
| location | In the http service, a series of configuration items corresponding to some specific URLs. | index, root |
| mail | Some shared configuration items when implementing email-related SMTP/IMAP/POP3 proxies (because multiple proxies may be implemented and work on multiple listening addresses). | server, http, imap_capabilities |
|include| in order to enhance the readability of the configuration file, so that part of the configuration file can be reused. | - |
| valid_referers | Used to verify whether the Http request header Referer is valid. | - |
| try_files | Used in the server part, but the most common is used in the location part, it will try in the order of the given parameters, the first one that is matched will be used. | - |
| if | When using if directives in location blocks, in some cases it doesn't work as expected, in general avoid using if directives. | - |


For example, we refer to two configurations vhost/example.com.conf and vhost/gitlab.com.conf in **nginx.conf**, which are placed under a directory vhost I created myself. The nginx.conf configuration is as follows:

```nginx
worker_processes  1;
events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;
    server {
        listen       80;
        server_name  localhost;
        location / {
            root   html;
            index  index.html index.htm;
        }
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
    include  vhost/example.com.conf;
    include  vhost/gitlab.com.conf;
}
```


Simple configuration: example.conf

```nginx
server {
    #Listen on port 80
    listen       80;
    server_name server.com app.server.com; # specify the domain name here
    index index.html index.htm; # Specify the default entry page here
    root /home/www/app.server.com; # Specify the directory here
}
```

### Built-in predefined variables

Nginx provides many predefined variables, and variables can also be set by using set. You can use predefined variables in the if and also pass them to the proxy server. The following are some common predefined variables, [see for more details](http://nginx.org/en/docs/varindex.html)

| variable name | value |
| ----  | ---- |
| $args_name | the name parameter in the request |
| $args | All request parameters |
| $query_string | alias for $args |
| $content_length | The value of the request header Content-Length |
| $content_type | The value of the request header Content-Type |
| $host | If there is currently a Host, it is the value of the request header Host; if there is no such header, then the value is equal to the value of the server_name matching the request |
| $remote_addr | client's IP address |
| $request | The complete request, received from the client, including Http request method, URI, Http protocol, header, request body |
| $request_uri | URI of the complete request, the request from the client, including parameters |
| $scheme | The current request's protocol |
| $uri | The normalized URI of the current request |

### reverse proxy

A reverse proxy is a web server that accepts a connection request from a client, forwards the request to an upstream server, and returns the result obtained from the server to the connected client. The following simple reverse proxy example:

```nginx
server {  
  listen       80;                                                        
  server_name  localhost;                                              
  client_max_body_size 1024M; # The maximum number of bytes of a single file that a client can request

  location / {
    proxy_pass                         http://localhost:8080;
    proxy_set_header Host              $host:$server_port;
    proxy_set_header X-Forwarded-For $remote_addr; # HTTP request end real IP
    proxy_set_header X-Forwarded-Proto $scheme; # In order to correctly identify whether the protocol issued by the actual user is http or https
  }
}
```

Complex configuration: gitlab.com.conf.

```nginx
server {
    #Listen on port 80
    listen       80;
    server_name  git.example.com;
    location / {
        proxy_pass   http://localhost:3000;
        #The following are some reverse proxy configurations that can be deleted
        proxy_redirect             off;
        #The back-end web server can obtain the user's real IP through X-Forwarded-For
        proxy_set_header           Host $host;
        client_max_body_size 10m; #The maximum number of bytes of a single file that a client can request
        client_body_buffer_size 128k; #The buffer agent buffers the maximum number of bytes requested by the client
        proxy_connect_timeout 300; #nginx and backend server connection timeout (proxy connection timeout)
        proxy_send_timeout 300; #Backend server data return time (proxy sending timeout)
        proxy_read_timeout 300; #After the connection is successful, the response time of the backend server (proxy receiving timeout)
        proxy_buffer_size 4k; #Set the buffer size of the proxy server (nginx) to save user header information
        proxy_buffers 4 32k; #proxy_buffers buffer, if the average web page is below 32k, set it like this
        proxy_busy_buffers_size 64k; #buffer size under high load (proxy_buffers*2)
    }
}
```

In the configuration of proxying to the upstream server, the most important thing is the proxy_pass directive. The following are some commonly used directives in the proxy module:

| Instructions | Instructions |
| ---- | ---- |
| proxy_connect_timeout | The maximum waiting time for Nginx to connect to the upstream server from accepting the request |
| proxy_send_timeout | Backend server data return time (proxy send timeout) |
| proxy_read_timeout | After the connection is successful, the response time of the backend server (proxy receiving timeout) |
| proxy_cookie_domain | Replaces the domain attribute of the Set-Cookie header from the upstream server |
| proxy_cookie_path | Replaces the path attribute of the Set-Cookie header from the upstream server |
| proxy_buffer_size | Set the buffer size of the proxy server (nginx) to save user header information |
| proxy_buffers | proxy_buffers buffer, how many k is the average web page |
| proxy_set_header | Rewrite the content sent to the upstream server header, or by setting the value of a certain header to an empty string instead of sending a certain header |
| proxy_ignore_headers | This directive disables processing of responses from proxy servers. |
| proxy_intercept_errors | Makes nginx block responses with HTTP response code 400 or higher. |

### load balancing

The upstream directive enables a new configuration section in which a set of upstream servers is defined. These servers may be set with different weights, or may be marked as down for server maintenance.

```nginx
upstream gitlab {
    ip_hash;
    # The load balance of upstream, weight is the weight, and the weight can be defined according to the machine configuration. The weight parameter represents the weight value, the higher the weight value, the greater the probability of being assigned.
    server 192.168.122.11:8081 ;
    server 127.0.0.1:82 weight=3;
    server 127.0.0.1:83 weight=3 down;
    server 127.0.0.1:84 weight=3; max_fails=3  fail_timeout=20s;
    server 127.0.0.1:85 weight=4;;
    keepalive 32;
}
server {
    #Listen on port 80
    listen       80;
    server_name  git.example.com;
    location / {
        proxy_pass http://gitlab; #Set a proxy here, the same name as upstream
        #The following are some reverse proxy configurations that can be deleted
        proxy_redirect             off;
        #The back-end web server can obtain the user's real IP through X-Forwarded-For
        proxy_set_header           Host $host;
        proxy_set_header           X-Real-IP $remote_addr;
        proxy_set_header           X-Forwarded-For $proxy_add_x_forwarded_for;
        client_max_body_size 10m; #The maximum number of bytes of a single file that a client can request
        client_body_buffer_size 128k; #The buffer agent buffers the maximum number of bytes requested by the client
        proxy_connect_timeout 300; #nginx and backend server connection timeout (proxy connection timeout)
        proxy_send_timeout 300; #Backend server data return time (proxy sending timeout)
        proxy_read_timeout 300; #After the connection is successful, the response time of the backend server (proxy receiving timeout)
        proxy_buffer_size 4k; #Set the buffer size of the proxy server (nginx) to save user header information
        proxy_buffers 4 32k;# Buffer, if the average web page is below 32k, set it like this
        proxy_busy_buffers_size 64k; #buffer size under high load (proxy_buffers*2)
        proxy_temp_file_write_size 64k; #Set the size of the cache folder, if it is larger than this value, it will be transmitted from the upstream server
    }
}
```

Each request is allocated to different backend servers one by one in chronological order. If the backend server is down, it can be automatically eliminated.

**Load Balancing:**

The upstream module can use 3 load balancing algorithms: round robin, IP hash, and minimum number of connections.

**Polling:** The polling algorithm is used by default, no configuration instructions are required to activate it, it is based on the principle of who is next in the queue to ensure that access is evenly distributed to each upstream server;  
**IP hash:** Activated by the ip_hash command, Nginx uses the first 3 bytes of the IPv4 address or the entire IPv6 address as a hash key, and the same IP address can always be mapped to the same upstream server;  
**Minimum number of connections:** Activated by the least_conn command, this algorithm connects by selecting an upstream server with the least active number. If the processing capabilities of the upstream servers are different, it can be explained by configuring the weight for the server. This algorithm will take into account the weighted minimum number of connections of different servers.

#### RR

**Simple configuration**, here I have configured 2 servers, of course it is actually one, but the port is different, and the 8081 server does not exist, that is to say, it cannot be accessed, but we visit `http: //localhost`, there will be no problem, it will jump to `http://localhost:8080` by default, because Nginx will automatically judge the status of the server, if the server is inaccessible (server hangs), it will It will not jump to this server, so it also avoids the situation that a server hangs and affects the use. Since Nginx defaults to the RR policy, we do not need other more settings

```nginx
upstream test {
    server localhost:8080;
    server localhost:8081;
}
server {
    listen       81;
    server_name  localhost;
    client_max_body_size 1024M;
 
    location / {
        proxy_pass http://test;
        proxy_set_header Host $host:$server_port;
    }
}
```

**The core code of load balancing is**

```nginx
upstream test {
    server localhost:8080;
    server localhost:8081;
}
```

#### Weights

Specify the polling probability, the weight is proportional to the access ratio, and it is used when the performance of the backend server is uneven. For example

```nginx
upstream test {
    server localhost:8080 weight=9;
    server localhost:8081 weight=1;
}
```

Then 10 times will generally only visit 8081 once, and 9 times will visit 8080

#### ip_hash

The above two methods have a problem, that is, when the next request comes, the request may be distributed to another server. When our program is not stateless (using session to save data), there is a big It’s very problematic. For example, if you save the login information in the session, you need to log in again when jumping to another server. So many times we need a client to only access one server, then we need to use iphash, iphash Each request is allocated according to the hash result of the access ip, so that each visitor accesses a backend server regularly, which can solve the session problem.

```nginx
upstream test {
    ip_hash;
    server localhost:8080;
    server localhost:8081;
}
```

#### fair

This is a third-party module that allocates requests according to the response time of the back-end server, and assigns priority to those with short response times.

```nginx
upstream backend {
    fair;
    server localhost:8080;
    server localhost:8081;
}
```

#### url_hash

This is a third-party module that allocates requests according to the hash result of the accessed url, so that each url is directed to the same backend server, and it is more effective when the backend server is cached. Add a hash statement to the upstream. Other parameters such as weight cannot be written in the server statement. hash_method is the hash algorithm used

```nginx
upstream backend {
    hash $request_uri;
    hash_method crc32;
    server localhost:8080;
    server localhost:8081;
}
```

The above five types of load balancing are applicable to different situations, so you can choose which strategy mode to use according to the actual situation, but fair and url_hash need to install a third-party module to use

**server command optional parameters:**

1. weight: Set the access weight of a server, the higher the value, the more requests received;
2. fail_timeout: The server must provide a response within this specified time. If no response is received within this time, the server will be marked as down;
3. max_fails: Set the maximum number of attempts to connect to a server within the fail_timeout time. If this number is exceeded, the server will be marked as down;
4. down: Mark a server that no longer accepts any requests;
5. backup: Once other servers are down, the machine with this mark will receive the request.

**keepalive command:**

The Nginx server will maintain a connection to the upstream server for each worker.

### Shield ip

Add the following configuration to the nginx configuration file `nginx.conf`, which can be placed in http, server, location, limit_except statement blocks, and relative paths need to be paid attention to. In this example, `nginx.conf` and `blocksip.conf` are in the same in the directory.

```nginx
include blockip.conf;
```

Enter the content in blockip.conf, such as:

```nginx
deny 165.91.122.67;

deny IP; # Block a single ip access
allow IP; # Allow single ip access
deny all; # Block all ip access
allow all; # allow all ip access
deny 123.0.0.0/8 # Shield the entire segment, that is, commands accessed from 123.0.0.1 to 123.255.255.254
deny 124.45.0.0/16 # Shield the IP segment, that is, the command accessed from 123.45.0.1 to 123.45.255.254
deny 123.45.6.0/24 # Shield the IP segment, that is, the command accessed from 123.45.6.1 to 123.45.6.254

# If you want to implement such an application, except for a few IPs, all others are rejected
allow 1.1.1.1; 
allow 1.1.1.2;
deny all; 
```

## Third-party module installation method

```
./configure --prefix=/your installation directory --add-module=/third-party module directory
```

## Redirect

- `permanent` Permanent redirection. The status code in the request log is 301
- `redirect` Temporary redirection. The status code in the request log is 302

### Redirect the entire site

```nginx
server {
    server_name old-site.com
    return 301 $scheme://new-site.com$request_uri;
}
```

### Redirect single page

```nginx
server {
    location = /oldpage.html {
        return 301 http://example.org/newpage.html;
    }
}
```

### Redirect the entire subpath

```nginx
location /old-site {
    rewrite ^/old-site/(.*) http://example.org/new-site/$1 permanent;
}
```

## Performance

### Content caching

Allows the browser to cache static content essentially permanently. Nginx will set the Expires and Cache-Control headers for you.

```nginx
location /static {
    root /data;
    expires max;
}
```

Use -1 if requesting that the browser should never cache responses (e.g. for tracking requests).

```nginx
location = /empty.gif {
    empty_gif;
    expires -1;
}
```

### Gzip compression

```nginx
gzip  on;
gzip_buffers 16 8k;
gzip_comp_level 6;
gzip_http_version 1.1;
gzip_min_length 256;
gzip_proxied any;
gzip_vary on;
gzip_types
    text/xml application/xml application/atom+xml application/rss+xml application/xhtml+xml image/svg+xml
    text/javascript application/javascript application/x-javascript
    text/x-json application/json application/x-web-app-manifest+json
    text/css text/plain text/x-component
    font/opentype application/x-font-ttf application/vnd.ms-fontobject
    image/x-icon;
gzip_disable  "msie6";
```

### Open file cache

```nginx
open_file_cache max=1000 inactive=20s;
open_file_cache_valid 30s;
open_file_cache_min_uses 2;
open_file_cache_errors on;
```

### SSL caching

```nginx
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
```

### Upstream Keepalives

```nginx
upstream backend {
    server 127.0.0.1:8080;
    keepalive 32;
}
server {
    ...
    location /api/ {
        proxy_pass http://backend;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
    }
}
```

### Monitoring

Use `ngxtop` to analyze nginx access logs in real time, and output the processing results to the terminal. The function is similar to the system command top. All examples read the access log location and format of the nginx configuration file. If you want to specify an access log file and/or log format, use the -f and -a options.

Note: The `/usr/local/nginx/conf/nginx.conf` log file must be an absolute path in the nginx configuration.

```bash
# install ngxtop
pip install ngxtop

# real-time status
ngxtop
# The path of the first 10 requests with status 404:
ngxtop top request_path --filter 'status == 404'

# Send the top 10 requests with the most total bytes
ngxtop --order-by 'avg(bytes_sent) * count'

# Top 10 IPs, for example, who attacked you the most
ngxtop --group-by remote_addr

# Print requests with 4xx or 5xx status, along with status and http referer
ngxtop -i 'status >= 400' print request status http_referer

# Average body bytes sent by 200 request path responses starting with 'foo':
ngxtop avg bytes_sent --filter 'status == 200 and request_path.startswith("foo")'

# Analyze apache access logs from a remote machine using the "common" log format
ssh remote tail -f /var/log/apache2/access.log | ngxtop -f common
```

## Common usage scenarios

### Cross domain issues

At work, sometimes some interfaces do not support cross-domain. At this time, you can simply add add_headers to support cors cross-domain. The configuration is as follows:

```nginx
server {
  listen 80;
  server_name api.xxx.com;
    
  add_header 'Access-Control-Allow-Origin' '*';
  add_header 'Access-Control-Allow-Credentials' 'true';
  add_header 'Access-Control-Allow-Methods' 'GET,POST,HEAD';

  location / {
    proxy_pass http://127.0.0.1:3000;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host  $http_host;    
  } 
}
```

There is another way to change the header information above, using the [rewrite](http://nginx.org/en/docs/http/ngx_http_rewrite_module.html) directive to redirect the URI to solve cross-domain problems.

```nginx
upstream test {
  server 127.0.0.1:8080;
  server localhost:8081;
}
server {
  listen 80;
  server_name api.xxx.com;
  location / { 
    root html; #To request the files in the ../html folder
    index index.html index.htm; #Home page response address
  }
  # Used to intercept requests, matching any address starting with /api/,
  # After the match is met, stop searching for regular expressions.
  location ^~/api/{ 
    # stands for rewriting intercepted incoming requests, and can only work on the string after the domain name except the passed parameters,
    # For example, www.a.com/proxy/api/msg?meth=1&par=2 rewrites, only rewrites /proxy/api/msg.
    # The parameter after rewrite is a simple regular ^/api/(.*)$,
    # $1 represents the first () in the regex, $2 represents the value of the second (), and so on.
    rewrite ^/api/(.*)$ /$1 break;
    
    # Proxy requests to other hosts
    # The difference between http://www.b.com/ and http://www.b.com is as follows
    # If your request address is his http://server/html/test.jsp
    # Configuration 1: http://www.b.com/ followed by "/"
    # Use the reverse proxy to access http://www.b.com/html/test.jsp
    # Configuration 1: There is no "/" behind http://www.b.com
    # Use the reverse proxy to access http://www.b.com/test.jsp
    proxy_pass http://test;

    # If the proxy_pass URL is http://a.xx.com/platform/ this case
    # proxy_cookie_path should be set to /platform/ / (note that there is a space between the two slashes).
    proxy_cookie_path /platfrom/ /;

    # http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass_header
    # Set the Cookie header via
    proxy_pass_header Set-Cookie;
  } 
}
```

### Jump to the domain with www

```nginx
server {
    listen 80;
    # Configure a normal domain name with www
    server_name www.x.com;
    root /home/www/user/download;
    location / {
        try_files $uri $uri/ /index.html =404;
    }
}
server {
    # This should be placed below,
    # Permanently redirect x.com without www to https://www.x.com
    server_name x.com;
    rewrite ^(.*) https://www.x.com$1 permanent;
}
```

### Proxy Forwarding

```nginx
upstream server-api{
    # api proxy service address
    server 127.0.0.1:3110;    
}
upstream server-resource{
    # Static resource proxy service address
    server 127.0.0.1:3120;
}
server {
    listen       3111;
    server_name localhost; # specify the domain name here
    root /home/www/server-statics;
    # Match the reverse proxy of the api route to the API service
    location ^~/api/ {
        rewrite ^/(.*)$ /$1 break;
        proxy_pass http://server-api;
    }
    # Assume that the verification code here is also in the API service
    location ^~/captcha {
        rewrite ^/(.*)$ /$1 break;
        proxy_pass http://server-api;
    }
    # Assume all your image resources are on another service
    location ^~/img/ {
        rewrite ^/(.*)$ /$1 break;
        proxy_pass http://server-resource;
    }
    # The route is at the front end, but there is no real route at the back end, and /index.html is returned to the 404 status page where the route does not exist
    # This method uses scenarios, when you write React or Vue projects, there is no real route
    location / {
        try_files $uri $uri/ /index.html =404;
        # ^ spaces are important
    }
}
```

### Monitor status information

Use `nginx -V` to see if there is `with-http_stub_status_module` the module.

> `nginx -V` where `V` is uppercase, if it is lowercase `v`, that is `nginx -v`, there will be no modules, only the version of `nginx` will appear

```nginx
location /nginx_status {
    stub_status on;
    access_log off;
}
```

Access through http://127.0.0.1/nginx_status shows the following results.

```bash
Active connections: 3
server accepts handled requests
 7 7 5 
Reading: 0 Writing: 1 Waiting: 2 
```

1. Active connection (line 1)

The current number of connections established with http, including waiting client connections: 3

2. The server accepts the processed request (lines 2~3)

Total number of client connections accepted: 7  
Total number of client connections handled: 7  
The total number of client requests: 5  

3. Read other messages (line 4)

Currently, nginx reads request connections  
Currently, nginx writes the response back to the client  
How many idle clients are currently requesting connections  

### Proxy Forwarding Connection Replacement

```nginx
location ^~/api/upload {
    rewrite ^/(.*)$ /wfs/v1/upload break;
    proxy_pass http://wfs-api;
}
```

### ssl configuration

Hypertext Transfer Security Protocol (abbreviation: HTTPS, English: Hypertext Transfer Protocol Secure) is a combination of Hypertext Transfer Protocol and SSL/TLS, which is used to provide encrypted communication and authentication of network server identity. HTTPS connections are often used for transaction payments on the World Wide Web and transmission of sensitive information in enterprise information systems. HTTPS should not be confused with Secure Hypertext Transfer Protocol (S-HTTP) as defined in RFC 2660. HTTPS is currently the first choice for all websites that focus on privacy and security. With the continuous development of technology, HTTPS websites are no longer the patent of large websites. All ordinary personal webmasters and bloggers can build a secure encrypted website by themselves. website.


Create an SSL certificate, if you purchased the certificate, you can download it directly

```bash
sudo mkdir /etc/nginx/ssl
# Create an SSL key key and an X509 certificate file with a validity period of 100 years and an encryption strength of RSA2048.
sudo openssl req -x509 -nodes -days 36500 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt
# The above command will have the following content to fill in
Country Name (2 letter code) [AU]:US
State or Province Name (full name) [Some-State]:New York
Locality Name (eg, city) []:New York City
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Bouncy Castles, Inc.
Organizational Unit Name (eg, section) []:Ministry of Water Slides
Common Name (e.g. server FQDN or YOUR name) []:your_domain.com
Email Address []:admin@your_domain.com
```

Create a self-signed certificate

```bash
First, create directories for certificates and private keys
# mkdir -p /etc/nginx/cert
# cd /etc/nginx/cert
To create a server private key, the command will ask you to enter a passphrase:
# openssl genrsa -des3 -out nginx.key 2048
Create a certificate signing request (CSR):
# openssl req -new -key nginx.key -out nginx.csr
Remove the required password when loading Nginx with SSL support and using the above private key:
# cp nginx.key nginx.key.org
# openssl rsa -in nginx.key.org -out nginx.key
Finally sign the certificate using the above private key and CSR:
# openssl x509 -req -days 365 -in nginx.csr -signkey nginx.key -out nginx.crt
```

View current nginx compilation options

```
sbin/nginx -V
```

output the following

```
nginx version: nginx/1.7.8
built by gcc 4.4.7 20120313 (Red Hat 4.4.7-4) (GCC)
TLS SNI support enabled
configure arguments: --prefix=/usr/local/nginx-1.7.8 --with-http_ssl_module --with-http_spdy_module --with-http_stub_status_module --with-pcre
```

If the dependent module does not exist, you can enter the installation directory and enter the following command to recompile and install.

```bash
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module
```

`make` is required after running (do not make install)

```bash
# Backup nginx binary files
cp -rf /usr/local/nginx/sbin/nginx　 /usr/local/nginx/sbin/nginx.bak
# Override the nginx binary
cp -rf objs/nginx   /usr/local/nginx/sbin/
```

HTTPS server

```nginx
server {
    listen       443 ssl;
    server_name  localhost;

    ssl_certificate /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx.key;
    # Prohibit the server version in the header to prevent hackers from exploiting version vulnerabilities
    server_tokens off;
    # Set the type and size of the ssl/tls session cache. If this parameter is set, it is generally shared, and buildin may parameterize memory fragmentation. The default is none, which is similar to off, and disables the cache. For example, shared:SSL:10m means that all my nginx working processes share the ssl session cache. According to the official website, 1M can store about 4000 sessions.
    ssl_session_cache    shared:SSL:1m; 

    # The client can reuse the expiration time of the ssl parameter in the session cache. The default 5 minutes of the intranet system is too short, and it can be set to 30m, that is, 30 minutes or even 4h.
    ssl_session_timeout  5m; 

    # Select the cipher suite, the suite (and order) supported by different browsers may be different.
    # This specifies the writing method that the OpenSSL library can recognize. You can view the supported algorithms through openssl -v cipher 'RC4:HIGH:!aNULL:!MD5' (followed by the suite encryption algorithm you specified).
    ssl_ciphers  HIGH:!aNULL:!MD5;

    # When setting the negotiation encryption algorithm, the encryption suite of our server is used first, rather than the encryption suite of the client browser.
    ssl_prefer_server_ciphers  on;

    location / {
        root   html;
        index  index.html index.htm;
    }
}
```

### Forcibly redirect http to https

```nginx
server {
    listen       80;
    server_name  example.com;
    rewrite ^ https://$http_host$request_uri? permanent; # Forcibly redirect http to https
    # Enable or disable emitting nginx version on error pages and in the "Server" response header field. Prevent hackers from exploiting version vulnerabilities
    server_tokens off;
}
```

### Two virtual hosts

Pure static-html support

```nginx
http {
    server {
        listen          80;
        server_name     www.domain1.com;
        access_log      logs/domain1.access.log main;
        location / {
            index index.html;
            root  /var/www/domain1.com/htdocs;
        }
    }
    server {
        listen          80;
        server_name     www.domain2.com;
        access_log      logs/domain2.access.log main;
        location / {
            index index.html;
            root  /var/www/domain2.com/htdocs;
        }
    }
}
```

### Virtual host standard configuration

```nginx
http {
  server {
    listen          80 default;
    server_name     _ *;
    access_log      logs/default.access.log main;
    location / {
       index index.html;
       root  /var/www/default/htdocs;
    }
  }
}
```

### Crawler filtering

Filter requests according to `User-Agent`, through a simple regular expression, you can filter crawler requests that do not meet the requirements (primary crawlers).

> `~*` means a case-insensitive regular match

```nginx
location / {
    if ($http_user_agent ~* "python|curl|java|wget|httpclient|okhttp") {
        return 503;
    }
    # normal processing
    # ...
}
```

### Anti-leech

```nginx
location ~* \.(gif|jpg|png|swf|flv)$ {
   root html
   valid_referers none blocked *.nginx.com;
   if ($invalid_referer) {
     rewrite ^/ www.nginx.com
     #return 404;
   }
}
```

### Virtual directory configuration

The directory specified by alias is accurate, and root is the upper-level directory of the specified directory, and the upper-level directory must contain a directory with the same name specified by location.

```nginx
location /img/ {
    alias /var/www/image/;
}
# When accessing files in the /img/ directory, ningx will automatically go to the /var/www/image/ directory to find files
location /img/ {
    root /var/www/image;
}
# When accessing files in the /img/ directory, nginx will go to the /var/www/image/img/ directory to find files. ]
```

### Anti-theft map configuration

```nginx
location ~ \/public\/(css|js|img)\/.*\.(js|css|gif|jpg|jpeg|png|bmp|swf) {
    valid_referers none blocked *.jslite.io;
    if ($invalid_referer) {
        rewrite ^/  http://x.com/piratesp.png;
    }
}
```

### Shield .git and other files

```nginx
location ~ (.git|.gitattributes|.gitignore|.svn) {
    deny all;
}
```

### Whether the domain name path is added or not, it can be accessed normally

```bash
http://x.com/api/index.php?a=1&name=wcj
                                  ^ with suffix

http://x.com/api/index?a=1&name=wcj
                                 ^ without suffix
```

The nginx rewrite rules are as follows:

```nginx
rewrite ^/(.*)/$ /index.php?/$1 permanent;
if (!-d $request_filename){
        set $rule_1 1$rule_1;
}
if (!-f $request_filename){
        set $rule_1 2$rule_1;
}
if ($rule_1 = "21"){
        rewrite ^/ /index.php last;
}
```

### cockpit

https://github.com/cockpit-project/cockpit

```nginx
server{
    listen 80;
    server_name cockpit.xxxxxxx.com;
    return 301 https://$server_name$request_uri;
}
 
server {
    listen 443 ssl;
    server_name cockpit.xxxxxxx.com;
 
    #ssl on;
    ssl_certificate /etc/nginx/cert/cockpit.xxxxxxx.com.pem;
    ssl_certificate_key /etc/nginx/cert/cockpit.xxxxxxx.com.key;
 
    location / {
        root /;
        index index.html;
        proxy_redirect off;
        proxy_pass http://websocket;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;
    }
}
```

At this time, if you enter the domain name, you can see the login page, but after logging in, no content is displayed, and the page is all white. Here you need to modify `cockpit.conf`.

```bash
sudo vim /etc/cockpit/cockpit.conf
```

Refer to the following configuration modification, note that the domain name is replaced with `your_domain_host`:

```toml
[WebService]
Origins = https://cockpit.xxxxxxx.com https://127.0.0.1:9090
ProtocolHeader = X-Forwarded-Proto
AllowUnencrypted = true
```

## Error Questions

```bash
The plain HTTP request was sent to HTTPS port
```

The solution, `fastcgi_param HTTPS $https if_not_empty` add this rule,

```nginx
server {
    listen 443 ssl; # Pay attention to this rule
    server_name  my.domain.com;
    
    fastcgi_param HTTPS $https if_not_empty;
    fastcgi_param HTTPS on;

    ssl_certificate /etc/ssl/certs/your.pem;
    ssl_certificate_key /etc/ssl/private/your.key;

    location / {
        # Your config here...
    }
}
```

## Nginx module

- [Nginx Office Hours](https://gitlab.com/rbdr/ngx_http_office_hours_filter_module) An nginx module that allows you to provide access to websites only during office hours.

## Excellent article reference
- [Inside NGINX: How We Designed for Performance and Scale](https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/)
- [Inside NGINX: How We Designed for Performance & Scale](https://www.nginx.com/blog/inside-nginx-how-we-designed-for-performance-scale/)

## License

Licensed under the MIT License.

