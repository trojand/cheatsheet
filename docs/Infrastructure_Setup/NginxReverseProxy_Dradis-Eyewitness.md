# Nginx Reverse Proxy to web applications and directories


This is used for quickly setting up Dradis and sharing Eyewitness results for team collaboration purposes via Nginx reverse proxy 

Intructions are originally based on Wazuh - Kibana - Nginx guide[^1]

## Technologies 

* Dradis
* Eyewitness



## Script

Make sure to change:

* `<Directory_of_eyewitness>`
* `<user>`

```nginx
sudo apt install dradis nginx apache2-utils -y


sudo rm /etc/nginx/sites-available/default
sudo rm /etc/nginx/sites-enabled/default


sudo mkdir -p /etc/pki/tls/certs /etc/pki/tls/private
sudo openssl req -x509 -nodes -days 365 -newkey \
rsa:2048 -keyout /etc/pki/tls/private/dradis-access.key \
-out /etc/pki/tls/certs/dradis-access.pem


sudo cat > /etc/nginx/conf.d/default.conf <<EOF
server {
listen 8443 http2;
ssl on;
ssl_certificate /etc/pki/tls/certs/dradis-access.pem;
ssl_certificate_key /etc/pki/tls/private/dradis-access.key;
access_log /var/log/nginx/nginx.access.log;
error_log /var/log/nginx/nginx.error.log;

location / {
auth_basic "Restricted";
auth_basic_user_file /etc/nginx/conf.d/dradis.htpasswd;
proxy_pass http://localhost:3000/;
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-SSL-Client-Cert $ssl_client_cert;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_redirect off;
}
}

server {
listen 8444 http2;
gzip on;
ssl on;
ssl_certificate /etc/pki/tls/certs/dradis-access.pem;
ssl_certificate_key /etc/pki/tls/private/dradis-access.key;
access_log /var/log/nginx/nginx.access.log;
error_log /var/log/nginx/nginx.error.log;

location / {
auth_basic "Restricted";
auth_basic_user_file /etc/nginx/conf.d/dradis.htpasswd;
root <Directory_of_eyewitness i.e. /home/kali/Results/eyewitness>;
index report.html report_page2.html report_page3.html report_page4.html report_page5.html;
}
}
EOF


htpasswd -c /etc/nginx/conf.d/dradis.htpasswd <user>


sudo systemctl enable nginx
sudo systemctl restart nginx
sudo systemctl enable dradis
sudo dradis
```

[^1]: [Wazuh-Kibana-Nginx guide](https://documentation.wazuh.com/3.13/installation-guide/installing-elastic-stack/protect-installation/kibana_ssl.html#nginx-ssl-proxy-for-kibana-debian-based-distributions)