## How to setup HAProxy on docker container.

### Create a Docker network:

```
sudo docker network create mynetwork
```

### Run Backend Containers:

```
sudo docker run -d --name backend1 --network mynetwork your_backend_image1
sudo docker run -d --name backend2 --network mynetwork your_backend_image2

#Replace your_backend_image1 and your_backend_image2 with the actual images of your backend services.
```

### Create an HAProxy Configuration File (haproxy.cfg):

```
# vim haproxy.cfg

global
  log /dev/log local0
  log /dev/log local1 notice

defaults
  log global
  mode http
  option httplog
  option dontlognull
  timeout connect 5000
  timeout client 50000
  timeout server 50000

frontend main
  bind *:80
  default_backend backend

backend backend
  balance roundrobin
  server backend1 backend1:80 check
  server backend2 backend2:80 check
```

### Run HAProxy Container:

```
sudo docker run -d --name haproxy \
  --network mynetwork \
  -p 80:80 \
  -v /path/to/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg:ro \
  haproxy:latest
```


### Test

```
http://localhost
```

