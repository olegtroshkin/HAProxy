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
