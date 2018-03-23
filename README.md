
# haproxy-emq

```
global
   log 127.0.0.1 local0 info   
   maxconn 256   
   #ssl fragmentation
   tune.ssl.maxrecord 1420

defaults
   log global
   option dontlognull
   option forwardfor

frontend emqtt-tcp
  mode tcp

  bind *:1883

  option tcplog
  option dontlognull   
  option clitcpka # For TCP keep-alive

  timeout connect 20000ms
  timeout client 3h
  timeout server 3h

  default_backend emqtt

frontend emqtt-ssl
   mode tcp

   bind *:8883 ssl crt /etc/ssl/private/haproxy.pem ca-file /etc/ssl/private/ca.crt verify optional crt-ignore-err all

   option tcplog
   option dontlognull   
   option clitcpka # For TCP keep-alive

   timeout connect 20000ms
   timeout client 3h
   timeout server 3h

   default_backend emqtt

backend emqtt
  mode tcp

  balance source
  server emqttd emqtt:1883 check  

  timeout connect 20000ms
  timeout client 3h
  timeout server 3h
```
