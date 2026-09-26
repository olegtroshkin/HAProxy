# HAProxy
задание 1

global
    log /dev/log local0
    log /dev/log local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

defaults
    log     global
    mode    tcp
    option  tcplog
    option  dontlognull
    timeout connect 5000
    timeout client  50000
    timeout server  50000
listen stats
    bind :888
    mode http
    stats enable
    stats uri /stats
    stats refresh 5s
    stats realm Haproxy\ Statistics

# Балансировка на 4 уровне (TCP)
frontend example
    mode tcp
    bind :1325
    default_backend web_servers

backend web_servers
    mode tcp
    balance roundrobin
    option tcp-check
    server s1 127.0.0.1:8888 check inter 3s
    server s2 127.0.0.1:9999 check inter 3s
<img width="744" height="196" alt="image" src="https://github.com/user-attachments/assets/3a20acd1-0b3f-4bd4-80d9-8a7b378df4d7" />
задание 2
global
    log /dev/log local0
    log /dev/log local1 notice
    chroot /var/lib/haproxy
    stats socket /run/haproxy/admin.sock mode 660 level admin expose-fd listeners
    stats timeout 30s
    user haproxy
    group haproxy
    daemon

defaults
    log     global
    mode    http
    option  httplog
    option  dontlognull
    timeout connect 5000
    timeout client  50000
    timeout server  50000

# Страница статистики (http://<IP_ВМ>:888/stats)
listen stats
    bind :888
    mode http
    stats enable
    stats uri /stats
    stats refresh 5s
    stats realm Haproxy\ Statistics

# Балансировка на 7 уровне (HTTP)
frontend example
    mode http
    bind :8088
    # Трафик только для домена example.local (с портом и без)
    acl ACL_example.local hdr(host) -i example.local example.local:8088
    use_backend web_servers if ACL_example.local
    # default_backend не задан: запросы без example.local получат 503

backend web_servers
    mode http
    balance roundrobin
    option httpchk
    http-check send meth GET uri /index.html
    server s1 127.0.0.1:8888 weight 2 check
    server s2 127.0.0.1:9999 weight 3 check
    server s3 127.0.0.1:7777 weight 4 check
    <img width="938" height="321" alt="image" src="https://github.com/user-attachments/assets/1e0991ed-087c-4e47-9f19-ba838b62cd70" />

