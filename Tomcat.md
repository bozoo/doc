TOMCAT
======

# Securing Tomcat 7 :

1. Delete all default applications (sample, manager app, ...)
2. Run under Security Manager (tomcat sandbox)
3. Disable shutdown port in server.xml (set it to "-1")
4. Disable context attributes crossContext and privileged attributes in server.xml
5. Disable allowLinking  in server.xml
6. Configure AccessLogValve in server.xml
7. Use LockOutRealm for authentification in server.xml
8. Set the org.apache.catalina.connector.RECYCLE_FACADES system property to true
9. Disable org.apache.catalina.connector.CoyoteAdapter.ALLOW_BACKSLASH and org.apache.tomcat.util.buf.UDecoder.ALLOW_ENCODED_SLASH system properties
