APACHE
======

Securing Apache
---------------

1. Remove Server Version Banner

    ServerTokens Prod # change Header to production only, i.e. Apache
    ServerSignature Off # remove the version information from the page generated like 403, 404, 502

2. Disable directory browser listing

    Options None
    # or
    Options -Indexes

3. Disable .htaccess

    AllowOverride None

4. Limit HTTP Requests Method

    <LimitExcept GET POST HEAD>
    deny from all
    </LimitExcept>

5. Disable Trace HTTP Request

    TraceEnable off

6. Set cookie with HttpOnly and Secure flag

    mod_headers.so
    Header edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure

7. Prevent Clickjacking Attack

    mod_headers.so
    Header always append X-Frame-Options SAMEORIGIN

8. Disable SSI

    Options –Indexes -Includes

9. X-XSS Protection

    Header set X-XSS-Protection “1; mode=block”

10. Disable HTTP 1.0 Protocol

    mod_rewrite.so
    RewriteEngine On
    RewriteCond %{THE_REQUEST} !HTTP/1\.1$
    RewriteRule .* - [F]

11. Set Timeout

    Timeout 60

12. Disable SSL v2

    SSLProtocol –ALL +SSLv3 +TLSv1

13. Use ModSecurity

http://www.modsecurity.org/

14. Set Listen with absolute IP and port

    Listen 10.10.10.1:80

15. Add time and session ID to Access Logging

    LogFormat "%h %l %u %t \"%{sessionID}C\" \"%r\" %>s %b %T" common

16. Disable unwanted modules

    mod_dav
    mod_dav_fs
    mod_info
