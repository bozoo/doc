CIS Apache 2.2 Benchmark configuration
======================================

Source: https://benchmarks.cisecurity.org/downloads/browse/

Apache User
-----------

 - User: `apache`
 - Shell: `/sbin/nologin`
 - Status: Locked (`passwd -l`)
 - $APACHE_PREFIX (`/etc/httpd`): owned by root/root (755 and 644 for files)

Default Pages
-------------

 - Remove default index.html
 - Remove apache user manual
 - Remove server-status, server-info and perl-status

CGI
---

 - Remove CGI printenv
 - Remove test-cgi

Use SSL/TLS
-----------

1. Install mod_ssl and/or mod_nss:
1. Install a Valid Trusted Certificate
1. Protect the Servers Private Key
1. Restrict Weak SSL Protocols and Ciphers
1. Restrict Insecure SSL Renegotiation

httpd.conf
----------

```apacheconf
# Disable Modules
#LoadModule mod_auth*
#LoadModule mod_ldap*
#LoadModule mod_dav.so
#LoadModule mod_dav_fs.so
#LoadModule mod_status.so
#LoadModule mod_autoindex.so
#LoadModule mod_proxy.so
#LoadModule mod_proxy_balancer.so
#LoadModule mod_proxy_ftp.so
#LoadModule mod_proxy_http.so
#LoadModule mod_proxy_connect.so
#LoadModule mod_proxy_ajp.so
#LoadModule mod_userdir.so
#LoadModule mod_info.so

# Enable Modules
LoadModule mod_log_config.so

# Restrict access to root directory
<Directory>
  Order deny,allow
  Deny from all
  AllowOverride none
  Option none
</Directory>

# Restrict options and .htaccess for all directories
#AllowOverride none
#Option none

# Restrict access to .ht files
<FilesMatch "^\.ht">
    Order allow,deny
</FilesMatch>

# Block all files by default, unless specifically allowed
<FilesMatch "^.*$">
    Order Deny,Allow
    Deny from all
</FilesMatch>

# Allow files with specifically approved file extensions
<FilesMatch "^.*\.(css|html?|js|pdf|txt|xml|xsl|gif|ico|jpe?g|png)$">
    Order Deny,Allow
    Allow from all
</FilesMatch>

# Limit HTTP Methods
<LimitExcept GET POST OPTIONS>
    deny from all
</LimitExcept>

# Disable Traces
TraceEnable off

# Restrict to HTTP/1.1
RewriteEngine On
RewriteCond %{THE_REQUEST} !HTTP/1\.1$
RewriteRule .* - [F]

# Disable access by IP instead of hostname
RewriteCond %{HTTP_HOST} !^www\.example\.com [NC]
RewriteCond %{REQUEST_URI} !^/error [NC]
RewriteRule ^.(.*) - [L,F]

# Listen on only needed network card
Listen 10.1.2.3:80

# Restrict browser frame options
Header always append X-Frame-Options SAMEORIGIN

# Configure the Error Log
LogLevel notice
ErrorLog "logs/error_log"

# Configure the Access Log
LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-agent}i\"" combined
CustomLog log/access_log combined

# Limit Information in the Server Token
ServerTokens Prod

# Limit Information in the Server Signature
ServerSignature Off

# Limit Information Leakage via Default Apache Content
#Alias /icons/ "/var/www/icons/"
#<Directory "/var/www/icons">
#     Options Indexes MultiViews FollowSymLinks
#     AllowOverride None
#     Order allow,deny
#     Allow from all
#</Directory>

# Denial of Service Mitigations
Timeout 10
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 15

# Buffer Overflow Mitigations
LimitRequestLine 512
LimitRequestFields 100
LimitRequestFieldsize 1024
LimitRequestBody 102400
```
