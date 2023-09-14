# DDOS test

## Description
This repo is a simple test of ddos attack. There are 3 services: web app, nginx as proxy server and ddos server.

Ddos server is able to init different type of attacks: HTTP flood, TCP SYN flood, UDP flood, TCP FIN Flood, TCP RST Flood
PUSH and ACK Flood, ICMP Flood.

Each of this attacks can be succesful, if we will not protect our web app.

- Protect from Http Flood. Try to limit request rate from one ip. In case of suspicious activity block connection
```
    limit_req zone=req_limit_per_ip burst=10;
```
-- Protect from TCP SYN flood. You can limit connection timeout and request rate. YOu also can limit header timeout. However for this type of attack it is better to use Firewall or other network security tool.
```
    proxy_connect_timeout 1;
```
```
    client_header_timeout 10;
```
```
    limit_conn_zone $binary_remote_addr zone=conn_limit_per_ip:10m;
    limit_conn conn_limit_per_ip 10;
```
-- Protect from UDP flood. There it is the best to use firewall or service like Cloudflare. But you also can add NGINX protection as rate limiting. As an option, we can drop invalid requests
```
    if ($request_method !~ ^(GET|HEAD|POST)$ ) {
        return 444;
    }
```
-- TCP FIN Flood. To prevent this attack you can use connection rate limit
```
    limit_conn_zone $binary_remote_addr zone=conn_limit_per_ip:10m;
    limit_conn conn_limit_per_ip 10;
```


# Testing
nginx.conf file contains configuration that can be used to prevent trafic from overwealming our web app. Configuration basically limits connection time, request rate and eliminates prohibited requests.
you can run docker-compose and in ddos section uncomment type of attack you would like to perform.