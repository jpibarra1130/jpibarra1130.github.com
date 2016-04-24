---
layout: post
title: "Hardening NGINX SSL Config on the Cloud Server"
description: "Hardeining the NGINX SSL configuration on my VPS/cloud server"
tags: ["nginx", "https", "ssl", "poodle", "sslchecker", "rapidssl", "ssl certificate"]
---

I tried running [thepinknotes.com](https://thepinknotes.com) in [Qualys SSL Labs SSL Server Test](https://www.ssllabs.com/ssltest/analyze.html) and found out that there were a couple of issues on my SSL configurations. I was only supposed to take a look but one thing led to another and decided to try and increase my score to `A`.

Here are the things that I changed in my configurations in order for me to accomplish that goal:

1. **Corrected the SSL certificate order**

    I bought a COMODO certificate from [namecheap.com](https://namecheap.com) a couple of months ago and tried to set it up. I didn't realize that I concatenated the certificates wrongly (noob). To fix this, I recreated my certificate by concatenating it in the right order.

    `cat your_site_com.crt COMODORSADomainValidationSecureServerCA.crt COMODORSAAddTrustCA.crt AddTrustExternalCARoot.crt > final.crt`

    I uploaded this new certificate in my server and restarted NGINX.

2. **Enabling ssl-session-cache**

    This came up in the analysis report from ssl labs.

        Session resumption (caching) No (IDs assigned but not accepted)

    This means that for every request a user makes, the SSL handshake will need to be re-established. This can be pretty costly. This can be reduced by caching the session.

    To enable it, you simply need to add this in `/etc/nginx/nginx.conf`:

    `ssl_session_cache shared:ssl_session_cache:10m;`

    This means that NGINX will share the session cache between workers. This will be stored for 10 minutes. For more information, you can check out this [link](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_session_cache).

    For further reading:

      - [When shoud I use ssl_session_cache paramter in nginx ssl settings?](http://serverfault.com/questions/695258/when-shoud-i-use-ssl-session-cache-paramter-in-nginx-ssl-settings)


3. **Guarding against POODLE attack**

    [POODLE attack](https://en.wikipedia.org/wiki/POODLE) exploits SSLv3 to reveal encrypted data. This is fairly easy to guard against. In NGINX, you need to add this line in `/etc/nginx/nginx.conf`'s `http` block.

    `ssl_protocols TLSv1 TLSv1.1 TLSv1.2;`

    This will only enable the specified [SSL protocols](http://nginx.org/en/docs/http/ngx_http_ssl_module.html#ssl_protocols).

4. **Strengthening the DH Key Exchange**

    I saw this comment in my analysis report:

        This server supports weak Diffie-Hellman (DH) key exchange parameters.

    NGINX uses the default key given by OpenSSL as input to [Diffie-Hellman Key Exchange](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange#General_overview).

    This default key is using `1024` numbits. To increase the strength of this key, I generated a new one using `4096` bits. If you don't specify `numbits` it will default to `2048` bits.

    ```
    $ cd /etc/ssl/private/
    $ openssl dhparam -out dhparam.pem 4096
    ```

    This will generate `dhparam.crt`

    I used this new dhparam and configured it in NGINX by adding this in `/etc/nginx/nginx.conf`'s `http` block.

    `ssl_dhparam /etc/ssl/private/dhparams.pem`

5. **HSTS**

    Because my website needs only to communicate in HTTPS, I have enabled [HSTS](https://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security).

    To do this, I added this in the `server` block for [thepinknotes.com](https://thepinknotes.com) in my NGINX config.

    `add_header Strict-Transport-Security max-age=15768000;`

6. **Cipher configuration**

    I have also modified my cipher configuration for stricter rules in regards to the cipher used. I added this in `/etc/nginx/nginx.conf`'s `http` block.

    `ssl_ciphers "EECDH+AESGCM:EDH+AESGCM:ECDHE-RSA-AES128-GCM-SHA256:AES256+EECDH:DHE-RSA-AES128-GCM-SHA256:AES256+EDH:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-SHA384:ECDHE-RSA-AES128-SHA256:ECDHE-RSA-AES256-SHA:ECDHE-RSA-AES128-SHA:DHE-RSA-AES256-SHA256:DHE-RSA-AES128-SHA256:DHE-RSA-AES256-SHA:DHE-RSA-AES128-SHA:ECDHE-RSA-DES-CBC3-SHA:EDH-RSA-DES-CBC3-SHA:AES256-GCM-SHA384:AES128-GCM-SHA256:AES256-SHA256:AES128-SHA256:AES256-SHA:AES128-SHA:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!MD5:!PSK:!RC4";`

    I have also set the configuration to prefer using the server cipher over the client's. To configure this, I have added this in the `http` block.

    `ssl_prefer_server_ciphers on;`

After doing these changes, I managed to get an `A+` on my SSL analysis. That's been a fun Saturday fiddling about my SSL configuration and reading up on some of these concepts. The final outcome :-)

![SSL Analysis Report](/assets/images/2016-04-24-ssl-rating.png)

If you want to read more, I suggest you read this [blog](https://raymii.org/s/tutorials/Strong_SSL_Security_On_nginx.html). It has more detailed information on some of the vulnerabilities.
