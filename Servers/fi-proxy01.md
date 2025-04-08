Datacenter: [[Hetzner]]
Type: `service host`
Backups: `no`
Reason for provider choice (when compared to others and why cannot be self-hosted): price + static ip + proximity

This server forwards all the traffic from port 80 and 443 over Tailscale to the intranet web server. The intranet web server handles all the configuration etc.
## Services

## pDNS slave


### Nginx

```conf
stream {
        server {
                listen 80;
                #TCP traffic will be forwarded to the specified server
                proxy_pass 100.94.18.46:80;
        }

        server {
                listen 443;
                #TCP traffic will be forwarded to the specified server
                proxy_pass 100.94.18.46:443;
        }
}
```
