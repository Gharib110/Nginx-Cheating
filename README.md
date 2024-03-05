# Nginx-Cheating
Nginx Cheats !

## The Base Configurations When You Install Nginx
- Use nginx.org packages or install it from source by compiling it !
```bash
apt install curl gnupg2 ca-certificates lsb-release ubuntu-keyring

curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
    | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null

gpg --dry-run --quiet --no-keyring --import --import-options import-show /usr/share/keyrings/nginx-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/ubuntu `lsb_release -cs` nginx" \
    | sudo tee /etc/apt/sources.list.d/nginx.list

echo -e "Package: *\nPin: origin nginx.org\nPin: release o=nginx\nPin-Priority: 900\n" \
    | sudo tee /etc/apt/preferences.d/99nginx

apt-get update

apt install nginx-full
```
- Other configurations in Confs directory !
- Try to inspect each line of `` nginx.conf `` file to get familiar yourself with it, then take look at Confs directory
```bash
user www-data;
worker_processes auto; # Number of process that is set to auto which is based on the number of the CPUs of your server
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;
load_module module/ngx_mp4_module.so; # mp4 module if you need it
load_module module/ngx_http_image.so; # image module if you need it

events {
        worker_connections 768; # The number of your Worker connections
        # multi_accept on;
}

http {

        ##
        # Basic Settings
        ##

        sendfile on;
        tcp_nopush off;
        types_hash_max_size 2048;
        server_tokens on; # Hide the version of Nginx that you use !

        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        ##
        # SSL Settings
        ##

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;

        ##
        # Logging Settings
        ##

        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;


        ##
        # FastCgi_Cache

        fastcgi_cache_path /tmp/nginx_cache levels=1:2 keys_zone=MYCACHE:2048m inactive=60m;
        fastcgi_cache_key "$scheme$request_method$request_uri";
      
        ##
        # Gzip Settings
        ##

        gzip on;

        # gzip_vary on;
        # gzip_proxied any;
        gzip_comp_level 4;
        # gzip_buffers 16 8k;
        # gzip_http_version 1.1;
        gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

        ##
        # Virtual Host Configs
        ##

        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
}


#mail {
#       # See sample authentication script at:
#       # http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#
#       # auth_http localhost/auth.php;
#       # pop3_capabilities "TOP" "USER";
#       # imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#       server {
#               listen     localhost:110;
#               protocol   pop3;
#               proxy      on;
#       }
#
#       server {
#               listen     localhost:143;
#               protocol   imap;
#               proxy      on;
#       }
#}
```
