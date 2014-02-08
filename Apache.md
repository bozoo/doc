APACHE
======

Securing Apache
---------------

Remove Server Version Banner:

```apacheconf
ServerTokens Prod # change Header to production only, i.e. Apache
ServerSignature Off # remove the version information from the page generated like 403, 404, 502
```

Disable directory browser listing:

```apacheconf
Options None
# or
Options -Indexes
```

Disable .htaccess:

```apacheconf
AllowOverride None
```

Limit HTTP Requests Method:

```apacheconf
<LimitExcept GET POST HEAD>
    deny from all
</LimitExcept>
```

Disable Trace HTTP Request:

```apacheconf
TraceEnable off
```

Set cookie with HttpOnly and Secure flag:

```apacheconf
mod_headers.so
Header edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure
```

Prevent Clickjacking Attack:

```apacheconf
mod_headers.so
Header always append X-Frame-Options SAMEORIGIN
```

Disable SSI:

```apacheconf
Options –Indexes -Includes
```

X-XSS Protection:

```apacheconf
Header set X-XSS-Protection “1; mode=block”
```

Disable HTTP 1.0 Protocol:

```apacheconf
mod_rewrite.so
RewriteEngine On
RewriteCond %{THE_REQUEST} !HTTP/1\.1$
RewriteRule .* - [F]
```

Set Timeout:

```apacheconf
Timeout 60
```

Disable SSL v2:

```apacheconf
SSLProtocol –ALL +SSLv3 +TLSv1
```

Use [ModSecurity](http://www.modsecurity.org/)

Set Listen with absolute IP and port:

```apacheconf
Listen 10.10.10.1:80
```

Add time and session ID to Access Logging:

```apacheconf
LogFormat "%h %l %u %t \"%{sessionID}C\" \"%r\" %>s %b %T" common
```

Disable unwanted modules:
 - mod_dav
 - mod_dav_fs
 - mod_info
