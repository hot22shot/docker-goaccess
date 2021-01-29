# Changes 
* Bump to v1.4.4.
* Basic authentification in the embedded nginx with .htpasswd, if needed you can put your own .htpasswd file by mounting it at runtime in the docker command :  
*For docker-compose*
```
    volume:
      - <local path to htpasswd>\.htpasswd:\opt\.htpasswd
```

*For docker*
```
-v <local path to htpasswd>\.htpasswd:\opt\.htpasswd
```
* Localisation forced to fr_FR.UTF8

# docker-goaccess
This is an Alpine linux container which builds GoAccess including GeoIP.  It reverse proxies the GoAccess HTML files and websockets through nginx, allowing GoAccess content to be viewed without any other setup.

# Usage
## Example docker run
```
docker run --name goaccess -p 7889:7889 -v /path/to/host/nginx/logs:/opt/log -v /path/to/goaccess/storage:/config -d gregyankovoy/goaccess
```

## Volume Mounts
- /config
  - Used to store configuration and GoAccess generated files
- /opt/log
  - Map to nginx log directory

## Variables
- PUID 
  - User Id of user to run nginx & GoAccess
- PGID 
  - User Group to run nginx & GoAccess

## Files
- /config/goaccess.conf
  - GoAccess config file (populated with default config unless modified)
- /config/html
  - GoAccess generated static HTML

## Reverse Proxy
### nginx
```
location ^~ /goaccess {
    resolver 127.0.0.11 valid=30s;
    set $upstream_goaccess goaccess;
    proxy_pass http://$upstream_goaccess:7889/;

    proxy_connect_timeout 1d;
    proxy_send_timeout 1d;
    proxy_read_timeout 1d;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "Upgrade";
}
```
