# Linux Apache

## Client Cert Authorization

Virtual Host Example

    <IfModule mod_ssl.c>
        <VirtualHost *:443>
            ...
            SSLCACertificateFile /etc/ssl/certs/MyCA.pem

            <Location /admin>
                SSLVerifyClient     require
                SSLOptions           +FakeBasicAuth +ExportCertData +StdEnvVars
                SSLRequireSSL
                AuthName             "Restricted Area"
                AuthType             Basic
                AuthUserFile         /etc/ssl/web-users/httpd.passwd.admin
                require              valid-user
                # SSLRequire           ( %{SSL_CLIENT_I_DN} eq "CN=My,DC=ca,DC=com" )
                SSLRequire %{SSL_CIPHER_USEKEYSIZE} >= 128
            </Location>

        </VirtualHost>
    </IfModule>

httpd.passwd.admin Example

    /C=CZ/ST=Czech Republic/L=Prague/O=MyCompany/OU=MyOranizationUnit/CN=MyCertCN:xxj31ZMTZzkVA
    /C=CZ/...

* **xxj31ZMTZzkVA** constant is documented in [Apache SSL documentation](https://httpd.apache.org/docs/2.4/mod/mod_ssl.html#ssloptions)

## Settings

Main conf

    ServerTokens Prod
    EnableMMAP off
    SecServerSignature "My"
    ...

SSL

    SSLInsecureRenegotiation off
    SSLProtocol -all +TLSv1.2 +TLSv1.3
    SSLCipherSuite ...
    SSLHonorCipherOrder On
    ...
    SSLCARevocationCheck chain no_crl_for_cert_ok
    SSLCARevocationFile "/etc/apache2/certs/CRL.pem"

Logging

    LogFormat "%h:%{remote}p %l %u %t \"%r\" %>s %O %I %D \"%{Referer}i\" \"%{User-Agent}i\" %{version}c %{cipher}c \"%{errcode}c\" \"%{errstr}c\"" combined

Headers

    <IfModule headers_module>

        ### Add source hos and port
        RewriteEngine On
        RewriteRule .* - [E=REMOTE_PORT:%{REMOTE_PORT},NE]
        RequestHeader set X-Remote-Port "%{REMOTE_PORT}e" env=REMOTE_PORT
        RequestHeader set X-Remote-Host "%{REMOTE_ADDR}s"

        ### Propagate information about SSL CIPHER SUITE used for secure connection with request
        RewriteRule .* - [E=SSL_CIPHER:%{SSL_CIPHER},NE]
        RequestHeader set X-SSL-CIPHER "%{SSL_CIPHER}s" early

        ### HSTS protection and Cache Control
        Header always set Strict-Transport-Security "max-age=31536000"
        Header set Cache-Control "max-age=0, no-cache, no-store, must-revalidate"
        Header set Pragma "no-cache"

        ### Cross Site Scripting (XSS) and Clickjacking protection
        Header edit Set-Cookie ^(.*)$ $1;HttpOnly;Secure
        Header always append X-Frame-Options SAMEORIGIN
        Header set X-XSS-Protection "1; mode=block"
        Header set X-Content-Type-Options: "nosniff"
        <If "req('Host') !~ /.*EXCEPTION1*/ && req('Host') !~ /.*EXCEPTION2*/">
            Header set Content-Security-Policy "frame-ancestors 'self'"
        </If>

        # Remove Server / X-Powered-By
        Header unset X-Powered-By
        Header always unset X-Powered-By

    </IfModule>

## Log All Headers

    a2enmod log_forensic

To global config

    <IfModule log_forensic_module>
        ForensicLog ${APACHE_LOG_DIR}/forensic.log
    </IfModule>

## Log ProxyPass Client IP

    a2enmod remoteip

To global config

    RemoteIPHeader X-Forwarded-For
    RemoteIPInternalProxy LOCAL_PROXY_IP

* Without **RemoteIPInternalProxy** set, LOCAL_PROXY_IP will appear in logs instead of **local** LAN client IP. External client will appear in logs OK.
